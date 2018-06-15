---
title: Automatické škálování a služby App Service Environment v1
description: Automatické škálování a služby App Service Environment
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 0feb6e4862f643c35a58c0321181bdda22b032e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837131"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatické škálování a služby App Service Environment v1

> [!NOTE]
> Tento článek je o v1 App Service Environment.  Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

Podpora prostředí Azure App Service *automatické škálování*. Můžete fondy škálování jednotlivých pracovních procesů na základě metriky nebo plán.

![Možnosti škálování fondu pracovních procesů.][intro]

Automatické škálování optimalizuje vaše využití prostředků pomocí automatické zvětšování a zmenšování služby App Service environment přizpůsobit rozpočet nebo načíst profil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurace pracovního procesu škálování fondu
Může přistupovat k funkcím škálování z **nastavení** ve fondu pracovních procesů.

![Karta nastavení fondu pracovních procesů.][settings-scale]

Z tohoto místa rozhraní by měl být poměrně obeznámeni, protože je stejné prostředí, které vidíte, kdy škálovat plán služby App Service. 

![Nastavení ruční škálování.][scale-manual]

Můžete také nakonfigurovat profilem škálování.

![Nastavení automatického škálování.][scale-profile]

Profilů automatického škálování jsou užitečné můžete nastavit omezení pro vaše škálování. Tímto způsobem může mít konzistentní výkon prostředí nastavením hodnotu dolní mez rozsahu (1) a předvídatelný výdaji cap nastavením horní mez (2).

![Nastavení škálování v profilu.][scale-profile2]

Po definování profilu, můžete přidat pravidla automatického škálování se škálovat nahoru nebo dolů počet instancí ve fondu pracovních procesů v rámci hranice definované profilem. Pravidla automatického škálování jsou založené na metriky.

![Pravidlo škálování.][scale-rule]

 Všechny fondu pracovních procesů nebo front-end metriky lze použít k definování pravidel škálování. Tyto metriky jsou stejné metriky můžete sledovat v okně grafy zdrojů nebo nastavit výstrahy pro.

## <a name="autoscale-example"></a>Příklad škálování
Škálování služby App Service environment lze ukázat nejlépe ve scénáři s návodem.

Tento článek vysvětluje všechny nezbytné informace při nastavení automatického škálování. Článek vás provede procesem interakce, které se vyskytnou se, když je zvážit automatické škálování služby App Service Environment, které jsou hostované v App Service Environment.

### <a name="scenario-introduction"></a>Scénář Úvod
František se správce systému pro organizace, která byla migrována část zátěží, které spravuje do služby App Service environment.

Prostředí služby App Service je ruční škálování nakonfigurovány takto:

* **Front-zakončení:** 3
* **Fond pracovních procesů 1**: 10
* **Fond pracovních procesů 2**: 5
* **Fond pracovních procesů 3**: 5

Fond pracovních procesů 1 se používá pro úlohy v produkčním prostředí při fondu pracovních procesů 2 a fondu pracovních procesů 3 se používají pro zajištění kvality (QA) a vývoj úlohy.

Plány služby App Service pro kontrolu kvality a vývojářů je nakonfigurován pro ruční škálování. Provozní plán služby App Service je nastavena na automatické škálování jak nakládat s rozdíly v zatížení a provozu.

Jan je velmi dobře známé s aplikací. Ví, že špičky pro zatížení jsou mezi 9:00:00 a 18:00:00, protože to je – obchodní (LOB) aplikace, která zaměstnanci používají době, kdy jsou v kanceláři v. Využití zahodí po, když se uživatelé provádějí pro daný den. Mimo dobu ve špičce přetrvává některé zatížení vzhledem k tomu, že uživatelé mají přístup ke aplikaci vzdáleně pomocí jejich mobilní zařízení nebo domácích počítačů. Provozní plán služby App Service je již nakonfigurována na automatické škálování podle využití procesoru na základě následujících pravidel:

![Konkrétní nastavení pro obchodní aplikace.][asp-scale]

| **Škálování profil – dny v týdnu – plán služby App Service** | **Škálování profil – víkendů – plán služby App Service** |
| --- | --- |
| **Název:** profil den v týdnu |**Název:** víkendu profilu |
| **Škálování podle:** pravidla plánu a výkonu |**Škálování podle:** pravidla plánu a výkonu |
| **Profil:** dny v týdnu |**Profil:** víkendu |
| **Typ:** opakování |**Typ:** opakování |
| **Cílový rozsah:** 5 až 20 instancí |**Cílový rozsah:** 3 až 10 instancí |
| **Počet dnů:** pondělí, úterý, středu, čtvrtek a pátek |**Počet dnů:** sobota, neděle |
| **Čas spuštění:** 9:00:00 |**Čas spuštění:** 9:00:00 |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |**Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |
| **Prostředek:** produkční (služby App Service Environment) |**Prostředek:** produkční (služby App Service Environment) |
| **Metrika:** % využití procesoru |**Metrika:** % využití procesoru |
| **Operace:** větší než 60 % |**Operace:** větší než 80 % |
| **Doba trvání:** 5 minut |**Doba trvání:** 10 minut |
| **Čas agregace:** průměrná |**Čas agregace:** průměrná |
| **Akce:** zvýšit počet 2 |**Akce:** zvýšit počet 1 |
| **Nástrojů dolů (minuty):** 15 |**Nástrojů dolů (minuty):** 20 |
|  | |
| **Pravidlo automatického škálování (škálování dolů)** |**Pravidlo automatického škálování (škálování dolů)** |
| **Prostředek:** produkční (služby App Service Environment) |**Prostředek:** produkční (služby App Service Environment) |
| **Metrika:** % využití procesoru |**Metrika:** % využití procesoru |
| **Operace:** méně než 30 % |**Operace:** méně než 20 % |
| **Doba trvání:** 10 minut |**Doba trvání:** 15 minut |
| **Čas agregace:** průměrná |**Čas agregace:** průměrná |
| **Akce:** snižte počet 1 |**Akce:** snižte počet 1 |
| **Nástrojů dolů (minuty):** 20 |**Nástrojů dolů (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Míry inflace plán služby App Service
Plány služby App Service, které jsou nakonfigurované na automatické škálování to provést s maximální rychlostí za hodinu. Tento kurz, lze vypočítat na základě hodnot zadaných pro pravidlo škálování.

Princip fungování a způsob výpočtu *míry inflace plán služby App Service* je důležité pro škálování prostředí služby App Service, protože nejsou okamžitou škálování změny fondu pracovních procesů.

Míry inflace plán služby App Service se vypočítává takto:

![Výpočet míry inflace plán služby App Service.][ASP-Inflation]

Podle škálování – pravidlo vertikálně navýšit kapacitu pro profil den v týdnu provozních plán služby App Service:

![Míra inflace plán služby App Service pro dny v týdnu podle škálování – pravidlo vertikálně navýšit kapacitu.][Equation1]

V případě škálování – pravidlo vertikálně navýšit kapacitu pro profil víkendu provozních plán služby App Service, by vzorec odkazující na:

![Míry inflace plán služby App Service pro víkendů podle škálování – pravidlo vertikálně navýšit kapacitu.][Equation2]

Tato hodnota může také vypočítá operacím vertikální snížení kapacity.

Podle škálování – pravidlo škálování dolů profilu den v týdnu provozních plán služby App Service, to vypadat takto:

![Míra inflace plán služby App Service pro dny v týdnu podle škálování – pravidlo škálování dolů.][Equation3]

V případě škálování – pravidlo škálování dolů profilu víkendu provozních plán služby App Service, by vzorec odkazující na:  

![Rychlost inflace plán služby App Service pro víkendů podle škálování – pravidlo škálování dolů.][Equation4]

Provozní plán služby App Service můžou růst v maximálně osm instancí za hodinu v týdnu a čtyři instance za hodinu během víkendu. Můžete ho verzi instance s maximální rychlostí čtyři instancí za hodinu v týdnu a šesti instancí za hodinu během víkendu.

Pokud víc plány služby App Service se hostovaným ve fondu pracovních procesů, je nutné vypočítat *celkovou rychlost inflace* jako součet inflace rychlost pro všechny plány služby App Service, které se hostování v tomto fondu pracovních procesů.

![Celková rychlost inflace výpočet pro víc plány služby App Service hostované ve fondu pracovních procesů.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Použijte k definování pravidel škálování fondu pracovní míry inflace plán služby App Service
Pracovní fondu tohoto hostitele plány služby App Service, které jsou nakonfigurované pro škálování nutné přidělit vyrovnávací paměť kapacity. Vyrovnávací paměť umožňuje operace škálování, které mají zvýšit nebo snížit plán služby App Service, podle potřeby. Minimální velikost vyrovnávací paměti by počítané celková aplikace služby plánování inflace rychlost.

Protože operací škálování služby App Service environment trvat delší dobu použít, všechny změny by měl účet pro vyžádání další změny, které může nastat, když probíhá operace škálování. Abychom vyhověli tato čekací doba, doporučujeme použít počítaný celková aplikace služby plánování inflace rychlost jako minimální počet instancí, které jsou přidány pro každou operaci škálování.

Tyto informace můžete definovat František následující škálování profil a pravidla:

![Profil pravidel škálování například LOB.][Worker-Pool-Scale]

| **Škálování profil – dny v týdnu** | **Škálování profil – víkendů** |
| --- | --- |
| **Název:** profil den v týdnu |**Název:** víkendu profilu |
| **Škálování podle:** pravidla plánu a výkonu |**Škálování podle:** pravidla plánu a výkonu |
| **Profil:** dny v týdnu |**Profil:** víkendu |
| **Typ:** opakování |**Typ:** opakování |
| **Cílový rozsah:** 13 až 25 instancí |**Cílový rozsah:** 6 až 15 instancí |
| **Počet dnů:** pondělí, úterý, středu, čtvrtek a pátek |**Počet dnů:** sobota, neděle |
| **Čas spuštění:** 7:00:00 |**Čas spuštění:** 9:00:00 |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |**Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |
| **Prostředek:** pracovní fond 1 |**Prostředek:** pracovní fond 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Operace:** menší než 8 |**Operace:** menší než 3 |
| **Doba trvání:** 20 minut |**Doba trvání:** 30 minut |
| **Čas agregace:** průměrná |**Čas agregace:** průměrná |
| **Akce:** zvýšit počet 8 |**Akce:** zvýšení počtu 3 |
| **Nástrojů dolů (minuty):** 180 |**Nástrojů dolů (minuty):** 180 |
|  | |
| **Pravidlo automatického škálování (škálování dolů)** |**Pravidlo automatického škálování (škálování dolů)** |
| **Prostředek:** pracovní fond 1 |**Prostředek:** pracovní fond 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Operace:** větší než 8 |**Operace:** větší než 3 |
| **Doba trvání:** 20 minut |**Doba trvání:** 15 minut |
| **Čas agregace:** průměrná |**Čas agregace:** průměrná |
| **Akce:** snižte počet 2 |**Akce:** snížit počet 3 |
| **Nástrojů dolů (minuty):** 120. |**Nástrojů dolů (minuty):** 120. |

Minimální instance definované v profilu pro plán služby App Service + vyrovnávací paměti je vypočítána cílový rozsah definovaný v profilu.

Maximální rozsah by součet všechny rozsahy maximální pro všechny plány služby App Service hostované ve fondu pracovních procesů.

Zvyšte počet pro rozšiřování škálování využívajících pravidla měli nastavit na alespoň 1 X míry inflace plán služby App škálování.

Snižte počet lze upravit na jinou hodnotu mezi 1/2 X nebo 1 X míry inflace plán služby App škálování směrem dolů.

### <a name="autoscale-for-front-end-pool"></a>Škálování front-endu fondu
Pravidla pro automatické škálování front-endu jsou jednodušší než pro fondy pracovních procesů. Především se stává měli byste  
Ujistěte se, že doba trvání měření a časovače cooldown zvažte, že nejsou okamžitou operací škálování na plán služby App Service.

V tomto scénáři František ví, že míra chyb zvyšuje po dosažení front-end 80 % využití CPU a nastaví pravidlo škálování zvýšit instance následujícím způsobem:

![Nastavení automatického škálování front-endu fondu.][Front-End-Scale]

| **Škálování profil – přední končí** |
| --- |
| **Název:** škálování – přední končí |
| **Škálování podle:** pravidla plánu a výkonu |
| **Profil:** každý den |
| **Typ:** opakování |
| **Cílový rozsah:** 3 až 10 instancí |
| **Počet dnů:** každý den |
| **Čas spuštění:** 9:00:00 |
| **Časové pásmo:** UTC-08 |
|  |
| **Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |
| **Prostředek:** Front-end fondu |
| **Metrika:** % využití procesoru |
| **Operace:** větší než 60 % |
| **Doba trvání:** 20 minut |
| **Čas agregace:** průměrná |
| **Akce:** zvýšení počtu 3 |
| **Nástrojů dolů (minuty):** 120. |
|  |
| **Pravidlo automatického škálování (škálování dolů)** |
| **Prostředek:** pracovní fond 1 |
| **Metrika:** % využití procesoru |
| **Operace:** méně než 30 % |
| **Doba trvání:** 20 minut |
| **Čas agregace:** průměrná |
| **Akce:** snížit počet 3 |
| **Nástrojů dolů (minuty):** 120. |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
