---
title: Použít licenci Windows na virtuální počítače hostitele relace – Azure
description: Popisuje, jak použít licenci Windows pro virtuální počítače virtuálních počítačů s Windows.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010136"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Použít licenci Windows na virtuální počítače hostitele relace

Zákazníci, kteří mají správnou licenci na spouštění úloh virtuálních klientů Windows, mají nárok na použití licence Windows na virtuální počítače hostitele relace a jejich spuštění bez placení jiné licence. Další informace najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Způsoby použití licence na virtuální počítače s Windows
Licencování virtuálních klientských počítačů s Windows umožňuje použít licenci na libovolný virtuální počítač s Windows nebo Windows serverem, který je registrovaný jako hostitel relace v hostitelském fondu a přijímá připojení uživatele. Tato licence se nevztahuje na virtuální počítače, které jsou spuštěny jako servery sdílené složky, řadiče domény a tak dále.

Existuje několik způsobů, jak použít licenci pro virtuální počítače s Windows:
- Fond hostitelů a jeho hostitele virtuálních počítačů můžete vytvořit pomocí [nabídky Azure Marketplace](./create-host-pools-azure-marketplace.md). Virtuální počítače vytvořené tímto způsobem mají automaticky použitu licenci.
- Pomocí [šablony Azure Resource Manager GitHubu](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)můžete vytvořit fond hostitelů a jeho virtuální počítače hostitele relace. Virtuální počítače vytvořené tímto způsobem mají automaticky použitu licenci.
- Licenci můžete použít pro existující virtuální počítač hostitele relace. Pokud to chcete provést, postupujte podle pokynů v části [Vytvoření fondu hostitelů s prostředím PowerShell](./create-host-pools-powershell.md) pro vytvoření fondu hostitelů a přidružených virtuálních počítačů a pak se vraťte k tomuto článku a Naučte se, jak tuto licenci použít.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Použití licence Windows na virtuální počítač hostitele relace
Ujistěte se, že máte [nainstalovanou a nakonfigurovanou nejnovější Azure PowerShell](/powershell/azure/). Pokud chcete použít licenci Windows, spusťte následující rutinu PowerShellu:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, že váš virtuální počítač hostitele relace využívá výhod licencování.
Po nasazení virtuálního počítače spusťte tuto rutinu, která ověří typ licence:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Virtuální počítač hostitele relace s použitou licencí Windows vám ukáže něco podobného:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Virtuální počítače bez použité licence Windows vám ukáže něco podobného:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Spuštěním následující rutiny zobrazíte seznam všech virtuálních počítačů hostitelů relací, ve kterých se ve vašem předplatném Azure použili licence Windows:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
