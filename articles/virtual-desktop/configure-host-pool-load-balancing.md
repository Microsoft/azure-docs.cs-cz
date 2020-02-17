---
title: Konfigurace vyrovnávání zatížení virtuálních počítačů s Windows – Azure
description: Jak nakonfigurovat metodu vyrovnávání zatížení pro prostředí virtuálních počítačů s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 1b341732dd844ba172c7e682fb7089c98ca9c165
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367605"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurace metody vyrovnávání zatížení Windows Virtual Desktop

Konfigurace metody vyrovnávání zatížení pro fond hostitelů vám umožní upravit prostředí virtuálních počítačů s Windows, aby lépe vyhovovalo vašim potřebám.

>[!NOTE]
> To se nevztahuje na fond hostitelů trvalé plochy, protože uživatelé mají vždycky mapování 1:1 na hostitele relace v rámci fondu hostitelů.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurace šířky – první vyrovnávání zatížení

Šířka – první vyrovnávání zatížení je výchozí konfigurace pro nové netrvalé fondy hostitelů. Šířka – první vyrovnávání zatížení distribuuje nové uživatelské relace napříč všemi dostupnými hostiteli relací ve fondu hostitelů. Při konfiguraci škály vyrovnávání zatížení můžete nastavit maximální počet relací na hostitele relace ve fondu hostitelů.

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pokud chcete nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení po prvním použití bez úprav maximálního limitu relací, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Pokud chcete nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení a používal nový limit maximálního počtu relací, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurace hloubky – první vyrovnávání zatížení

Hloubka při prvním vyrovnávání zatížení distribuuje nové uživatelské relace k dostupnému hostiteli relace s největším počtem připojení, ale nedosáhla maximální prahové hodnoty limitu relací. Při konfiguraci vyrovnávání zatížení prvního rozsahu **musíte** nastavit maximální počet relací na hostitele relace ve fondu hostitelů.

Pokud chcete nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení první hloubky, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
