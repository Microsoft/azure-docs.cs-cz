---
title: Upgrade operačního systému pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Provedení upgradu operačního systému pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa88e45f2523dd65c4f714bfeab1c0eda401d720
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869155"
---
# <a name="operating-system-upgrade"></a>Upgrade operačního systému
Tento dokument popisuje podrobnosti o upgradech operačního systému ve velkých instancích HANA.

>[!NOTE]
>Upgrade operačního systému je zodpovědností zákazníků. Microsoft Operations support vám může podávat klíčové oblasti, které vám při upgradu můžou sledovat. Před plánováním upgradu byste se měli obrátit na dodavatele operačního systému.

V současné době zřizování jednotky HLI operační systém Microsoft Operations Team nainstaluje operační systém. V průběhu času se vyžaduje údržba operačního systému (příklad: Opravy, optimalizace, upgrade atd.) na jednotce HLI

Před prováděním podstatných změn v operačním systému (například upgrade SP1 na verzi SP2) musíte kontaktovat tým Microsoft Operations, a to tak, že si spustíte lístek podpory.

Zahrnout do lístku:

* ID předplatného HLI
* Název vašeho serveru.
* Úroveň opravy, kterou plánujete použít.
* Datum, kdy plánujete tuto změnu. 

Doporučujeme, abyste tento lístek otevřeli aspoň jeden týden před tím, než je vhodné datum aktualizace kvůli kontrole, jestli bude v okně serveru nutné upgradovat firmware.


Pro matrici podpory různých verzí SAP HANA s různými verzemi systému Linux najdete informace v tématu [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Známé problémy

V následující části najdete několik běžných známých problémů během upgradu:
- U SKU třídy b typu SKU se Software Foundation software (SFS) odebere po upgradu operačního systému. Po upgradu operačního systému je nutné přeinstalovat kompatibilní SFS.
- Ovladače karet Ethernet (ENIC a FNIC) se vrátily zpátky na starší verzi. Po upgradu musíte přeinstalovat kompatibilní verze ovladačů.

## <a name="next-steps"></a>Další kroky
- Přečtěte si téma [zálohování a obnovení](hana-overview-high-availability-disaster-recovery.md) pro třídu SKU typu zálohování operačního systému.
- Přečtěte si [zálohování operačního systému pro položky typu II SKU revize 3](os-backup-type-ii-skus.md) pro třídu SKU typu II.
