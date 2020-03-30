---
title: Nejčastější dotazy týkající se problémů s dostupností aplikací a služeb
titleSuffix: Azure Cloud Services
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se dostupnosti aplikací a služeb pro cloudové služby Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386914"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy s dostupností aplikací a služeb pro Cloudové služby Azure: Nejčastější dotazy (nejčastější dotazy)

Tento článek obsahuje nejčastější dotazy týkající se problémů s dostupností aplikací a služeb pro [cloudové služby Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete také na [stránce Velikost virtuálního počítače cloudových služeb.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moje role byla recyklována. Byla pro cloudovou službu zavedena nějaká aktualizace?
Zhruba jednou za měsíc, Microsoft vydává novou verzi hostovaného operačního systému pro Windows Azure PaaS virtuální počítače.Hostovaný operační systém je pouze jedna taková aktualizace v kanálu. Uvolnění může být ovlivněno mnoha dalšími faktory. Kromě toho Azure běží na stovkách tisíc počítačů. Proto není možné předpovědět přesné datum a čas, kdy se vaše role restartují. Informační kanál RSS aktualizace hostovaného operačního systému aktualizujeme o nejnovější informace, které máme, ale měli byste považovat vykazovaný čas za přibližnou hodnotu. Jsme si vědomi toho, že je to pro zákazníky problematické a pracujeme na plánu na omezení nebo přesném časovém restartu.

Úplné podrobnosti o nedávných aktualizacích hostovaného operačního systému najdete v [tématu verze operačního hooperačního systému Azure guest a matice kompatibility sady SDK](cloud-services-guestos-update-matrix.md).

Užitečné informace o restartování a odkazy na technické podrobnosti o aktualizacích hostovaného a hostitelského operačního systému naleznete v příspěvku blogu MSDN, [instance role restartuje z důvodu upgradů operačního systému](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Proč první požadavek na cloudovou službu poté, co byla služba nějakou dobu nečinná, trvá déle než obvykle?
Když webový server obdrží první požadavek, nejprve překompiluje kód a poté požadavek zpracuje. To je důvod, proč první žádost trvá déle než ostatní. Ve výchozím nastavení se fond aplikací vypne v případě nečinnosti uživatele. Fond aplikací bude také recyklut ve výchozím nastavení každých 1 740 minut (29 hodin).

Fondy aplikací Internetové informační služby (IIS) lze pravidelně recyklovat, aby se zabránilo nestabilním stavům, které mohou vést k selhání aplikací, zablokování nebo nevracení paměti.

Následující dokumenty vám pomohou pochopit a zmírnit tento problém:
* [Oprava pomalého počátečního zatížení pro iis](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [První požadavek webové aplikace služby IIS 7.5 po velmi pomalé recyklaci fondu aplikací](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Chcete-li změnit výchozí chování služby IIS, budete muset použít úlohy při spuštění, protože pokud ručně použijete změny v instancích webové role, změny budou nakonec ztraceny.

Další informace naleznete v tématu [Konfigurace a spuštění úloh při spuštění pro cloudovou službu](cloud-services-startup-tasks.md).
