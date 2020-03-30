---
title: Použití licence Windows pro virtuální počítače hostitele relací – Azure
description: Popisuje, jak použít licenci systému Windows pro virtuální počítače virtuální plochy systému Windows.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254232"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Použití licence systému Windows pro virtuální počítače hostitele relací

Zákazníci, kteří mají řádnou licenci ke spuštění úloh Windows Virtual Desktop, mohou použít licenci systému Windows pro své virtuální počítače hostitele relací a spustit je bez placení za jinou licenci. Další informace naleznete v tématu [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Způsoby použití licence na virtuální plochu systému Windows
Licencování virtuální plochy systému Windows umožňuje použít licenci pro jakýkoli virtuální počítač se systémem Windows nebo Windows Server, který je registrován jako hostitel relací ve fondu hostitelů a přijímá uživatelská připojení. Tato licence se nevztahuje na virtuální počítače, které jsou spuštěny jako servery pro sdílení souborů, řadiče domény a tak dále.

Licenci Virtuální plochy systému Windows můžete používat několika způsoby:
- Fond hostitelů a jeho virtuální počítače hostitele relací můžete vytvořit pomocí [nabídky Azure Marketplace](./create-host-pools-azure-marketplace.md). Virtuální počítače vytvořené tímto způsobem automaticky mají licenci použita.
- Fond hostitelů a jeho virtuální počítače hostitele relací můžete vytvořit pomocí [šablony GitHub Azure Resource Manager](./create-host-pools-arm-template.md). Virtuální počítače vytvořené tímto způsobem automaticky mají licenci použita.
- Licenci můžete použít pro existující virtuální počítač hostitele relace. Chcete-li to provést, nejprve postupujte podle pokynů v [vytvořit fond hostitelů s PowerShell](./create-host-pools-powershell.md) vytvořit fond hostitelů a přidružené virtuální chody a pak se vrátit k tomuto článku se dozvíte, jak použít licenci.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Použití licence systému Windows u hostitelského virtuálního uživatele relace
Ujistěte se, že jste [nainstalovali a nakonfigurovali nejnovější Azure PowerShell](/powershell/azure/overview). Spusťte následující rutinu prostředí PowerShell a použijte licenci systému Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, zda hostitelský virtuální počítač relace využívá výhody licencování
Po nasazení virtuálního počítače spusťte tuto rutinu ot ověřit typ licence:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Virtuální virtuální hostitel relace s použitou licencí systému Windows vám zobrazí něco takového:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Virtuální servery bez použité licence systému Windows vám zobrazí něco takového:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Spuštěním následující rutiny zobrazíte seznam všech hostitelských virtuálních počítačů relací, u kterých je ve vašem předplatném Azure použita licence systému Windows:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
