---
title: Aplikace a problémy s dostupností služby pro Microsoft Azure Cloud Services – nejčastější dotazy | Microsoft Docs
description: V tomto článku jsou uvedené nejčastější dotazy o aplikaci a dostupnost služeb pro Microsoft Azure Cloud Services.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 49576aa99f6cd505648e33348b89e502bea9d5c4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068109"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Aplikace a problémy s dostupností služby pro Azure Cloud Services: Časté otázky (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se aplikace a problémy s dostupností služby pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Můžete také obrátit [cloudové služby virtuálních počítačů velikost stránky](cloud-services-sizes-specs.md) velikost informace.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moje role tu recykluje. Se nějakou aktualizaci nasazuje pro moje Cloudová služba?
Přibližně jednou za měsíc, společnost Microsoft vydá nové verze hostovaného operačního systému pro virtuální počítače Windows Azure PaaS. Hostovaného operačního systému je jenom jedna taková aktualizace v kanálu. Verze může mít vliv mnoho dalších faktorů. Kromě toho Azure je spuštěná na stovky tisíc počítačů. Proto není možné předpovědět přesné datum a čas, kdy se restartuje vaše role. Aktualizujeme s nejnovější informace, které jsme hostovaného operačního systému aktualizujte informačního kanálu RSS, ale měli byste zvážit, hlášena čas přibližnou hodnotu. Jsme víte, že se jedná o problém pro zákazníky a práce na plán limit nebo přesněji čas restartování.

Kompletní informace o nejnovějších aktualizacích hostovaného operačního systému najdete v tématu [verze hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).

Užitečné informace o restartování a odkazy na technické informace o aktualizace hosta a hostitelským operačním systémem, najdete v příspěvku blogu MSDN [Role Instance restartuje kvůli upgrady operačního systému](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Proč první požadavek na můj cloudové služby po služby bylo nečinné po určitý čas trvat déle než obvykle?
Když webový Server obdrží požadavek na první, nejprve znovu zkompiluje kód a potom zpracuje žádost. To je důvod, proč první žádost trvá déle než jiné. Ve výchozím nastavení získá fondu aplikací v případech nečinnosti uživatele vypnout. Fond aplikací bude taky recyklaci ve výchozím nastavení každých 1,740 minut (29 hodiny).

Aplikace Internetové informační služby (IIS), který fondy může být pravidelně recykluje, aby se zabránilo nestabilním stavy, které můžou vést k aplikaci spadne, zablokuje, nebo nevracení paměti.

V následujících dokumentech vám pomůže pochopit a zmírnění tohoto problému:
* [Oprava pomalé počáteční zatížení pro služby IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Aplikace webové služby IIS 7.5 po recyklaci fondu aplikací je velmi pomalé první požadavek.](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Pokud chcete změnit výchozí chování služby IIS, musíte pro použití při spuštění úloh, protože pokud ručně použít změny instance webových rolí, změny se nakonec ztratí.

Další informace najdete v tématu [postupy pro konfiguraci a spuštění úlohy spuštění pro cloudové služby](cloud-services-startup-tasks.md).
