---
title: 'Vytvoření a správa virtuálního počítače Azure pomocí C #'
description: Pomocí C# a Azure Resource Manager nasadit virtuální počítač a všechny jeho podpůrné prostředky.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 9b873ce9e9b203c6999f93adc8fcd96de3dbf362
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452056"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Vytváření a správa virtuálních aplikací Windows v Azure pomocí C # #

Virtuální [počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik podpůrných prostředků Azure. Tento článek popisuje vytváření, správu a odstranění prostředků virtuálního aplikace pomocí jazyka C#. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu ve Visual Studiu
> * Instalace balíčku
> * Vytvoření přihlašovacích údajů
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Trvá asi 20 minut, než provedete tyto kroky.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Pokud jste tak ještě neučinili, nainstalujte [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Na stránce Úlohy vyberte **vývoj plochy .NET** a klepněte na tlačítko **Instalovat**. V souhrnu vidíte, že **vývojové nástroje .NET Framework 4 - 4.6** jsou automaticky vybrány za vás. Pokud jste již nainstalovali Visual Studio, můžete přidat zatížení .NET pomocí Spouštěč sady Visual Studio.
2. V sadě Visual Studio klepněte na **položku Soubor** > **nového** > **projektu**.
3. V **části Šablony** > **visual c#** vyberte Console App **(.NET Framework)**, zadejte *myDotnetProject* pro název projektu, vyberte umístění projektu a klepněte na tlačítko **OK**.

## <a name="install-the-package"></a>Instalace balíčku

Balíčky NuGet jsou nejjednodušší způsob, jak nainstalovat knihovny, které potřebujete k dokončení těchto kroků. Pokud chcete v sadě Visual Studio získat knihovny, které potřebujete, postupujte takto:

1. Klepněte na **položku Tools** > **Nuget Package Manager**a potom klepněte na příkaz **Konzola správce balíčků**.
2. Zadejte tento příkaz do konzoly:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

Před zahájením tohoto kroku se ujistěte, že máte přístup k [objektu active directory](../../active-directory/develop/howto-create-service-principal-portal.md). Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID klienta, které potřebujete v pozdějším kroku.

### <a name="create-the-authorization-file"></a>Vytvoření autorizačního souboru

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
4. Nastavte proměnnou prostředí v systému Windows s názvem AZURE_AUTH_LOCATION s úplnou cestou k autorizačnímu souboru, který jste vytvořili. Lze například použít následující příkaz prostředí PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Vytvoření klienta pro správu

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili. Potom přidejte tyto příkazy using do existujících příkazů v horní části souboru:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být obsaženy ve [skupině prostředků](../../azure-resource-manager/management/overview.md).

Chcete-li zadat hodnoty pro aplikaci a vytvořit skupinu prostředků, přidejte tento kód do metody Main:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Vytvoření sady dostupnosti

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů používaných vaší aplikací.

Chcete-li vytvořit sadu dostupnosti, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro komunikaci s virtuálním počítačem je potřeba [veřejná IP adresa.](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)

Chcete-li vytvořit veřejnou IP adresu pro virtuální počítač, přidejte tento kód do hlavní metody:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Virtuální počítač musí být v podsíti [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

Chcete-li vytvořit podsíť a virtuální síť, přidejte tento kód do metody Main:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Vytvoření síťového rozhraní

Virtuální počítač potřebuje síťové rozhraní pro komunikaci ve virtuální síti.

Chcete-li vytvořit síťové rozhraní, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Teď, když jste vytvořili všechny podpůrné prostředky, můžete vytvořit virtuální počítač.

Chcete-li vytvořit virtuální počítač, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Tento kurz vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru dalších bitových kopií najdete [v tématu Navigace a výběr ibi virtuálních zařízení Azure pomocí Windows PowerShellu a Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Pokud chcete místo image marketplace použít existující disk, použijte tento kód:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Provádění úloh správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete chtít vytvořit kód pro automatizaci opakovaných nebo složitých úloh.

Když potřebujete něco udělat s virtuálním virtuálním virtuálním ms, musíte získat jeho instanci:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Získání informací o virtuálním virtuálním mísu

Chcete-li získat informace o virtuálním počítači, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Můžete zastavit virtuální počítač a zachovat všechna jeho nastavení, ale nadále se za něj účtovat, nebo můžete zastavit virtuální počítač a navrátit ho. Když je virtuální počítač nabytá, všechny prostředky s ním spojené jsou také přiděleny a fakturace pro něj končí.

Chcete-li zastavit virtuální počítač bez zrušení jeho přidělení, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Pokud chcete navrátit virtuální počítač, změňte volání PowerOff na tento kód:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

Chcete-li spustit virtuální počítač, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Mnoho aspektů nasazení by měly být považovány při rozhodování o velikosti pro váš virtuální počítač. Další informace najdete v tématu [velikosti virtuálních počítače](sizes.md).  

Chcete-li změnit velikost virtuálního počítače, přidejte tento kód do hlavní metody:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Chcete-li do virtuálního počítače přidat datový disk, přidejte tento kód do metody Main. Tento příklad přidá datový disk o velikosti 2 GB, han a LUN 0 a typ čtení readwrite do mezipaměti:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nejsou potřeba. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, stačí odstranit skupinu prostředků.

Chcete-li odstranit skupinu prostředků, přidejte tento kód do hlavní metody:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

Mělo by trvat asi pět minut, než bude tato konzolová aplikace spuštěna úplně od začátku do konce. 

1. Chcete-li spustit konzolovou aplikaci, klepněte na tlačítko **Spustit**.

2. Než stisknete **Enter** a začnete spouštět prostředky, může trvat několik minut, než ověříte vytvoření prostředků na webu Azure Portal. Kliknutím na stav nasazení zobrazíte informace o nasazení.

## <a name="next-steps"></a>Další kroky
* Využijte výhod použití šablony k vytvoření virtuálního počítače pomocí informací v [nasazení virtuálního počítače Azure pomocí jazyka C# a šablony Správce prostředků](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Další informace o používání [knihoven Azure pro rozhraní .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
