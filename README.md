## File Sharing App (文件共享工具）

这个ASP.NET Core的应用程序源码来自MS Learn课程中使用的实验项目。网站需要部署到Azure App Service中，并使用Azure Storage存储上传的文件。

**网站截图**:  

![网站截图](media/FileSharingApp.PNG)

执行以下配置步骤，将网站部署到自己的Azure订阅中。

1. 登录Azure Portal。
1. 打开Cloud Shell，选择Cloud Shell - Bash。
1. 下载源码。
    ```
    git clone https://github.com/7788wangzi/FileSharingApp.git
    ```
1. 切换到FileSharingApp/src目录。
    ```
    cd FileSharingApp/src
    ```
 1. 添加引用的包WindowsAzure.Storeage,并使用命令`dotnet restore`获取最新版本。
    ```
    dotnet add package WindowsAzure.Storage
    dotnet restore
    ```
1. 创建资源组，并记录该资源组的名字。
    ```
    az group create --name <resource group name> --location eastasia
    ```    
    **注意：** 将&lt;resource group name&gt;替换为自己命名的资源组。
1. 创建**storage account**，使用刚刚创建的资源组:
    ```
    az storage account create \
    --kind StorageV2 \
    --resource-group <resource group name> \
    --location eastasia \
    --name <storage account name>
    ```
    **注意：** 将&lt;resource group name&gt;替换为自己命名的资源组, 将&lt;storage account name&gt;替换为自己命名的Storage Account。Storage Account的名字，全球唯一。
1. 创建**App Service**, 使用刚刚创建的资源组：
    ```
    az appservice plan create --name <app service plan> --resource-group <resource group name>
    az webapp create --name <web app name> --plan <app service plan> --resource-group <resource group name>
    ```
    **注意：** 将&lt;app service plan&gt;替换为自己命名的宿主计划，将&lt;resource group name&gt;替换为自己命名的资源组。将&lt;web app name&gt;替换为自己命名的应用程序名字。
1. 导出Storage Account的Connection String并配置到App Service中，注意替换为你自己的Storage Account和App Service的名字。
    ```
    CONNECTIONSTRING=$(az storage account show-connection-string --name <storage account name> --output tsv)
    az webapp config appsettings set --name <web app name> --resource-group <resource group name> --settings AzureStorageConfig:ConnectionString=$CONNECTIONSTRING AzureStorageConfig:FileContainerName=files
    ```
1. 发布网站，部署到Azure中，注意替换为你自己的资源组名字和应用程序名字：
    ```
    dotnet publish -o pub
    cd pub
    zip -r ../site.zip *
    az webapp deployment source config-zip --src ../site.zip --name <web app name> --resource-group <resource group name>
    ```
 1. 在网站中打开刚刚发布的网站，验证文件上传，获取文件列表和下载文件的功能。
    [https://&lt;web app name&gt;.azurewebsites.net/]()
