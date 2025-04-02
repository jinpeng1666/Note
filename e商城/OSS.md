version：5e7546c33e8a5683a7e865291622916a90db4c1e

本文主要讲解OSS相关概念、如何实现文件上传的过程，包括两个实战案例。



# 介绍

> [!TIP]
>
> emall使用的是阿里云对象存储服务，详情可以查看官方的文档：[什么是对象存储OSS_对象存储(OSS)-阿里云帮助中心](https://help.aliyun.com/zh/oss/product-overview/what-is-oss?spm=a2c4g.11174283.0.0.30337368hfc44l)

阿里云对象存储OSS（Object Storage Service，简称 OSS）是阿里云提供的海量、安全、低成本、高可靠的云存储服务。OSS可用于图片、音视频、日志等海量文件的存储。各种终端设备、Web网站程序、移动应用可以直接向OSS写入或读取数据。



**工作原理**

数据以对象（Object）的形式存储在OSS的存储空间（Bucket ）中。如果要使用OSS存储数据，您需要先创建Bucket，并指定Bucket的地域、访问权限、存储类型等属性。创建Bucket后，您可以将数据以Object的形式上传到Bucket，并指定Object的文件名（Key）作为其唯一标识。

OSS以HTTP RESTful API的形式对外提供服务，访问不同地域需要不同的访问域名（Endpoint）。当您请求访问OSS时，OSS通过使用访问密钥（AccessKey ID和AccessKey Secret）对称加密的方法来验证某个请求的发送者身份。



**相关概念**

- Endpoint：访问域名，通过该域名可以访问OSS服务的API，进行文件上传、下载等操作。
- Bucket：存储空间，是存储对象的容器，所有存储对象都必须隶属于某个存储空间。
- Object：对象，对象是 OSS 存储数据的基本单元，也被称为 OSS 的文件。
- AccessKey：访问密钥，指的是访问身份验证中用到的 AccessKeyId 和 AccessKeySecret。



# 快速入门

> [!TIP]
>
> 详情请查询官方文档：[OSS Java SDK快速入门_对象存储(OSS)-阿里云帮助中心](https://help.aliyun.com/zh/oss/developer-reference/getting-started?spm=a2c4g.11186623.0.nextDoc.eccfb9192LW6Bi)

步骤大致如下：

1. 开通OSS服务
2. 创建存储空间
3. 跨域资源共享（CORS）的设置
4. 代码实现



# 客户端直传

> [!TIP]
>
> 详情请查询官方文档：[在客户端直接上传文件到OSS_对象存储(OSS)-阿里云帮助中心](https://help.aliyun.com/zh/oss/use-cases/uploading-objects-to-oss-directly-from-clients/?spm=a2c4g.11186623.help-menu-31815.d_6_1.12656776VHyJBP)

在典型的服务端和客户端架构下，常见的文件上传方式是服务端代理上传：客户端将文件上传到业务服务器，然后业务服务器将文件上传到OSS。在这个过程中，一份数据需要在网络上传输两次，会造成网络资源的浪费，增加服务端的资源开销。为了解决这一问题，您可以在客户端直连OSS来完成文件上传，无需经过业务服务器中转。

![image-20250401104609393](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250401104609393.png)



# 案例

### emall实现OSS（客户端直传）

#### 后端

**流程图**

![image-20250401105242960](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250401105242960.png)



##### 依赖引入

在`emall-third`的pom.xml文件引入依赖

```xml
<!--oss-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alicloud-oss</artifactId>
    <version>2.2.0.RELEASE</version>
</dependency>
```



##### Controller

```java
package com.easyshopping.emallthird.controller;

import com.aliyun.oss.OSS;
import com.aliyun.oss.common.utils.BinaryUtil;
import com.aliyun.oss.model.MatchMode;
import com.aliyun.oss.model.PolicyConditions;
import com.easyshopping.common.utils.R;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.LinkedHashMap;
import java.util.Map;

@RestController
public class OssController {

    @Autowired
    private OSS ossClient;

    @Value("${spring.cloud.alicloud.oss.endpoint}")
    private String endpoint;

    @Value("${spring.cloud.alicloud.oss.bucket}")
    private String bucket;

    @Value("${spring.cloud.alicloud.access-key}")
    private String accessId;

    /**
     * 前端在直传文件到阿里云 OSS 之前，先获取签名和相关参数，以便安全地上传文件
     * @return 上传签名
     */
    @RequestMapping("/oss/policy")
    public R policy() {

        String host = "https://" + bucket + "." + endpoint;

        // 生成上传目录
        String format = new SimpleDateFormat("yyyy-MM-dd").format(new Date());
        String dir = format + "/"; // 用户上传文件时指定的前缀

        Map<String, String> respMap = null;
        try {
            long expireTime = 30;
            long expireEndTime = System.currentTimeMillis() + expireTime * 1000;
            Date expiration = new Date(expireEndTime);

            // 设置上传策略
            PolicyConditions policyConds = new PolicyConditions();
            policyConds.addConditionItem(PolicyConditions.COND_CONTENT_LENGTH_RANGE, 0, 1048576000);
            policyConds.addConditionItem(MatchMode.StartWith, PolicyConditions.COND_KEY, dir);

            // 生成签名
            String postPolicy = ossClient.generatePostPolicy(expiration, policyConds);
            byte[] binaryData = postPolicy.getBytes("utf-8");
            String encodedPolicy = BinaryUtil.toBase64String(binaryData);
            String postSignature = ossClient.calculatePostSignature(postPolicy);

            // 返回信息
            respMap = new LinkedHashMap<String, String>();
            respMap.put("accessid", accessId);
            respMap.put("policy", encodedPolicy);
            respMap.put("signature", postSignature);
            respMap.put("dir", dir);
            respMap.put("host", host);
            respMap.put("expire", String.valueOf(expireTime / 1000));
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }

        return R.ok().put("data", respMap);
    }
}
```



##### bootstrap.yaml

添加如下内容

```yaml
spring:
  cloud:
    alicloud:
      access-key: ......
      secret-key: ......
      oss:
        endpoint: oss-cn-shenzhen.aliyuncs.com
        bucket: emall-zhou # 自定义属性
```



#### 前端

流程：用户上传图片 → 图片传到OSS → 获取OSS返回的URL → 通过v-model机制将URL传回父组件 → 父组件保存URL到dataForm → 表单提交时将URL保存到数据库



##### policy.js

用来发送请求获取post签名

```javascript
import http from '@/utils/httpRequest.js'
export function policy () {
  return new Promise((resolve, reject) => {
    http({
      url: http.adornUrl('/third/oss/policy'),
      method: 'get',
      params: http.adornParams({})
    }).then(({ data }) => {
      resolve(data)
    })
  })
}

```



##### singleUpload.vue

![image-20250401111239452](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250401111239452.png)

**代码**

```vue
<template>
  <div>
    <el-upload
      action="http://emall-zhou.oss-cn-shenzhen.aliyuncs.com"
      :data="dataObj"
      list-type="picture"
      :multiple="false"
      :show-file-list="showFileList"
      :file-list="fileList"
      :before-upload="beforeUpload"
      :on-remove="handleRemove"
      :on-success="handleUploadSuccess"
      :on-preview="handlePreview"
    >
      <el-button size="small" type="primary">点击上传</el-button>
      <div slot="tip" class="el-upload__tip">
        只能上传jpg/png文件，且不超过10MB
      </div>
    </el-upload>
    <el-dialog :visible.sync="dialogVisible">
      <img width="100%" :src="fileList[0].url" alt="" />
    </el-dialog>
  </div>
</template>
<script>
import { policy } from "./policy";
import { getUUID } from "@/utils";
export default {
  name: "singleUpload",
  props: {
    value: String,
  },
  computed: {
    imageUrl() {
      return this.value;
    },
    imageName() {
      if (this.value != null && this.value !== "") {
        return this.value.substr(this.value.lastIndexOf("/") + 1);
      } else {
        return null;
      }
    },
    fileList() {
      return [
        {
          name: this.imageName,
          url: this.imageUrl,
        },
      ];
    },
    showFileList: {
      get: function () {
        return (
          this.value !== null && this.value !== "" && this.value !== undefined
        );
      },
      set: function (newValue) {},
    },
  },
  data() {
    return {
      dataObj: {
        policy: "",
        signature: "",
        key: "",
        ossaccessKeyId: "",
        dir: "",
        host: "",
        // callback:'',
      },
      dialogVisible: false,
    };
  },
  methods: {
    emitInput(val) {
      this.$emit("input", val);
    },
    handleRemove(file, fileList) {
      this.emitInput("");
    },
    handlePreview(file) {
      this.dialogVisible = true;
    },
    beforeUpload(file) {
      let _self = this;
      return new Promise((resolve, reject) => {
        policy()
          .then((response) => {
            console.log("响应的数据", response);
            _self.dataObj.policy = response.data.policy;
            _self.dataObj.signature = response.data.signature;
            _self.dataObj.ossaccessKeyId = response.data.accessid;
            _self.dataObj.key = response.data.dir + getUUID() + "_${filename}";
            _self.dataObj.dir = response.data.dir;
            _self.dataObj.host = response.data.host;
            console.log("响应的数据222。。。", _self.dataObj);
            resolve(true);
          })
          .catch((err) => {
            reject(false);
          });
      });
    },
    handleUploadSuccess(res, file) {
      console.log("上传成功...");
      this.showFileList = true;
      this.fileList.pop();
      this.fileList.push({
        name: file.name,
        url:
          this.dataObj.host +
          "/" +
          this.dataObj.key.replace("${filename}", file.name),
      });
      this.emitInput(this.fileList[0].url);
    },
  },
};
</script>
<style>
</style>

```

- 用户点击上传，调用`policy.js`的方法，获取post签名，获取签名后向OSS上传文件
- el-upload的action值指定为OSS地址



##### brand-add-or-update.vue

![image-20250401113222684](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250401113222684.png)

**代码**

```vue
<template>
  <el-dialog
    :title="!dataForm.brandId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="140px"
    >
      <el-form-item label="品牌名" prop="name">
        <el-input v-model="dataForm.name" placeholder="品牌名"></el-input>
      </el-form-item>
      <el-form-item label="品牌logo地址" prop="logo">
        <SingleUpload v-model="dataForm.logo"></SingleUpload>
      </el-form-item>
      <el-form-item label="介绍" prop="descript">
        <el-input v-model="dataForm.descript" placeholder="介绍"></el-input>
      </el-form-item>
      <el-form-item label="显示状态" prop="showStatus">
        <el-switch
          v-model="dataForm.showStatus"
          active-color="#13ce66"
          inactive-color="#ff4949"
          :active-value="1"
          :inactive-value="0"
        >
        </el-switch>
      </el-form-item>
      <el-form-item label="检索首字母" prop="firstLetter">
        <el-input
          v-model="dataForm.firstLetter"
          placeholder="检索首字母"
        ></el-input>
      </el-form-item>
      <el-form-item label="排序" prop="sort">
        <el-input v-model.number="dataForm.sort" placeholder="排序"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import SingleUpload from "@/components/upload/singleUpload.vue";
export default {
  components: {
    SingleUpload
  },
  data() {
    return {
      visible: false,
      dataForm: {
        brandId: 0,
        name: "",
        logo: "",
        descript: "",
        showStatus: 1,
        firstLetter: "",
        sort: 0
      },
      dataRule: {
        name: [{ required: true, message: "品牌名不能为空", trigger: "blur" }],
        logo: [
          { required: true, message: "品牌logo地址不能为空", trigger: "blur" }
        ],
        descript: [
          { required: true, message: "介绍不能为空", trigger: "blur" }
        ],
        showStatus: [
          {
            required: true,
            message: "显示状态[0-不显示；1-显示]不能为空",
            trigger: "blur"
          }
        ],
        firstLetter: [
          {
            validator: (rule, value, callback) => {
              if (value == "") {
                callback(new Error("不能为空"));
              } else if (!/^[a-zA-Z]$/.test(value)) {
                callback(new Error("必须为一个字母"));
              } else {
                callback();
              }
            },
            trigger: "blur"
          }
        ],
        sort: [
          {
            validator: (rule, value, callback) => {
              if (value == "") {
                callback(new Error("不能为空"));
              } else if (!Number.isInteger(value) || value < 0) {
                callback(new Error("必须为正整数"));
              } else {
                callback();
              }
            },
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    init(id) {
      this.dataForm.brandId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.brandId) {
          this.$http({
            url: this.$http.adornUrl(
              `/product/brand/info/${this.dataForm.brandId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.name = data.brand.name;
              this.dataForm.logo = data.brand.logo;
              this.dataForm.descript = data.brand.descript;
              this.dataForm.showStatus = data.brand.showStatus;
              this.dataForm.firstLetter = data.brand.firstLetter;
              this.dataForm.sort = data.brand.sort;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/product/brand/${!this.dataForm.brandId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              brandId: this.dataForm.brandId || undefined,
              name: this.dataForm.name,
              logo: this.dataForm.logo,
              descript: this.dataForm.descript,
              showStatus: this.dataForm.showStatus,
              firstLetter: this.dataForm.firstLetter,
              sort: this.dataForm.sort
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    }
  }
};
</script>

```



##### 组件通信

**组件关系**

- `BrandAddOrUpdate` 是父组件，包含品牌表单
- `SingleUpload` 是子组件，专门处理图片上传到OSS的功能



**数据传递流程**

在 `BrandAddOrUpdate` 中：

```vue
<SingleUpload v-model="dataForm.logo"></SingleUpload>
```

是以下写法的简写

```vue
<SingleUpload :value="dataForm.logo" @input="val => dataForm.logo = val"></SingleUpload>
```



**上传过程**

1. 用户点击上传按钮：
    - 触发 `SingleUpload` 组件中的上传操作
2. 上传前准备：
    - `beforeUpload` 方法会先获取OSS上传策略(policy)
    - 这个方法返回一个Promise，确保在上传前获取到必要的认证信息
3. 实际上传到OSS：
    - 使用el-upload组件将文件上传到阿里云OSS
    - 上传地址是 `http://emall-zhou.oss-cn-shenzhen.aliyuncs.com`
4. 上传成功处理：
    - `handleUploadSuccess` 方法被调用
    - 构建完整的图片URL：`host + key`（其中key已经用UUID和文件名替换）
    - 更新fileList数组，只保留最新上传的图片



**数据回传**

在 `handleUploadSuccess` 方法中：

```javascript
this.emitInput(this.fileList[0].url);
```

`emitInput` 方法定义：

```javascript
emitInput(val) {
  this.$emit("input", val);
}
```

这会触发父组件的 `@input` 事件，将图片URL赋值给 `dataForm.logo`



### sky实现（服务器代理上传）

#### 引入依赖

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.10.2</version>
</dependency>
```



#### application.yaml

```yaml
sky:
  alioss:
    endpoint: ${sky.alioss.endpoint}
    access-key-id: ${sky.alioss.access-key-id}
    access-key-secret: ${sky.alioss.access-key-secret}
    bucket-name: ${sky.alioss.bucket-name}
```



#### AliOssProperties

设置一个Spring Boot 配置属性类，用于管理和配置阿里云对象存储服务(OSS)的相关参数

```java
package com.sky.properties;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "sky.alioss")
@Data
public class AliOssProperties {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

}

```



#### OssConfiguration

配置类，用于创建AliOssUtil对象

```java
package com.sky.config;

import com.sky.properties.AliOssProperties;
import com.sky.utils.AliOssUtil;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置类，用于创建AliOssUtil对象
 */
@Configuration
@Slf4j
public class OssConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public AliOssUtil aliOssUtil(AliOssProperties aliOssProperties) {
        log.info("开始创建阿里云文件上传工具类对象：{}", aliOssProperties);
        return new AliOssUtil(aliOssProperties.getEndpoint(),
                aliOssProperties.getAccessKeyId(),
                aliOssProperties.getAccessKeySecret(),
                aliOssProperties.getBucketName());
    }
}
```



#### Controler

```java
package com.sky.controller.admin;

import com.sky.constant.MessageConstant;
import com.sky.result.Result;
import com.sky.utils.AliOssUtil;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;
import java.io.IOException;
import java.util.UUID;

/**
 * 通用接口
 */
@RestController
@RequestMapping("/admin/common")
@Api(tags = "通用接口")
@Slf4j
public class CommonControler {

    @Autowired
    private AliOssUtil aliOssUtil;

    /**
     * 文件上传
     * @param file
     * @return
     */
    @PostMapping("/upload")
    @ApiOperation("文件上传")
    public Result<String> upload(MultipartFile file) {
        log.info("文件上传：{}", file);

        // ----------存储在本地----------
        // 获取原始文件后缀
        //String originalFilename = file.getOriginalFilename();
        //String suffix = originalFilename.substring(originalFilename.lastIndexOf("."));

        // 重新生成文件名，防止文件名称重复
        //String fileName = UUID.randomUUID().toString() + suffix;

        // 创建一个目录对象
        //File dir = new File(basePath);
        // 目录不存在，创建一个目录
        //if (!dir.exists()) {
        //    dir.mkdirs();
        //}

        //try {
        //    // 将文件临时转存到指定位置
        //    file.transferTo(new File(basePath + fileName));
        //} catch (IOException e) {
        //    e.printStackTrace();
        //}

        // ----------存储在阿里云OSS----------
        try {
            // 获取原始文件名
            String originalFilename = file.getOriginalFilename();

            // 获取文件拓展名
            String extension = originalFilename.substring(originalFilename.lastIndexOf("."));

            // 构造新文件名称
            String objectName = UUID.randomUUID().toString() + extension;

            // 文件的请求路径
            String filePath = aliOssUtil.upload(file.getBytes(), objectName);

            return  Result.success(filePath);
        } catch (IOException e) {
            log.error("文件上传失败：{}", e);
        }
        return Result.error(MessageConstant.UPLOAD_FAILED);
    }

}
```