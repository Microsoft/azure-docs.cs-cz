---
title: Nasazení virtuálního počítače pomocí C# a šablony Resource Manageru | Microsoft Docs
description: Zjistěte, jak můžete pomocí C# a šablony Resource Manageru k nasazení virtuálního počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: e57505b4bd89a79af076dc4cf132c844ae0abd1d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527820"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Nasazení virtuálního počítače Azure pomocí jazyka C# a šablony Resource Manageru
Tento článek ukazuje, jak nasadit šablonu Azure Resource Manager pomocí jazyka C#. Šablona, kterou vytvoříte nasadí jednoho virtuálního počítače se systémem Windows Server v nové virtuální sítě s jedinou podsítí.

Podrobný popis prostředek virtuálního počítače naleznete v tématu [virtuálních počítačů v šablonu Azure Resource Manager](template-description.md). Další informace o všechny prostředky v šabloně najdete v tématu [názorný Průvodce šablonou Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Proveďte tyto kroky trvá přibližně 10 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V tomto kroku Ujistěte se, že je nainstalován nástroj Visual Studio a vytvořte konzolovou aplikaci použít k nasazení šablony.

1. Pokud jste to ještě neudělali, nainstalujte [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Vyberte **vývoj aplikací .NET** na stránce úlohy a pak klikněte na tlačítko **nainstalovat**. V souhrnu, můžete uvidíte, že **nástrojů pro vývoj řešení pro rozhraní .NET Framework 4 4.6** je automaticky vybrána pro vás. Pokud jste již nainstalovali Visual Studio, můžete přidat pomocí Spouštěče sady Visual Studio .NET zatížení.
2. V sadě Visual Studio, klikněte na tlačítko **soubor** > **nový** > **projektu**.
3. V **šablony** > **Visual C#**, vyberte **konzolovou aplikaci (rozhraní .NET Framework)**, zadejte *myDotnetProject* pro název projektu a vyberte umístění projektu a pak klikněte na tlačítko **OK**.

## <a name="install-the-packages"></a>Instalaci balíčků

Balíčky NuGet jsou nejjednodušší způsob, jak nainstalovat knihovny, které potřebujete k dokončení těchto kroků. Chcete-li získat knihovny, které je nutné v sadě Visual Studio, proveďte tyto kroky:

1. Klikněte na tlačítko **nástroje** > **Správce balíčků Nuget**a potom klikněte na **Konzola správce balíčků**.
2. V konzole zadejte tyto příkazy:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Vytvoření souborů

V tomto kroku vytvoříte soubor šablony, která nasazuje prostředky a soubor parametrů, který poskytuje hodnoty parametrů šablony. Můžete také vytvořit soubor autorizace, který se používá k provádění operací Azure Resource Manager.

### <a name="create-the-template-file"></a>Vytvořte soubor šablony

1. V Průzkumníku řešení klikněte pravým tlačítkem na *myDotnetProject* > **přidat** > **nová položka**a potom vyberte **textový soubor** v *Visual C# položky*. Název souboru *CreateVMTemplate.json*a potom klikněte na **přidat**.
2. Přidejte do souboru, který jste vytvořili tento kód JSON:

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

Pokud chcete zadat hodnoty pro parametry prostředků, které jsou definované v šabloně, vytvoříte soubor parametrů, který obsahuje hodnoty.

1. V Průzkumníku řešení klikněte pravým tlačítkem na *myDotnetProject* > **přidat** > **nová položka**a potom vyberte **textový soubor** v *Visual C# položky*. Název souboru *Parameters.JSON tímto kódem*a potom klikněte na **přidat**.
2. Přidejte do souboru, který jste vytvořili tento kód JSON:

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

4. Uložte soubor Parameters.JSON tímto kódem.

### <a name="create-the-authorization-file"></a>Vytvoření souboru autorizace

Než bude možné nasadit šablonu, ujistěte se, zda máte přístup k [objektu služby Active Directory](../../resource-group-authenticate-service-principal.md). Z objektu služby můžete získat token pro ověřování požadavků na Azure Resource Manager. Také byste měli zaznamenat ID aplikace, ověřovací klíč a ID klienta, které potřebujete v souboru autorizace.

1. V Průzkumníku řešení klikněte pravým tlačítkem na *myDotnetProject* > **přidat** > **nová položka**a potom vyberte **textový soubor** v *Visual C# položky*. Název souboru *azureauth.properties*a potom klikněte na **přidat**.
2. Přidejte tyto vlastnosti autorizace:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Nahraďte **&lt;id předplatného&gt;** s ID vašeho předplatného **&lt;id aplikace&gt;** s identifikátor aplikace služby Active Directory **&lt;ověřovací klíč&gt;** s klíč aplikace a **&lt;id klienta&gt;** s identifikátorem klienta.

3. Uložte soubor azureauth.properties.
4. Nastavte proměnnou prostředí v systému Windows s názvem AZURE_AUTH_LOCATION s úplnou cestu k souboru autorizace kterou jste vytvořili, například PowerShell následující příkaz lze použít:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>Vytvoření klienta správy

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili a poté přidejte tyto příkazy using do existující příkazy v horní části souboru:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Pro vytvoření klienta správy, přidejte do metody Main tento kód:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete zadat hodnoty pro aplikace, přidejte do hlavní metoda kód:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Šablony a parametry jsou nasazeny v účtu úložiště v Azure. V tomto kroku vytvoříte účet a odesílat soubory. 

Pokud chcete vytvořit účet, přidejte do metodu Main tento kód:

```
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

Nasaďte šablony a parametry z účtu úložiště, který byl vytvořen. 

Pokud chcete nasadit šablonu, přidejte do hlavní metoda tento kód:

```
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

Vzhledem k tomu, že se vám účtovat prostředky využívané v Azure, vždycky je dobrým zvykem odstranit prostředky, které už nejsou potřeba. Nemusíte samostatně odstranit jednotlivé prostředky ze skupiny prostředků. Odstraňte skupinu prostředků a všechny její prostředky se automaticky odstraní. 

Pokud chcete odstranit skupinu prostředků, přidejte do metody Main tento kód:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

Dokončit má trvat přibližně pět minut, než tato Konzolová aplikace spustit úplně od začátku. 

1. Chcete-li spustit aplikaci konzoly, klikněte na tlačítko **spustit**.

2. Před stisknutím klávesy **Enter** zahájíte odstranění prostředků, může trvat několik minut na ověření vytváření prostředků na portálu Azure. Klikněte na tlačítko Stav nasazení zobrazíte informace o tomto nasazení.

## <a name="next-steps"></a>Další postup
* Pokud byly nějaké problémy s nasazením, je dalším krokem by mohla být podívejte se na [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](../../resource-manager-common-deployment-errors.md).
* Informace o nasazení virtuálního počítače a jeho podpůrné prostředků kontrolou [nasazení služby Azure virtuálního počítače pomocí jazyka C#](csharp.md).
