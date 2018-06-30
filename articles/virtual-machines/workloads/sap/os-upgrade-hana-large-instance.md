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
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114588"
---
# <a name="operating-system-upgrade"></a>Upgrade operačního systému
Tento dokument popisuje podrobnosti na upgrady operačního systému v rámci instancí velké HANA.

>[!NOTE]
>Upgrade operačního systému je zodpovědností zákazníků, operace podporu společnosti Microsoft můžete navést na klíčové oblasti, které je sledujte během upgradu. Byste se měli obrátit dodavatele operačního systému před naplánování pro upgrade.

V době HLI jednotky zřizování provozní tým Microsoft instalace operačního systému. V čase, mají povinnost zachovávat operačního systému (Příklad: opravy, ladění, upgrade atd.) na jednotce HLI.

Před hlavní změny v operačním systému (například Upgrade SP1 na SP2), obraťte se na tým Microsoft Operations otevřením lístku podpory prostudovat.


Na matici podpory různých verzí SAP HANA s různými verzemi systému Linux, najdete v části [2235581 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Známé problémy

Následuje několik běžných známé problémy v během upgradu:
- U třídy SKU typu II SKU odeberou se software foundation softwaru (SFS) po upgradu operačního systému. Po upgradu operačního systému, je třeba přeinstalovat SFS kompatibilní.
- Ovladače karty Ethernet (ENIC a FNIC) vrátit zpět na starší verze. Po dokončení upgradu znovu nainstalujte kompatibilní verzi ovladače.

## <a name="next-steps"></a>Další postup
- Odkazovat [zálohování a obnovení](hana-overview-high-availability-disaster-recovery.md) pro operační systém zálohování – třída typu I SKU.
- Odkazovat [zálohování operačního systému pro typ II SKU](os-backup-type-ii-skus.md) pro třídu typu II SKU.