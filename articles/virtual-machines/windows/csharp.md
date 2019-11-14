---
title: Vytvoření a Správa virtuálního počítače Azure pomocíC#
description: Pomocí C# a Azure Resource Manager můžete nasadit virtuální počítač a všechny jeho podpůrné prostředky.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: fe838ade7a65161efaeb60b9e4d8de17a7080da1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032794"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Vytváření a správa virtuálních počítačů s Windows v Azure pomocíC# #

[Virtuální počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik pomocných prostředků Azure. Tento článek popisuje vytváření, správu a odstraňování prostředků virtuálních počítačů pomocí C#nástroje. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu ve Visual Studiu
> * Instalace balíčku
> * Vytvořit pověření
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Provedení těchto kroků trvá přibližně 20 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Pokud jste to ještě neudělali, nainstalujte [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Na stránce úlohy vyberte **vývoj pro desktopy .NET** a pak klikněte na **nainstalovat**. V souhrnu vidíte, že se pro vás automaticky vybraly **.NET Framework nástroje pro vývoj 4-4,6** . Pokud jste již nainstalovali aplikaci Visual Studio, můžete přidat úlohu rozhraní .NET pomocí spouštěče sady Visual Studio.
2. V sadě Visual Studio klikněte na **Soubor** > **Nový** > **Projekt**.
3. V **části šablony** > **C#vizuál**vyberte **Konzolová aplikace (.NET Framework)** , jako název projektu zadejte *myDotnetProject* , vyberte umístění projektu a pak klikněte na **OK**.

## <a name="install-the-package"></a>Instalace balíčku

Balíčky NuGet představují nejjednodušší způsob, jak nainstalovat knihovny, které potřebujete k dokončení těchto kroků. Chcete-li získat knihovny, které potřebujete v aplikaci Visual Studio, proveďte tyto kroky:

1. Klikněte na **nástroje** > **Správce balíčků NuGet**a pak klikněte na **Konzola správce balíčků**.
2. Do konzoly zadejte tento příkaz:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Vytvořit pověření

Než začnete tento krok, ujistěte se, že máte přístup k [instančnímu objektu služby Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID tenanta, které budete potřebovat v pozdějším kroku.

### <a name="create-the-authorization-file"></a>Vytvoření autorizačního souboru

1. V Průzkumník řešení klikněte pravým tlačítkem myši na *myDotnetProject* > **Přidat** > **novou položku**a potom v části *vizuální C# položky*vyberte **textový soubor** . Pojmenujte soubor *azureauth. Properties*a pak klikněte na **Přidat**.
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

    Nahraďte **&lt;ID předplatného&gt;** pomocí identifikátoru předplatného, **&lt;&gt;ID aplikace** s identifikátorem aplikace služby Active Directory, **&lt;Authentication-Key&gt;** s klíčem aplikace a **&lt;ID tenanta**&gt;s identifikátorem tenanta.

3. Uložte soubor azureauth. Properties. 
4. V systému Windows nastavte proměnnou prostředí s názvem AZURE_AUTH_LOCATION s úplnou cestou k autorizačnímu souboru, který jste vytvořili. Například můžete použít následující příkaz prostředí PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Vytvořit klienta pro správu

1. Otevřete soubor Program.cs pro projekt, který jste vytvořili. Pak přidejte tyto příkazy using do stávajících příkazů v horní části souboru:

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

Všechny prostředky musí být obsaženy ve [skupině prostředků](../../azure-resource-manager/resource-group-overview.md).

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

### <a name="create-the-availability-set"></a>Vytvoření skupiny dostupnosti

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů používaných vaší aplikací.

Chcete-li vytvořit skupinu dostupnosti, přidejte tento kód do metody Main:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy

[Veřejná IP adresa](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) je potřeba ke komunikaci s virtuálním počítačem.

Pro vytvoření veřejné IP adresy pro virtuální počítač přidejte tento kód do metody Main:
   
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

Virtuální počítač potřebuje ke komunikaci s virtuální sítí síťové rozhraní.

Chcete-li vytvořit síťové rozhraní, přidejte tento kód do metody Main:

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

Chcete-li vytvořit virtuální počítač, přidejte tento kód do metody Main:

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
> V tomto kurzu se vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru dalších imagí najdete v tématu [navigace a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a rozhraní příkazového řádku Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Pokud chcete použít existující disk místo image Marketplace, použijte tento kód:

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

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete chtít vytvořit kód pro automatizaci opakujících se nebo složitých úloh.

Pokud potřebujete s virtuálním počítačem něco udělat, musíte získat jeho instanci:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

Chcete-li získat informace o virtuálním počítači, přidejte tento kód do metody Main:

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

Můžete zastavit virtuální počítač a zachovat všechna jeho nastavení, ale nadále se vám bude účtovat, nebo můžete virtuální počítač zastavit a zrušit jeho přidělení. Když je virtuální počítač uvolněný, oddělují se i všechny prostředky, které jsou k němu přidružené, a pro něj končí fakturace.

Chcete-li zastavit virtuální počítač bez jeho přidělení, přidejte tento kód do metody Main:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Pokud chcete zrušit přidělení virtuálního počítače, změňte volání stavu PowerOff na tento kód:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

Pokud chcete virtuální počítač spustit, přidejte tento kód do metody Main:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Při rozhodování o velikosti vašeho virtuálního počítače je třeba vzít v úvahu mnoho aspektů nasazení. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md).  

Chcete-li změnit velikost virtuálního počítače, přidejte tento kód do metody Main:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Chcete-li přidat datový disk k virtuálnímu počítači, přidejte tento kód do metody Main. Tento příklad přidává datový disk, který má velikost 2 GB, Han, logickou jednotku 0 a typ mezipaměti pro čtení:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nepotřebujete. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, stačí odstranit skupinu prostředků.

Pokud chcete odstranit skupinu prostředků, přidejte tento kód do metody Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Spuštění aplikace

Spuštění této konzolové aplikace z začátku do konce by mělo trvat přibližně pět minut. 

1. Chcete-li spustit konzolovou aplikaci, klikněte na tlačítko **Start**.

2. Než stisknete **ENTER** a začnete odstraňovat prostředky, může trvat několik minut, než se ověří vytváření prostředků v Azure Portal. Kliknutím na stav nasazení zobrazíte informace o nasazení.

## <a name="next-steps"></a>Další kroky
* Využijte výhod používání šablony k vytvoření virtuálního počítače pomocí informací v části [nasazení virtuálního počítače Azure pomocí C# a správce prostředků šablony](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Přečtěte si další informace o používání [knihoven Azure pro .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
