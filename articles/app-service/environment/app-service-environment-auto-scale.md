---
title: Automatické škálování v1
description: Automatické škálování a prostředí služby App Service v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687284"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatické škálování a prostředí služby App Service v1

> [!NOTE]
> Tento článek je o prostředí služby App Service v1.  K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).
> 

Prostředí služby Azure App Service podporují *automatické škálování*. Automatické škálování jednotlivých fondů pracovních procesů můžete škálovat na základě metrik nebo plánu.

![Možnosti automatického škálování pro fond pracovních procesů.][intro]

Automatické škálování optimalizuje využití prostředků automatickým zvětšováním a zmenšováním prostředí služby App Service tak, aby odpovídalo vašemu rozpočtu nebo profilu zatížení.

## <a name="configure-worker-pool-autoscale"></a>Konfigurace automatického škálování fondu pracovních pracovníků
K funkci automatického škálování můžete přistupovat na kartě **Nastavení** fondu pracovních pracovníků.

![Karta Nastavení fondu pracovních pracovníků][settings-scale]

Odtud by rozhraní mělo být poměrně známé, protože je to stejné prostředí, které vidíte při škálování plánu služby App Service. 

![Ruční nastavení měřítka.][scale-manual]

Můžete také nakonfigurovat profil automatického škálování.

![Nastavení automatického škálování.][scale-profile]

Profily automatického škálování jsou užitečné pro nastavení limitů ve vašem měřítku. Tímto způsobem můžete mít konzistentní výkon zkušenosti nastavením dolní mez hodnoty stupnice (1) a předvídatelné utratit limit nastavením horní mez (2).

![Nastavení měřítka v profilu.][scale-profile2]

Po definování profilu můžete přidat pravidla automatického škálování pro zvýšení nebo zmenšení počtu instancí ve fondu pracovních procesů v rámci hranic definovaných profilem. Pravidla automatického škálování jsou založena na metrikách.

![Pravidlo měřítka.][scale-rule]

 K definování pravidel automatického škálování lze použít libovolný fond pracovních pracovníků nebo metriky front-endu. Tyto metriky jsou stejné metriky, které můžete sledovat v grafech blade prostředků nebo nastavit výstrahy pro.

## <a name="autoscale-example"></a>Příklad automatického škálování
Automatické škálování prostředí služby App Service lze nejlépe ilustrovat procházením scénáře.

Tento článek vysvětluje všechny důležité informace při nastavování automatického škálování. Článek vás provede interakcemi, které se hrají, když vezmete v úvahu automatické škálování prostředí služby App Service, která jsou hostovaná v prostředí služby App Service.

### <a name="scenario-introduction"></a>Úvod scénáře
Frank je sysadmin pro podnik, který migroval část úloh, které spravují do prostředí služby App Service.

Prostředí služby App Service je nakonfigurováno takto na ruční škálování takto:

* **Přední části:** 3
* **Fond pracovníků 1**: 10
* **Fond pracovníků 2**: 5
* **Fond pracovníků 3**: 5

Fond pracovních pracovníků 1 se používá pro produkční úlohy, zatímco fond pracovních pracovníků 2 a fond pracovníků 3 se používají pro zajištění kvality (QA) a vývojové úlohy.

Plány služby App Service pro qa a dev jsou nakonfigurovány pro ruční škálování. Produkční plán služby App Service je nastaven na automatické škálování, aby se vypořádal s rozdíly v zatížení a provozu.

Frank je velmi dobře obeznámen s aplikací. Vědí, že špičky pro zatížení jsou mezi 9:00 a 18:00, protože se jedná o obchodní (LOB) aplikace, kterou zaměstnanci používají, když jsou v kanceláři. Využití klesne po tom, když uživatelé jsou hotové pro tento den. Mimo špičku je stále určité zatížení, protože uživatelé mohou přistupovat k aplikaci vzdáleně pomocí svých mobilních zařízení nebo domácích počítačů. Plán produkční služby App Service je již nakonfigurován tak, aby se automaticky škáloval na základě využití procesoru s následujícími pravidly:

![Konkrétní nastavení pro obchodní aplikaci.][asp-scale]

| **Profil automatického škálování – pracovní dny – plán služby App Service** | **Profil automatického škálování – víkendy – plán služby App Service** |
| --- | --- |
| **Název:** Profil dne v týdnu |**Název:** Víkendový profil |
| **Měřítko podle:** Pravidla plánu a výkonu |**Měřítko podle:** Pravidla plánu a výkonu |
| **Profil:** Ve všední dny |**Profil:** Víkend |
| **Typ:** Opakování |**Typ:** Opakování |
| **Cílový rozsah:** 5 až 20 instancí |**Cílový rozsah:** 3 až 10 instancí |
| **Počet dní:** Pondělí, úterý, středa, čtvrtek, pátek |**Počet dní:** Sobota, neděle |
| **Čas zahájení:** 9:00 |**Čas zahájení:** 9:00 |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (škálovat nahoru)** |**Pravidlo automatického škálování (škálovat nahoru)** |
| **Zdroj:** Produkční (prostředí služby App Service) |**Zdroj:** Produkční (prostředí služby App Service) |
| **Metrika:** Procesor % |**Metrika:** Procesor % |
| **Provoz:** Více než 60% |**Provoz:** Více než 80% |
| **Doba trvání:** 5 minut |**Doba trvání:** 10 minut |
| **Agregace času:** Průměrná |**Agregace času:** Průměrná |
| **Akce:** Zvýšit počet o 2 |**Akce:** Zvýšit počet o 1 |
| **Ochlazení (minuty):** 15 |**Ochlazení (minuty):** 20 |
|  | |
| **Pravidlo automatického škálování (zmenšit měřítko)** |**Pravidlo automatického škálování (zmenšit měřítko)** |
| **Zdroj:** Produkční (prostředí služby App Service) |**Zdroj:** Produkční (prostředí služby App Service) |
| **Metrika:** Procesor % |**Metrika:** Procesor % |
| **Provoz:** Méně než 30% |**Provoz:** Méně než 20% |
| **Doba trvání:** 10 minut |**Doba trvání:** 15 minut |
| **Agregace času:** Průměrná |**Agregace času:** Průměrná |
| **Akce:** Snížit počet o 1 |**Akce:** Snížit počet o 1 |
| **Ochlazení (minuty):** 20 |**Ochladit (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Míra inflace plánu služby App Service
Plány služby App Service, které jsou nakonfigurované pro automatické škálování, tak činí maximální rychlostí za hodinu. Tuto sazbu lze vypočítat na základě hodnot uvedených v pravidle automatického škálování.

Pochopení a výpočet *míry inflace plánu služby App Service* je důležité pro automatické škálování prostředí služby App Service, protože změny škálování do fondu pracovních postupů nejsou okamžité.

Míra inflace plánu služby App Service se vypočítá takto:

![Výpočet míry inflace plánu Služby App Service][ASP-Inflation]

Na základě pravidla automatického škálování – škálování pro profil Den v týdnu v plánu produkční služby App Service:

![App Service plán míra inflace pro pracovní dny na základě pravidla automatického škálování – vertikálně navýšit kapacitu.][Equation1]

V případě pravidla automatického škálování – škálování pro víkendový profil plánu produkční služby App Service by vzorec vyřešil:

![App Service plán míra inflace pro víkendy na základě pravidla automatického škálování – vertikálně navýšit kapacitu.][Equation2]

Tuto hodnotu lze také vypočítat pro operace s horizontálním navýšením kapacity.

Na základě pravidla automatického škálování – škálování dolů pro profil den v týdnu v plánu produkční služby App Service by to vypadalo takto:

![App Service plán míra inflace pro pracovní dny na základě pravidla automatického škálování – škálování dolů.][Equation3]

V případě pravidla automatického škálování – škálování dolů pro víkendový profil plánu produkční služby App Service by vzorec vyřešil:  

![App Service plán míra inflace pro víkendy na základě pravidla automatického škálování – škálování dolů.][Equation4]

Produkční plán služby App Service může růst maximální rychlostí osmi instancí za hodinu během týdne a čtyř instancí za hodinu během víkendu. Může uvolnit instance s maximální rychlostí čtyři instance za hodinu během týdne a šest instancí za hodinu během víkendů.

Pokud více plánů služby App Service jsou hostovány ve fondu pracovních procesů, budete muset vypočítat *celkovou míru inflace* jako součet míry inflace pro všechny plány služby App Service, které jsou hostování v tomto fondu pracovních pracovníků.

![Výpočet celkové míry inflace pro více plánů služby App Service hostovaných ve fondu pracovních pracovníků.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Definování pravidel automatického škálování fondu pracovních služeb pomocí míry inflace plánu služby App Service
Pracovní fondy, které jsou hostiteli plánů služby App Service, které jsou nakonfigurovány pro automatické škálování, musí být přidělena vyrovnávací paměť kapacity. Vyrovnávací paměť umožňuje operace automatického škálování zvětšit a zmenšit plán služby App Service podle potřeby. Minimální rezerva by byla vypočtená celková míra inflace plánu služby App Service.

Vzhledem k tomu, že operace škálování prostředí služby App Service nějakou dobu trvat, by měla každá změna účet pro další změny poptávky, které by mohlo dojít, zatímco operace škálování probíhá. Pro přizpůsobení této latence doporučujeme použít vypočtenou míru inflace plánu služby Total App Service jako minimální počet instancí, které jsou přidány pro každou operaci automatického škálování.

Pomocí těchto informací může Frank definovat následující profil a pravidla automatického škálování:

![Automatické škálování pravidel profilu pro příklad LOB.][Worker-Pool-Scale]

| **Profil automatického škálování – pracovní dny** | **Profil automatického škálování – víkendy** |
| --- | --- |
| **Název:** Profil dne v týdnu |**Název:** Víkendový profil |
| **Měřítko podle:** Pravidla plánu a výkonu |**Měřítko podle:** Pravidla plánu a výkonu |
| **Profil:** Ve všední dny |**Profil:** Víkend |
| **Typ:** Opakování |**Typ:** Opakování |
| **Cílový rozsah:** 13 až 25 instancí |**Cílový rozsah:** 6 až 15 instancí |
| **Počet dní:** Pondělí, úterý, středa, čtvrtek, pátek |**Počet dní:** Sobota, neděle |
| **Čas zahájení:** 7:00 |**Čas zahájení:** 9:00 |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (škálovat nahoru)** |**Pravidlo automatického škálování (škálovat nahoru)** |
| **Zdroj:** Fond pracovníků 1 |**Zdroj:** Fond pracovníků 1 |
| **Metrika:** PracovníciK dispozici |**Metrika:** PracovníciK dispozici |
| **Provoz:** Méně než 8 |**Provoz:** Méně než 3 |
| **Doba trvání:** 20 minut |**Doba trvání:** 30 minut |
| **Agregace času:** Průměrná |**Agregace času:** Průměrná |
| **Akce:** Zvýšit počet o 8 |**Akce:** Zvýšit počet o 3 |
| **Ochlazení (minuty):** 180 |**Ochlazení (minuty):** 180 |
|  | |
| **Pravidlo automatického škálování (zmenšit měřítko)** |**Pravidlo automatického škálování (zmenšit měřítko)** |
| **Zdroj:** Fond pracovníků 1 |**Zdroj:** Fond pracovníků 1 |
| **Metrika:** PracovníciK dispozici |**Metrika:** PracovníciK dispozici |
| **Provoz:** Větší než 8 |**Provoz:** Větší než 3 |
| **Doba trvání:** 20 minut |**Doba trvání:** 15 minut |
| **Agregace času:** Průměrná |**Agregace času:** Průměrná |
| **Akce:** Snížit počet o 2 |**Akce:** Snížit počet o 3 |
| **Ochlazení (minuty):** 120 |**Ochlazení (minuty):** 120 |

Rozsah cíl definovaný v profilu se vypočítá podle minimálních instancí definovaných v profilu pro plán služby App Service + vyrovnávací paměti.

Maximální rozsah by součet všech maximální rozsahy pro všechny plány služby App Service hostované ve fondu pracovních procesů.

Počet zvýšení pro pravidla škálování by měla být nastavena alespoň 1x app service plán míra inflace pro škálování nahoru.

Snížení počtu lze upravit na něco mezi 1/2X nebo 1X App Service Plan Míra inflace pro škálování.

### <a name="autoscale-for-front-end-pool"></a>Automatické škálování pro front-endový fond
Pravidla pro automatické škálování front-endu jsou jednodušší než pro fondy pracovních procesů. V první řadě byste měli  
Ujistěte se, že doba trvání měření a časovače cooldown uvažují, že operace škálování v plánu služby App Service nejsou okamžité.

V tomto scénáři Frank ví, že míra chyb se zvyšuje po front-endy dosáhnout 80% využití procesoru a nastaví pravidlo automatického škálování pro zvýšení instance takto:

![Nastavení automatického škálování pro front-endový fond.][Front-End-Scale]

| **Profil automatického škálování – přední konce** |
| --- |
| **Název:** Automatické škálování – přední konce |
| **Měřítko podle:** Pravidla plánu a výkonu |
| **Profil:** Každodenní |
| **Typ:** Opakování |
| **Cílový rozsah:** 3 až 10 instancí |
| **Počet dní:** Každodenní |
| **Čas zahájení:** 9:00 |
| **Časové pásmo:** UTC-08 |
|  |
| **Pravidlo automatického škálování (škálovat nahoru)** |
| **Zdroj:** Front-end bazén |
| **Metrika:** Procesor % |
| **Provoz:** Více než 60% |
| **Doba trvání:** 20 minut |
| **Agregace času:** Průměrná |
| **Akce:** Zvýšit počet o 3 |
| **Ochlazení (minuty):** 120 |
|  |
| **Pravidlo automatického škálování (zmenšit měřítko)** |
| **Zdroj:** Fond pracovníků 1 |
| **Metrika:** Procesor % |
| **Provoz:** Méně než 30% |
| **Doba trvání:** 20 minut |
| **Agregace času:** Průměrná |
| **Akce:** Snížit počet o 3 |
| **Ochlazení (minuty):** 120 |

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
