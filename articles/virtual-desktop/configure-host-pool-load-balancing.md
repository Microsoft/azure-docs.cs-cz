---
title: Nakonfigurujte virtuální plochy Windows-metodu Vyrovnávání zatížení (verze preview) – Azure
description: Jak konfigurovat metodu Vyrovnávání zatížení pro prostředí virtuálního klienta Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318341"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurace metody vyrovnávání zatížení virtuálního klienta Windows

Konfigurace metoda Vyrovnávání zatížení pro skupinu hostitelů, můžete upravit prostředí virtuálního klienta Windows (preview) tak, aby lépe vyhovovala vašim potřebám.

>[!NOTE]
> To se nevztahují na fond trvalé klasické pracovní plochy hostiteli vzhledem k tomu, že uživatelé vždy mít mapování 1:1 pro hostitele relace v rámci fondu hostitele.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení první šířka

Vyrovnávání zatížení šířka první je výchozí konfigurace pro nové dočasné hostitelů fondy. Šířka first Vyrovnávání zatížení distribuuje nových uživatelských relací na všech hostitelích dostupných relace ve fondu hostitele. Při konfiguraci Vyrovnávání zatížení první kontejnerových nástrojů, můžete nastavit limit maximální časový limit relace na hostiteli relace ve fondu hostitele.

Nejprve je potřeba [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

Ke konfiguraci fondu hostitele k provedení první šířka zátěže bez nastavení limit maximální časový limit relace, spusťte následující rutinu Powershellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Ke konfiguraci fondu hostitele provádět Vyrovnávání zatížení šířka první a použít novou relaci maximální limit, spusťte následující rutinu Powershellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení první hloubky

Vyrovnávání zatížení v první hloubce distribuuje nové relace uživatele na hostiteli, který k dispozici relace s nejvyšší počet připojení, avšak nepropracoval se limit prahové hodnoty maximální časový limit relace. Při konfiguraci služby Vyrovnávání zatížení hloubky první, je **musí** nastavit limit maximální časový limit relace na hostiteli relace ve fondu hostitele.

Ke konfiguraci fondu hostitele provádět Vyrovnávání zatížení první hloubka, spusťte následující rutinu Powershellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
