---
title: Vytvoření a Správa virtuálního počítače Azure pomocí jazyka C# | Dokumentace Microsoftu
description: Použití jazyka C# a Azure Resource Manageru k nasazení virtuálního počítače a všechny její Podpůrné prostředky.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: ce05d097aa69aa1aadb8450e40722448bc5a7de0
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883054"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Vytvoření a správa virtuálních počítačů s Windows v Azure pomocí jazyka C# #

[Virtuálního počítače Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik Podpůrné prostředky Azure. Tento článek popisuje vytváření, správě a odstraňování prostředků virtuálního počítače pomocí C#. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu ve Visual Studiu
> * Instalace balíčku
> * Vytvořte přihlašovací údaje
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Proveďte tyto kroky trvá přibližně 20 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Pokud jste tak dosud neučinili, nainstalujte [sady Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Vyberte **vývoj desktopových aplikací .NET** na stránku úlohy a pak klikněte na tlačítko **nainstalovat**. Stručně řečeno, vidíte, že **vývojové nástroje .NET Framework 4 – 4.6** se vybere automaticky za vás. Pokud jste již nainstalovali aplikaci Visual Studio, můžete přidat úlohy .NET pomocí Visual Studio Spouštěče.
2. V sadě Visual Studio klikněte na **Soubor** > **Nový** > **Projekt**.
3. V **šablony** > **Visual C#** vyberte **Konzolová aplikace (.NET Framework)**, zadejte *myDotnetProject* název projekt, vyberte umístění projektu a pak klikněte na tlačítko **OK**.

## <a name="install-the-package"></a>Instalace balíčku

Nejjednodušší způsob, jak nainstalovat knihoven, které je potřeba dokončit tyto kroky jsou balíčky NuGet. Chcete-li získat knihoven, které je nutné v sadě Visual Studio, proveďte tyto kroky:

1. Klikněte na tlačítko **nástroje** > **Správce balíčků Nuget**a potom klikněte na tlačítko **Konzola správce balíčků**.
2. V konzole zadejte tento příkaz:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Vytvořte přihlašovací údaje

Předtím, než se pustíte do tohoto kroku, ujistěte se, že máte přístup do [instanční objekt služby Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Také byste měli zaznamenávat ID aplikace, ověřovací klíč a ID tenanta, který budete potřebovat v pozdějším kroku.

### <a name="create-the-authorization-file"></a>Vytvoření souboru autorizace

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
4. Nastavení proměnné prostředí ve Windows s názvem AZURE_AUTH_LOCATION s úplnou cestou k souboru autorizace, který jste vytvořili. Například můžete použít následující příkaz Powershellu:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Vytvoření klienta služby správy

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili a pak přidejte tyto pomocí příkazů do stávajících příkazů v horní části souboru:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být součástí [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md).

Zadejte hodnoty pro aplikace a vytvořte skupinu prostředků, do metody Main přidejte tento kód:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Vytvořit skupinu dostupnosti

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů, které používá vaše aplikace.

Pokud chcete vytvořit skupinu dostupnosti, do metody Main přidejte tento kód:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy

A [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) je potřeba ke komunikaci s virtuálním počítačem.

Chcete-li vytvořit veřejnou IP adresu pro virtuální počítač, do metody Main přidejte tento kód:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Virtuální počítač musí být v podsíti [virtuální síť](../../virtual-network/virtual-networks-overview.md).

Pokud chcete vytvořit podsítě a virtuální síť, do metody Main přidejte tento kód:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Vytvořte síťové rozhraní

Virtuální počítač potřebuje síťové rozhraní ve virtuální síti komunikovat.

Vytvořte síťové rozhraní do metody Main přidejte tento kód:

```
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

K vytvoření virtuálního počítače, do metody Main přidejte tento kód:

```
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
> V tomto kurzu se vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru další Image najdete v tématu [vyhledání a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Pokud chcete použít stávající disk místo marketplace image, použijte tento kód:

```
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

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete vytvořit kód pro automatizaci opakovaných nebo složitých úloh.

Když budete potřebovat k ničemu s virtuálním Počítačem, budete muset získat instanci:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Získání informací o virtuálním počítači

Pokud chcete získat informace o virtuálním počítači, do metody Main přidejte tento kód:

```
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

Zastavit virtuální počítač a ponechat jeho nastavení ale dál bude účtovat, nebo můžete zastavit virtuální počítač a uvolnit ji. Při zrušení přidělení virtuálního počítače jsou všechny prostředky s ním spojená také končí uvolnění a účtování pro něj.

Pokud chcete zastavit virtuální počítač bez rušení přidělení ho, přidejte tento kód do metody Main:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Pokud chcete zrušit přidělení virtuálního počítače, změna stavu PowerOff volání tento kód:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

Chcete-li spustit virtuální počítač, do metody Main přidejte tento kód:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Mnoho aspektů nasazení má brát při rozhodování o velikosti pro virtuální počítač. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md).  

Chcete-li změnit velikost virtuálního počítače, do metody Main přidejte tento kód:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Chcete-li přidat datový disk k virtuálnímu počítači, přidejte tento kód do metody Main přidat datový disk, který je 2 GB. velikost han logické jednotce LUN 0 a ukládání do mezipaměti typu ReadWrite:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtovat prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nejsou potřeba. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, je vše, co musíte udělat, odstraňte skupinu prostředků.

Pokud chcete odstranit skupinu prostředků, do metody Main přidejte tento kód:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

To by měla trvat asi pět minut, než tuto konzolovou aplikaci pro spuštění úplně od začátku. 

1. Chcete-li spustit konzolovou aplikaci, klikněte na tlačítko **Start**.

2. Než stisknete klávesu **Enter** spuštění odstranění prostředků, může trvat několik minut na ověření vytváření prostředků na webu Azure Portal. Klikněte na stav nasazení můžete zobrazit informace o nasazení.

## <a name="next-steps"></a>Další postup
* Využijte výhod pomocí šablony k vytvoření virtuálního počítače s využitím informací v [nasazení virtuálního počítače Azure pomocí jazyka C# a šablony Resource Manageru](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Další informace o používání [knihovny Azure pro .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

