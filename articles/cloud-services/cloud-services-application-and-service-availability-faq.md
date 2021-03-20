---
title: Nejčastější dotazy týkající se dostupnosti aplikací a služeb
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se dostupnosti aplikací a služeb pro Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742892"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problémy s dostupností aplikací a služeb pro Azure Cloud Services (Classic): nejčastější dotazy

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Tento článek obsahuje nejčastější dotazy týkající se problémů s dostupností aplikací a služeb pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete také na [stránce Cloud Services velikosti virtuálního počítače](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moje role se recykluje. Aktualizovala se nějaká aktualizace pro cloudovou službu?
Přibližně jednou za měsíc vydává společnost Microsoft novou verzi hostovaného operačního systému pro virtuální počítače s Windows Azure PaaS.Hostovaný operační systém je v kanálu jenom jedna aktualizace. Vydaná verze může být ovlivněna mnoha dalšími faktory. Azure se navíc spouští na stovkách tisíc počítačů. Proto není možné předpovědět přesné datum a čas, kdy se vaše role restartují. Informační kanál RSS aktualizace hostovaného operačního systému aktualizujeme nejnovějšími informacemi, ale měli byste zvážit, že nahlášený čas je přibližná hodnota. Upozorňujeme, že to je problematické pro zákazníky a pracuje na plánu, který se má omezit nebo přesně restartovat.

Podrobné informace o nejnovějších aktualizacích operačního systému hosta najdete v tématu [věnovaném vydání hostovaného operačního systému Azure a s maticí kompatibility SDK](cloud-services-guestos-update-matrix.md).

Další informace o restartech a ukazatelích na technické podrobnosti o aktualizacích hostovaného a hostitelského operačního systému najdete v tématu [restart instance role blogu MSDN z důvodu upgradů operačního systému](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Proč je první požadavek na moji cloudovou službu po nějakou dobu nečinný, trvat déle než obvykle?
Když webový server obdrží první požadavek, nejprve znovu zkompiluje kód a potom zpracuje požadavek. To je důvod, proč první požadavek trvá déle než ostatní. Ve výchozím nastavení se fond aplikací vypne v případech nečinnosti uživatele. Ve výchozím nastavení se fond aplikací recykluje každých 1 740 minut (29 hodin).

Fondy aplikací Internetová informační služba (IIS) je možné pravidelně recyklovat, aby nedocházelo k nestabilním stavům, které mohou vést k chybám aplikace, zablokování nebo nevracení paměti.

Následující dokumenty vám pomůžou pochopit a zmírnit tento problém:
* [Opravuje se pomalé počáteční zatížení služby IIS.](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Webová aplikace IIS 7,5 – první požadavek po recyklaci fondu aplikací je velmi pomalý](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Pokud chcete změnit výchozí chování služby IIS, budete muset použít úlohy po spuštění, protože pokud ručně použijete změny instancí webové role, změny se nakonec ztratí.

Další informace najdete v tématu [Konfigurace a spouštění úloh po spuštění pro cloudovou službu](cloud-services-startup-tasks.md).