---
title: Upgrade operačního systému pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Proveďte upgrade operačního systému pro SAP HANA v Azure (velké instance)
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
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100750"
---
# <a name="operating-system-upgrade"></a>Upgrade operačního systému
Tento dokument popisuje podrobnosti pro upgrady operačního systému na velkých instancích HANA.

>[!NOTE]
>Upgrade operačního systému je zodpovědností zákazníků, podpora v nástroji Microsoft operations může vás provede hlavními k dávejte pozor při upgradu. Předtím, než máte v plánu pro účely upgradu, by se měli obrátit dodavatele operačního systému.

Při zřizování jednotek HLI provozní tým Microsoft instalace operačního systému. V čase, budete muset udržovat operačního systému (Příklad: Opravy chyb, ladění, upgrade atd.) na jednotce HLI.

Předtím, než hlavní změny v operačním systému (například Upgrade SP1 na SP2), musí kontaktovat tým Microsoft Operations tak, že otevřete lístek podpory poradit.

Uveďte prosím v lístku:

* HLI ID předplatného.
* Název vašeho serveru.
* Úroveň oprav, které máte v úmyslu použít.
* Datum, plánujete tuto změnu. 

Doporučujeme by, že otevřete tento lístek alespoň týden před datem žádoucí upgradu z důvodu existence provozního týmu, kontrole, jestli se upgrade firmwaru, bude nutné v okně serveru.


Matice podpory různých verzí SAP HANA s různými verzemi systému Linux, najdete v části [2235581 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Známé problémy

Tady jsou známo několik běžných problémů při upgradu:
- Ve třídě SKU typu II skladovou Položku bude odebrán software foundation softwaru (SFS), po upgradu operačního systému. Po upgradu operačního systému, je třeba přeinstalovat SFS kompatibilní.
- Ethernet ovladače karty (ENIC a FNIC) vrátit zpátky na starší verzi. Po upgradu je třeba přeinstalovat kompatibilní verzi ovladače.

## <a name="next-steps"></a>Další postup
- Přečtěte si [zálohování a obnovení](hana-overview-high-availability-disaster-recovery.md) pro operační systém zálohování třídy typu I SKU.
- Přečtěte si [zálohování operačního systému pro SKU typu II](os-backup-type-ii-skus.md) pro SKU typu II třídu.
