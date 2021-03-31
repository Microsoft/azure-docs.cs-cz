---
title: Vytvoření virtuálního počítače a účtu úložiště pro škálovatelnou aplikaci v Azure
description: Zjistěte, jak nasadit virtuální počítač, který se použije ke spuštění škálovatelné aplikace využívající úložiště objektů blob v Azure.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8c52b03cb6dec6096565e9eac26b7b2c4a30e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073246"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Vytvoření virtuálního počítače a účtu úložiště pro škálovatelnou aplikaci

Tento kurz je první částí série. V tomto kurzu se dozvíte, jak nasadit aplikaci, která nahrává a stahuje velké objemy náhodných dat s využitím účtu úložiště Azure. Až budete hotovi, budete mít na virtuálním počítači spuštěnou konzolovou aplikaci, která nahrává a stahuje velké objemy dat do účtu úložiště.

V první části tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště
> * Vytvoření virtuálního počítače
> * Konfigurace rozšíření vlastních skriptů

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell modul AZ verze 0,7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště
 
Ukázka nahraje 50 velkých souborů do kontejneru objektů blob v účtu služby Azure Storage. Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim. Ve skupině prostředků, kterou jste vytvořili, vytvořte účet úložiště pomocí příkazu [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) .

V následujícím příkazu nahraďte zástupný symbol `<blob_storage_account>` vlastním globálně jedinečným názvem účtu služby Blob Storage.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení, která se používají při nasazení virtuálního počítače, jako je image virtuálního počítače, jeho velikost a konfigurace ověřování. Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Nasazení konfigurace

Tento kurz vyžaduje, aby na virtuálním počítači bylo nainstalovaných několik položek. Pomocí rozšíření vlastních skriptů se spustí skript PowerShellu, který provede následující úlohy:

> [!div class="checklist"]
> * Instalace .NET Core 2.0
> * Instalace Chocolatey
> * Instalace GITu
> * Naklonování ukázkového úložiště
> * Obnovení balíčků NuGet
> * Vytvoření 50 1GB souborů s náhodnými daty

Spuštěním následující rutiny dokončete konfiguraci virtuálního počítače. Dokončení tohoto kroku trvá 5 až 15 minut.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Další kroky

V první části série jste se seznámili s vytvořením účtu úložiště, nasazením virtuálního počítače a konfigurací požadovaných součástí na virtuálním počítači a naučili jste se například:

> [!div class="checklist"]
> * Vytvoření účtu úložiště
> * Vytvoření virtuálního počítače
> * Konfigurace rozšíření vlastních skriptů

Přejděte k druhé části série, kde do účtu úložiště nahrajete velké objemy dat s využitím exponenciálního opakování a paralelismu.

> [!div class="nextstepaction"]
> [Paralelní nahrání velkého množství velkých souborů do účtu úložiště](storage-blob-scalable-app-upload-files.md)
