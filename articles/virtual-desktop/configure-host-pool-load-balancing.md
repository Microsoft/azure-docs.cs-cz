---
title: Konfigurace vyrovnávání zatížení virtuální plochy Windows – Azure
description: Jak nakonfigurovat metodu vyrovnávání zatížení pro prostředí Virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128304"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurace metody vyrovnávání zatížení Windows Virtual Desktop

Konfigurace metody vyrovnávání zatížení pro fond hostitelů umožňuje upravit prostředí Virtuální plochy systému Windows tak, aby lépe vyhovovalo vašim potřebám.

>[!NOTE]
> To se nevztahuje na trvalý fond hostitelů plochy, protože uživatelé mají vždy mapování 1:1 na hostitele relace v rámci fondu hostitelů.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurace vyrovnávání zatížení jako první v šíři

Šířka první vyrovnávání zatížení je výchozí konfigurace pro nové netrvalé fondy hostitelů. Vyrovnávání zatížení na šířku první distribuuje nové uživatelské relace mezi všechny hostitele dostupných relací ve fondu hostitelů. Při konfiguraci šířky první vyrovnávání zatížení, můžete nastavit maximální limit relace na hostitele relace v hostitelském fondu.

Nejprve [si stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili. Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Chcete-li nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení nejpr.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Chcete-li nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení nejprve šířku a používal nový maximální limit relace, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurace vyvážení zatížení první hloubky

Hloubka první vyrovnávání zatížení distribuuje nové uživatelské relace k hostiteli relace k dispozici s nejvyšším počtem připojení, ale nedosáhla maximální limit relace prahovou hodnotu. Při konfiguraci hloubky první vyrovnávání zatížení, **je nutné** nastavit maximální limit relace na hostitele relace v hostitelském fondu.

Chcete-li nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení nejprve hloubky, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
