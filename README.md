## FileSharingApp

这个ASP.NET Core的应用程序源码来自MS Learn课程中使用的实验项目。网站需要部署到Azure App Service中，并使用Azure Storage存储上传的文件。

**网站截图**:  

![网站截图](media/FileSharingApp.PNG)

执行以下配置步骤，将网站部署到自己的Azure订阅中。

1. 登录Azure Portal。
1. 打开Cloud Shell，选择Cloud Shell - Bash。
1. 下载源码。
    ```
    
    ```
1. 添加引用的包。
  ```
  dotnet add package WindowsAzure.Storage
  dotnet restore
  ```
1. 创建**storage account**，以Azure CLI为例:
  ```
  az storage account create \
  --kind StorageV2 \
  --resource-group Learn-16951c9a-b156-4ae2-8630-57241b4e1cd6 \
  --location eastasia \
  --name storage072406
  ```
  **NOTE:** storage072406是Storage Account的名字，全球唯一，需要替换为你自己的Storage Account的名字。
1. 创建**App Service**, 以Azure CLI为例：
  ```
  az appservice plan create --name blob-exercise-plan --resource-group Learn-16951c9a-b156-4ae2-8630-57241b4e1cd6
  az webapp create --name FileSharingApp --plan blob-exercise-plan --resource-group Learn-16951c9a-b156-4ae2-8630-57241b4e1cd6
  ```
  **NOTE:** FileSharingApp是App Service的名字，全球唯一，需要替换为你自己的App Service的名字。
1. 导出Storage Account的Connection String并配置到App Service中，注意替换为你自己的Storage Account和App Service的名字。
  ```
  CONNECTIONSTRING=$(az storage account show-connection-string --name storage072406 --output tsv)
  az webapp config appsettings set --name FileSharingApp --resource-group Learn-16951c9a-b156-4ae2-8630-57241b4e1cd6 --settings AzureStorageConfig:ConnectionString=$CONNECTIONSTRING AzureStorageConfig:FileContainerName=files
  ```
1. 发布网站，部署到Azure中，以Azure CLI为例：
  ```
  dotnet publish -o pub
  cd pub
  zip -r ../site.zip *
  az webapp deployment source config-zip --src ../site.zip --name FileSharingApp --resource-group Learn-16951c9a-b156-4ae2-8630-57241b4e1cd6
  ```
