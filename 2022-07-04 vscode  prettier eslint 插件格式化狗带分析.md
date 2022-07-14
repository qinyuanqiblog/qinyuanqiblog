# vscode prettier eslint 插件格式化不生效的问题

## 问题背景

> 公司项目采用的是prettier eslint插件来进行格式化，一直以来都用的好好的， 最近突然就狗带了，本来还怀疑是我的设置问题，各种百度谷歌，后来发现不是 😂😂😂

## 在官网的issue里面发现了答案！！！

[官网issue地址](https://github.com/idahogurl/vs-code-prettier-eslint/issues/41)

![官网issue地址](https://api2.mubu.com/v3/document_image/25ed02e1-86ca-49fb-9e05-ccb7d57cd90f-2331693.jpg)

## 问题原因：

> eslint 8升级了一些api，导致不兼容，然后当前这个插件也即时做了处理，只不过他们把最新版本的代码给当作主版本了，像我们这种eslint 8以下的项目，需要降级prettier eslint 版本就好了

### 分析报错信息，报错信息

```bash
r is not a constructor
```

![r is not a constructor](https://api2.mubu.com/v3/document_image/73caf62b-84d4-4d84-9067-5287621c1119-2331693.jpg)

## 解决办法：vscode 把插件版本回退

### 1. vscode插件栏，点击prettier eslint 插件的的小按钮

![step1](https://api2.mubu.com/v3/document_image/900e1061-38d6-475e-87fb-19c8143b7dfc-2331693.jpg)

### 2. 选择安装历史版本

![step2](https://api2.mubu.com/v3/document_image/30b3f1c6-345c-4cea-aaa4-92769ab44e48-2331693.jpg)

### 3. 安装3.1.0 版本， 然后重启vscode

![step3](https://api2.mubu.com/v3/document_image/92e413e2-ebfd-4f8d-9b33-e6cc3205caec-2331693.jpg)

### 4. 然后再回到之前搞乱的文件，执行prettier eslint 格式化， 完美，收摊！！

![step4](https://api2.mubu.com/v3/document_image/7e29b193-0424-4339-a732-137ac59fc83c-2331693.jpg)
