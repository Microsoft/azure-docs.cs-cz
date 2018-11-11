---
title: Aplikace a problémy s dostupností služby pro Microsoft Azure Cloud Services – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje nejčastější dotazy o dostupnost aplikací a služeb pro Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 68f3fe0dbd16d3d71dc66af05de6f90156d67dfa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246357"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Aplikace a problémy s dostupností služby pro Azure Cloud Services: Nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy o aplikaci a problémy s dostupností služby pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Můžete také najdete [cloudové služby virtuálních počítačů velikosti stránky](cloud-services-sizes-specs.md) pro informace o velikosti.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moje role je teď recyklován. Pokusil se žádné aktualizace zavádí pro svojí cloudové služby?
Přibližně jednou za měsíc, společnost Microsoft vydává nové verze hostovaného operačního systému pro virtuální počítače s Windows Azure PaaS. Hostovaný operační systém je pouze jeden tyto aktualizace v kanálu. Verze může být ovlivněna řadu dalších faktorů. Kromě toho Azure běží na stovky tisíc počítačů. Proto není možné předpovědět přesné datum a čas, kdy se role restartuje. Aktualizujeme hostovaného operačního systému aktualizujte informační kanál RSS s nejnovějšími informacemi, který máme k dispozici, ale byste měli zvážit, který hlášena době přibližné hodnoty. Víme, že se jedná o problematické pro zákazníky a pracují na plán na omezení nebo přesně čas restartování.

Úplné podrobnosti o nedávných aktualizacích hostovaného operačního systému naleznete v tématu [SDK kompatibility přehled verzí hostovaného operačního systému Azure a](cloud-services-guestos-update-matrix.md).

Užitečné informace o restartování a odkazy na technické podrobnosti aktualizace hosta a hostitelským operačním systémem, najdete v příspěvku blogu MSDN [Role Instance se restartuje kvůli upgrady operačního systému](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Proč první požadavek na svojí cloudové služby až služby byl nečinný nechystáte nějakou dobu trvat déle než obvykle?
První žádost o přijetí Webový Server nejprve znovu zkompiluje kód a poté zpracuje požadavek. To je důvod, proč první požadavek trvá déle, než ostatní. Ve výchozím nastavení získá fond aplikací v případě nečinnosti uživatele vypnout. Fond aplikací se také recyklaci ve výchozím nastavení každých 1,740 minut (29 hodin).

Aplikace Internetové informační služby (IIS), které fondy mohou být pravidelně recyklovat, aby se zabránilo nestabilitě stavy, které můžou vést k aplikaci dojde k chybě, zablokování nebo nevracení paměti.

Následující dokumenty vám pomůžou pochopit a tento problém zmírnit:
* [Oprava pomalé počátečním načtení pro službu IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [První požadavek službu IIS 7.5 webové aplikace po recyklaci fondu aplikací velmi pomalé.](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Pokud chcete změnit výchozí chování služby IIS, je potřeba pomocí úlohy po spuštění, protože pokud ručně aplikovat změny na instance webové Role, změny se nakonec ztratí.

Další informace najdete v tématu [jak nakonfigurovat a spustit úlohy po spuštění pro cloudovou službu](cloud-services-startup-tasks.md).
