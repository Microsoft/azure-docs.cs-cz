---
title: Nakonfigurujte virtuální plochy Preview Windows-metodu Vyrovnávání zatížení – Azure
description: Jak konfigurovat metodu Vyrovnávání zatížení pro prostředí virtuálního klienta Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328880"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Konfigurace metody vyrovnávání zatížení Preview virtuální plochy Windows

Konfigurace metodu Vyrovnávání zatížení pro skupinu hostitelů, můžete upravit prostředí Preview virtuální plochy Windows tak, aby lépe vyhovovala vašim potřebám.

>[!NOTE]
> To se nevztahují na fond trvalé klasické pracovní plochy hostiteli vzhledem k tomu, že uživatelé vždy mít mapování 1:1 pro hostitele relace v rámci fondu hostitele.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení první šířka

Vyrovnávání zatížení šířka první je výchozí konfigurace pro nové dočasné hostitelů fondy. Vyrovnávání zatížení šířka první distribuuje nových uživatelských relací na všech hostitelích dostupných relace ve fondu hostitele. Při konfiguraci Vyrovnávání zatížení první kontejnerových nástrojů, můžete nastavit limit maximální časový limit relace na hostiteli relace ve fondu hostitele.

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
