---
title: Nasazení virtuálního počítače pomocí C# a šablony Resource Manageru | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí jazyka C# a šablony Resource Manageru k nasazení virtuálního počítače Azure.
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
ms.openlocfilehash: 005b0e74084325606a9a07df6b36b9100cad1750
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885944"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Nasazení virtuálního počítače Azure pomocí jazyka C# a šablony Resource Manageru
V tomto článku se dozvíte, jak nasadit šablonu Azure Resource Manageru pomocí jazyka C#. Šablona, kterou vytvoříte nasadí jeden virtuální počítač s Windows serverem v nové virtuální sítě s jedinou podsítí.

Podrobný popis prostředku virtuálního počítače najdete v tématu [virtuální počítače v šabloně Azure Resource Manageru](template-description.md). Další informace o všech prostředcích v šabloně najdete v tématu [názorném průvodci k šablonám Azure Resource Manageru](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Proveďte tyto kroky trvá asi 10 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V tomto kroku můžete zajistit, že je nainstalována aplikace Visual Studio a vytvořte konzolovou aplikaci použít k nasazení šablony.

1. Pokud jste tak dosud neučinili, nainstalujte [sady Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Vyberte **vývoj desktopových aplikací .NET** na stránku úlohy a pak klikněte na tlačítko **nainstalovat**. Stručně řečeno, vidíte, že **vývojové nástroje .NET Framework 4 – 4.6** se vybere automaticky za vás. Pokud jste již nainstalovali aplikaci Visual Studio, můžete přidat úlohy .NET pomocí Visual Studio Spouštěče.
2. V sadě Visual Studio klikněte na **Soubor** > **Nový** > **Projekt**.
3. V **šablony** > **Visual C#** vyberte **Konzolová aplikace (.NET Framework)**, zadejte *myDotnetProject* název projekt, vyberte umístění projektu a pak klikněte na tlačítko **OK**.

## <a name="install-the-packages"></a>Nainstalujte balíčky

Nejjednodušší způsob, jak nainstalovat knihoven, které je potřeba dokončit tyto kroky jsou balíčky NuGet. Chcete-li získat knihoven, které je nutné v sadě Visual Studio, proveďte tyto kroky:

1. Klikněte na tlačítko **nástroje** > **Správce balíčků Nuget**a potom klikněte na tlačítko **Konzola správce balíčků**.
2. V konzole zadejte tyto příkazy:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Vytvoření souborů

V tomto kroku vytvoříte soubor šablony, který se nasazuje prostředky a parametry souboru, který poskytuje hodnoty parametrů v šabloně. Můžete také vytvořit soubor autorizace, který se používá k provádění operací Azure Resource Manageru.

### <a name="create-the-template-file"></a>Vytvořte soubor šablony

1. V Průzkumníku řešení klikněte pravým tlačítkem na *myDotnetProject* > **přidat** > **nová položka**a pak vyberte **textovýsoubor** v *položky Visual C#*. Název souboru *CreateVMTemplate.json*a potom klikněte na tlačítko **přidat**.
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

Zadání hodnot pro parametry prostředků, které jsou definovány v šabloně, vytvořte soubor parametrů, který obsahuje hodnoty.

1. V Průzkumníku řešení klikněte pravým tlačítkem na *myDotnetProject* > **přidat** > **nová položka**a pak vyberte **textovýsoubor** v *položky Visual C#*. Název souboru *Parameters.json*a potom klikněte na tlačítko **přidat**.
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

4. Uložení souboru Parameters.json.

### <a name="create-the-authorization-file"></a>Vytvoření souboru autorizace

Než bude možné nasadit šablonu, ujistěte se, že máte přístup k [instanční objekt služby Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Z instančního objektu získání tokenu pro ověřování požadavků na Azure Resource Manager. Také byste měli zaznamenávat ID aplikace, ověřovací klíč a ID tenanta, které potřebujete v souboru autorizace.

1. V Průzkumníku řešení klikněte pravým tlačítkem na *myDotnetProject* > **přidat** > **nová položka**a pak vyberte **textovýsoubor** v *položky Visual C#*. Název souboru *azureauth.properties*a potom klikněte na tlačítko **přidat**.
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

    Nahraďte **&lt;id předplatného&gt;** identifikátorem předplatné **&lt;id aplikace&gt;** aplikaci služby Active Directory identifikátor, **&lt;ověřovací klíč&gt;** s klíčem aplikace a **&lt;id tenanta&gt;** s identifikátor tenanta.

3. Uložte soubor azureauth.properties.
4. Nastavení proměnné prostředí ve Windows s názvem AZURE_AUTH_LOCATION s úplnou cestou k souboru autorizace, který jste vytvořili, například následující příkaz Powershellu můžete použít příkaz:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>Vytvoření klienta služby správy

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili a pak přidejte tyto pomocí příkazů do stávajících příkazů v horní části souboru:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. K vytvoření klienta služby správy, přidejte tento kód do metody Main:

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

Zadání hodnot pro aplikaci, přidejte kód do metody Main:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Z účtu úložiště v Azure se nasazují šablonu a parametry. V tomto kroku vytvoříte účet a nahrávání souborů. 

Chcete-li vytvořit účet, do metody Main přidejte tento kód:

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

Nasaďte šablonu a parametry z účtu úložiště, který byl vytvořen. 

Pokud chcete nasadit šablonu, přidejte tento kód do metody Main:

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

Vzhledem k tomu, že se vám účtovat prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nejsou potřeba. Není nutné samostatně odstranit jednotlivé prostředky ze skupiny prostředků. Odstranit skupinu prostředků a všechny její prostředky se automaticky odstraní. 

Pokud chcete odstranit skupinu prostředků, do metody Main přidejte tento kód:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

To by měla trvat asi pět minut, než tuto konzolovou aplikaci pro spuštění úplně od začátku. 

1. Chcete-li spustit konzolovou aplikaci, klikněte na tlačítko **Start**.

2. Než stisknete klávesu **Enter** spuštění odstranění prostředků, může trvat několik minut na ověření vytváření prostředků na webu Azure Portal. Klikněte na stav nasazení můžete zobrazit informace o nasazení.

## <a name="next-steps"></a>Další postup
* Pokud byly nějaké problémy s nasazením, je dalším krokem by se podívat na [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](../../resource-manager-common-deployment-errors.md).
* Zjistěte, jak nasadit virtuální počítač a jeho podpůrné prostředky kontrolou [nasazení Azure virtuálního počítače pomocí C#](csharp.md).
