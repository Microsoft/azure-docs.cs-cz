---
title: Upgrade operačního systému pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Provedení upgradu operačního systému pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6af1a6612360c2433c05a7add79d2e7b3b9d754
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658256"
---
# <a name="operating-system-upgrade"></a>Upgrade operačního systému
Tento dokument popisuje podrobnosti na upgrady operačního systému v rámci instancí velké HANA.

>[!NOTE]
>Upgrade operačního systému je zodpovědností zákazníků, operace podporu společnosti Microsoft můžete navést na klíčové oblasti, které je sledujte během upgradu. Byste se měli obrátit dodavatele operačního systému před naplánování pro upgrade.

V době HLI jednotky zřizování provozní tým Microsoft instalace operačního systému. V čase, mají povinnost zachovávat operačního systému (Příklad: opravy, ladění, upgrade atd.) na jednotce HLI.

Před hlavní změny v operačním systému (například Upgrade operačního systému), můžete **musí** zvažte následující matice kompatibility. Můžete **musí** také kontaktovat tým Microsoft Operations otevřením lístku podpory prostudovat před zahájením činnosti hlavní operačního systému, jako například upgrade.

Na matici podpory různých verzí SAP HANA s různými verzemi systému Linux, najdete v části [2235581 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2235581).

Následující kompatibility byl testovaný na HLIs. Pokud váš server HLI mimo matice kompatibility, obraťte se na podporu společnosti Microsoft operaci.

## <a name="for-type-i-class-sku-category"></a>Pro typ I třídy kategorie SKU

| Konfigurace | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Server firmwaru | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC verze | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC verze | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Zakázáno | Zakázáno | Zakázáno | Zakázáno |
| Verze jádra | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.X86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Pro kategorii SKU typu II – třída

| Konfigurace | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Verze firmwaru RMC | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| Verze firmwaru BMC | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Verze softwaru Foundation Server (SFS) | 2,16    | 2,16    | 2.14/2.16   | 2,16   |
| SYSTÉMU BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e verze    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Verze jádra    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Známé problémy

Následuje několik běžných známé problémy v během upgradu:
- U třídy SKU typu II SKU odeberou se software foundation softwaru (SFS) po upgradu operačního systému. Po upgradu operačního systému, je třeba přeinstalovat SFS kompatibilní.
- Ovladače karty Ethernet (ENIC a FNIC) vrátit zpět na starší verze. Po dokončení upgradu znovu nainstalujte kompatibilní verzi ovladače.

## <a name="next-steps"></a>Další postup
- Odkazovat [zálohování a obnovení](hana-overview-high-availability-disaster-recovery.md) pro operační systém zálohování – třída typu I SKU.
- Odkazovat [zálohování operačního systému pro typ II SKU](os-backup-type-ii-skus.md) pro třídu typu II SKU.