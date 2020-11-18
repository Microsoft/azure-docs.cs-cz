---
title: Rozšířená podpora životního cyklu pro Red Hat Enterprise Linux
description: Další informace o přidání podpory pro rozšíření Red Hat Enterprise Extended životní cyklus
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 124ff65087887a437e0b82fbd7b1e4c72e4f7b4c
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684351"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Rozšířená podpora životního cyklu Red Hat Enterprise Linux (RHEL)
Tento článek poskytuje informace o podpoře rozšířeného životního cyklu pro Image Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Životní cyklus Red Hat Enterprise Linux 6
Od 30. listopadu 2020 se Red Hat Enterprise Linux 6 dostane ke konci fáze údržby. Fáze údržby následuje po rozšířené životní fázi. Vzhledem k tomu, že Red Hat Enterprise Linux 6 přechody mimo fáze úplné/údržby, důrazně doporučujeme upgradovat na Red Hat Enterprise Linux 7 nebo 8. Pokud si zákazníci musí zůstat v Red Hat Enterprise Linux 6, doporučujeme přidat doplněk podpora rozšíření ELS (Extended životní cyklus) Red Hat Enterprise Linux.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Používám Red Hat Enterprise Linux 6 a v tuto chvíli se nedá migrovat na novější verzi. Jaké možnosti mám?
* Nadále spouštějte Red Hat Enterprise Linux 6 a Zakupte si podporu rozšířeného životního cyklu (ELS) Add-On úložišť, aby se nadále přijímala omezená údržba softwaru, a technickou podporu (viz postup upgradu a podrobnosti o cenách níže).
* Migrujte na Red Hat Enterprise Linux 7 nebo 8, jakmile budete moct.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Jaké jsou další poplatky za používání doplňku Red Hat Enterprise Linux rozšířené podpory životního cyklu (ELS)?

|Velikost virtuálního počítače|Rámec dodatečných poplatků za čas|Další částka dolaru (USD)| Poznámky|
|---|---|---|---|
| Malý virtuální Host (<= 4 jádra) | Hodinové dodatečné poplatky | TBC | |
|  | Dodatečné poplatky za měsíc | TBC | Pro rezervované instance |
|  | Roční dodatečné poplatky | TBC | Pro rezervované instance |
| Velký virtuální Host (>4 jádra) | Hodinové dodatečné poplatky | TBC | |
|  | Dodatečné poplatky za měsíc | TBC | Pro rezervované instance |
|  | Roční dodatečné poplatky | TBC | Pro rezervované instance |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Jaký je proces přidání úložišť s rozšířeným životním cyklem (ELS), aby nadále přijímala údržbu softwaru (opravy chyb a zabezpečení) a podporovala Red Hat Enterprise Linux 6?

Ukončení procesu registrace do ELS bude brzy k dispozici (nejpozději 30. listopadu 2020).

## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit úplný seznam imagí RHEL v Azure, přečtěte si část [Red Hat Enterprise Linux (RHEL) imagí dostupné v Azure](./redhat-imagelist.md).
* Další informace o infrastruktuře aktualizací Red Hat najdete v tématu [infrastruktura aktualizace Red Hat pro virtuální počítače RHEL na vyžádání v Azure](./redhat-rhui.md).
* Další informace o nabídce BYOS pro RHEL Red Hat Enterprise Linux najdete v článku o tom, jak se v Azure naučíte používat [zlaté image pro vlastní odběry](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).