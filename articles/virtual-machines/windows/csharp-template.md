---
title: Nasazení virtuálního počítače pomocí jazyka C# a šablony Správce prostředků
description: Naučte se používat C# a šablonu Správce prostředků k nasazení virtuálního počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: f636af911b3c6eb7d40a5ce5052bf17ae5d10f01
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452192"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Nasazení virtuálního počítače Azure pomocí Jazyka C# a šablony Správce prostředků

Tento článek ukazuje, jak nasadit šablonu Azure Resource Manager pomocí C#. Šablona, kterou vytvoříte, nasadí jeden virtuální počítač se systémem Windows Server v nové virtuální síti s jednou podsítí.

Podrobný popis prostředku virtuálního počítače najdete v tématu [virtuální počítače v šabloně Správce prostředků Azure](template-description.md). Další informace o všech prostředcích v šabloně najdete v [návodu k šabloně Správce prostředků Azure](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Trvá asi 10 minut, než provedete tyto kroky.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V tomto kroku se ujistěte, že je nainstalována sada Visual Studio a vytvoříte konzolovou aplikaci používanou k nasazení šablony.

1. Pokud jste tak ještě neučinili, nainstalujte [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Na stránce Úlohy vyberte **vývoj plochy .NET** a klepněte na tlačítko **Instalovat**. V souhrnu vidíte, že **vývojové nástroje .NET Framework 4 - 4.6** jsou automaticky vybrány za vás. Pokud jste již nainstalovali Visual Studio, můžete přidat zatížení .NET pomocí Spouštěč sady Visual Studio.
2. V sadě Visual Studio klepněte na **položku Soubor** > **nového** > **projektu**.
3. V **části Šablony** > **visual c#** vyberte Console App **(.NET Framework)**, zadejte *myDotnetProject* pro název projektu, vyberte umístění projektu a klepněte na tlačítko **OK**.

## <a name="install-the-packages"></a>Instalace balíčků

Balíčky NuGet jsou nejjednodušší způsob, jak nainstalovat knihovny, které potřebujete k dokončení těchto kroků. Pokud chcete v sadě Visual Studio získat knihovny, které potřebujete, postupujte takto:

1. Klepněte na **položku Tools** > **Nuget Package Manager**a potom klepněte na příkaz **Konzola správce balíčků**.
2. Zadejte tyto příkazy do konzoly:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Vytvoření souborů

V tomto kroku vytvoříte soubor šablony, který nasazuje prostředky a soubor parametrů, který do kládání dodává hodnoty parametrů. Můžete také vytvořit autorizační soubor, který se používá k provádění operací Azure Resource Manager.

### <a name="create-the-template-file"></a>Vytvoření souboru šablony

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na *myDotnetProject* > **Přidat** > **novou položku**a potom vyberte **položku Textový soubor** v *aplikaci Visual C# Items*. Pojmenujte soubor *CreateVMTemplate.json*a klepněte na tlačítko **Přidat**.
2. Přidejte tento kód JSON do souboru, který jste vytvořili:

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

3. Uložte soubor CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Vytvoření souboru parametrů

Chcete-li zadat hodnoty parametrů prostředků v šabloně, vytvořte soubor parametrů, který obsahuje hodnoty.

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na *myDotnetProject* > **Přidat** > **novou položku**a potom vyberte **položku Textový soubor** v *aplikaci Visual C# Items*. Pojmenujte soubor *Parameters.json*a klepněte na tlačítko **Přidat**.
2. Přidejte tento kód JSON do souboru, který jste vytvořili:

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

4. Uložte soubor Parameters.json.

### <a name="create-the-authorization-file"></a>Vytvoření autorizačního souboru

Před nasazením šablony se ujistěte, že máte přístup k [objektu zabezpečení služby Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Z instančního objektu získáte token pro ověřování požadavků na Azure Resource Manager. Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID klienta, které potřebujete v autorizačním souboru.

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na *myDotnetProject* > **Přidat** > **novou položku**a potom vyberte **položku Textový soubor** v *aplikaci Visual C# Items*. Pojmenujte soubor *azureauth.properties*a klepněte na tlačítko **Přidat**.
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

    ** &lt;&gt; Nahraďte id předplatného** identifikátorem předplatného, ** &lt;id&gt; aplikace** identifikátorem aplikace služby Active Directory, ** &lt;ověřovacím klíčem&gt; ** s klíčem aplikace a ** &lt;id&gt; klienta** identifikátorem klienta.

3. Uložte soubor azureauth.properties.
4. Nastavte proměnnou prostředí v systému Windows s názvem AZURE_AUTH_LOCATION s úplnou cestou k autorizačnímu souboru, který jste vytvořili, například můžete použít následující příkaz prostředí PowerShell:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Vytvoření klienta pro správu

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili. Potom přidejte tyto příkazy using do existujících příkazů v horní části souboru:

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

Šablona a parametry se nasazují z účtu úložiště v Azure. V tomto kroku vytvoříte účet a nahrajete soubory. 

Chcete-li vytvořit účet, přidejte tento kód do hlavní metody:

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

Nasaďte šablonu a parametry z účtu úložiště, který byl vytvořen. 

Chcete-li nasadit šablonu, přidejte tento kód do hlavní metody:

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

## <a name="delete-the-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nejsou potřeba. Není nutné odstranit každý prostředek odděleně od skupiny prostředků. Odstraňte skupinu prostředků a všechny její prostředky se automaticky odstraní. 

Chcete-li odstranit skupinu prostředků, přidejte tento kód do hlavní metody:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

Mělo by trvat asi pět minut, než bude tato konzolová aplikace spuštěna úplně od začátku do konce. 

1. Chcete-li spustit konzolovou aplikaci, klepněte na tlačítko **Spustit**.

2. Než stisknete **Enter** a začnete spouštět prostředky, může trvat několik minut, než ověříte vytvoření prostředků na webu Azure Portal. Kliknutím na stav nasazení zobrazíte informace o nasazení.

## <a name="next-steps"></a>Další kroky

* Pokud by došlo k problémům s nasazením, dalším krokem by bylo podívat se na [řešení běžných chyb nasazení Azure s Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Zjistěte, jak nasadit virtuální počítač a jeho podpůrné prostředky kontrolou [Nasazení virtuálního počítače Azure pomocí Jazyka C#](csharp.md).