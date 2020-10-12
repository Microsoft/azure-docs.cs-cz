---
title: Nasazení virtuálního počítače pomocí C# a šablony Správce prostředků
description: Naučte se používat C# a šablonu Správce prostředků k nasazení virtuálního počítače Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 07/14/2017
ms.author: cynthn
ms.custom: devx-track-csharp
ms.openlocfilehash: 779a09532790ea272d8c95ac28f8c152216efc5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002959"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Nasazení virtuálního počítače Azure pomocí C# a šablony Správce prostředků

V tomto článku se dozvíte, jak nasadit šablonu Azure Resource Manager pomocí jazyka C#. Šablona, kterou vytvoříte, nasadí jeden virtuální počítač se systémem Windows Server v nové virtuální síti s jednou podsítí.

Podrobný popis prostředku virtuálního počítače najdete v tématu [virtuální počítače v šabloně Azure Resource Manager](template-description.md). Další informace o všech prostředcích v šabloně najdete v tématu [Azure Resource Manager návodu](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)k vytvoření šablony.

Provedení těchto kroků trvá přibližně 10 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V tomto kroku se ujistěte, že je nainstalovaná aplikace Visual Studio a Vy vytvoříte konzolovou aplikaci, která se používá k nasazení šablony.

1. Pokud jste to ještě neudělali, nainstalujte [Visual Studio](/visualstudio/install/install-visual-studio). Na stránce úlohy vyberte **vývoj pro desktopy .NET** a pak klikněte na **nainstalovat**. V souhrnu vidíte, že se pro vás automaticky vybraly **.NET Framework nástroje pro vývoj 4-4,6** . Pokud jste již nainstalovali aplikaci Visual Studio, můžete přidat úlohu rozhraní .NET pomocí spouštěče sady Visual Studio.
2. V aplikaci Visual Studio klikněte na **soubor**  >  **Nový**  >  **projekt**.
3. V **šablonách**  >  **Visual C#** vyberte **Konzolová aplikace (.NET Framework)**, jako název projektu zadejte *myDotnetProject* , vyberte umístění projektu a pak klikněte na **OK**.

## <a name="install-the-packages"></a>Nainstalovat balíčky

Balíčky NuGet představují nejjednodušší způsob, jak nainstalovat knihovny, které potřebujete k dokončení těchto kroků. Chcete-li získat knihovny, které potřebujete v aplikaci Visual Studio, proveďte tyto kroky:

1. Klikněte na **nástroje**  >  **Správce balíčků NuGet**a pak klikněte na **Konzola správce balíčků**.
2. Do konzoly zadejte tyto příkazy:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Vytvoření souborů

V tomto kroku vytvoříte soubor šablony, který nasadí prostředky a soubor parametrů, který do šablony dodá hodnoty parametrů. Můžete také vytvořit autorizační soubor, který se používá k provádění operací Azure Resource Manager.

### <a name="create-the-template-file"></a>Vytvoření souboru šablony

1. V Průzkumník řešení klikněte pravým tlačítkem myši na *myDotnetProject*  >  **Přidat**  >  **novou položku**a pak vyberte **textový soubor** v *položkách jazyka Visual C#*. Pojmenujte soubor *CreateVMTemplate.jsv*a pak klikněte na **Přidat**.
2. Tento kód JSON přidejte do souboru, který jste vytvořili:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Uložte CreateVMTemplate.jsdo souboru.

### <a name="create-the-parameters-file"></a>Vytvoření souboru parametrů

Chcete-li zadat hodnoty parametrů prostředků v šabloně, vytvořte soubor parametrů, který obsahuje hodnoty.

1. V Průzkumník řešení klikněte pravým tlačítkem myši na *myDotnetProject*  >  **Přidat**  >  **novou položku**a pak vyberte **textový soubor** v *položkách jazyka Visual C#*. Pojmenujte soubor *Parameters.jsv*a pak klikněte na **Přidat**.
2. Tento kód JSON přidejte do souboru, který jste vytvořili:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Uložte Parameters.jsdo souboru.

### <a name="create-the-authorization-file"></a>Vytvoření autorizačního souboru

Než budete moct nasadit šablonu, ujistěte se, že máte přístup k [instančnímu objektu služby Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Z instančního objektu získáte token pro ověřování požadavků Azure Resource Manager. Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID tenanta, které v autorizačním souboru potřebujete.

1. V Průzkumník řešení klikněte pravým tlačítkem myši na *myDotnetProject*  >  **Přidat**  >  **novou položku**a pak vyberte **textový soubor** v *položkách jazyka Visual C#*. Pojmenujte soubor *azureauth. Properties*a pak klikněte na **Přidat**.
2. Přidejte tyto vlastnosti autorizace:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Nahraďte ** &lt; ID &gt; ** předplatného identifikátorem vašeho předplatného, ** &lt; ID &gt; aplikace** s identifikátorem aplikace služby Active ** &lt; &gt; ** Directory, klíčem pro ověřování a klíčovým klíčem aplikace a ** &lt; ID &gt; tenanta** s identifikátorem tenanta.

3. Uložte soubor azureauth. Properties.
4. Nastavte v systému Windows proměnnou prostředí s názvem AZURE_AUTH_LOCATION s úplnou cestou k vytvořenému autorizačnímu souboru, například můžete použít následující příkaz prostředí PowerShell:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Vytvořit klienta pro správu

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili. Pak přidejte tyto příkazy using do stávajících příkazů v horní části souboru:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Chcete-li vytvořit klienta pro správu, přidejte tento kód do metody Main:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Chcete-li zadat hodnoty pro aplikaci, přidejte kód do metody Main:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Šablonu a parametry se nasazují z účtu úložiště v Azure. V tomto kroku vytvoříte účet a nahrajete soubory. 

Chcete-li vytvořit účet, přidejte tento kód do metody Main:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu a parametry z účtu úložiště, který jste vytvořili. 

Chcete-li nasadit šablonu, přidejte tento kód do metody Main:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Odstranit prostředky

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nepotřebujete. Nemusíte odstraňovat jednotlivé prostředky odděleně od skupiny prostředků. Odstraní skupinu prostředků a všechny její prostředky se automaticky odstraní. 

Pokud chcete odstranit skupinu prostředků, přidejte tento kód do metody Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

Spuštění této konzolové aplikace z začátku do konce by mělo trvat přibližně pět minut. 

1. Chcete-li spustit konzolovou aplikaci, klikněte na tlačítko **Start**.

2. Než stisknete **ENTER** a začnete odstraňovat prostředky, může trvat několik minut, než se ověří vytváření prostředků v Azure Portal. Kliknutím na stav nasazení zobrazíte informace o nasazení.

## <a name="next-steps"></a>Další kroky

* V případě, že došlo k potížím s nasazením, je dalším krokem postup [při řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
* Zjistěte, jak nasadit virtuální počítač a jeho podpůrné prostředky pomocí tématu [nasazení virtuálního počítače Azure pomocí jazyka C#](../../azure-resource-manager/templates/deploy-rest.md).