---
title: Automatické škálování v1
description: Automatické škálování a App Service Environment v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74687284"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatické škálování a App Service Environment v1

> [!NOTE]
> Tento článek se týká App Service Environment v1.  Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).
> 

Prostředí Azure App Service podporují automatické *škálování*. Můžete automatické škálování jednotlivých fondů pracovních procesů na základě metrik nebo plánu.

![Možnosti automatického škálování pro fond pracovních procesů.][intro]

Automatické škálování optimalizuje vaše využití prostředků tím, že automaticky zvětšuje a zmenšuje App Service prostředí tak, aby odpovídalo vašemu rozpočtu a nebo zátěži profilu.

## <a name="configure-worker-pool-autoscale"></a>Konfigurovat automatické škálování fondu pracovních procesů
K funkcím automatického škálování můžete přistupovat z karty **Nastavení** fondu pracovních procesů.

![Karta nastavení fondu pracovních procesů.][settings-scale]

Odtud by mělo být rozhraní dostatečně známé, protože se jedná o stejné prostředí, které vidíte při škálování plánu App Service. 

![Nastavení ručního škálování.][scale-manual]

Můžete také nakonfigurovat profil automatického škálování.

![Nastavení automatického škálování.][scale-profile]

Profily automatického škálování jsou užitečné pro nastavení omezení pro vaši škálu. Tímto způsobem můžete zajistit konzistentní výkon pomocí nastavení nižší meze škálování (1) a předvídatelného limitu útraty tím, že nastavíte horní mez (2).

![Nastavení měřítka v profilu.][scale-profile2]

Po definování profilu můžete přidat pravidla automatického škálování pro horizontální navýšení nebo snížení počtu instancí ve fondu pracovních procesů v mezích definovaných profilem. Pravidla automatického škálování jsou založena na metrikách.

![Pravidlo škálování.][scale-rule]

 K definování pravidel automatického škálování můžete použít jakýkoliv fond pracovních procesů nebo metriky front-endu. Tyto metriky jsou stejné metriky, které můžete monitorovat v grafech okna prostředků nebo nastavovat výstrahy pro.

## <a name="autoscale-example"></a>Příklad automatického škálování
Automatické škálování App Serviceho prostředí se dá nejlépe vymezit prostřednictvím scénáře.

Tento článek vysvětluje všechny nezbytné předpoklady při nastavení automatického škálování. Tento článek vás provede interakcemi, které přicházejí do hry při automatickém škálování App Service prostředí, která jsou hostovaná v App Service Environment.

### <a name="scenario-introduction"></a>Úvod do scénáře
Jan je sysadmin pro podnik, který migrovali část úloh, které spravují do App Serviceho prostředí.

Prostředí App Service je nakonfigurované tak, aby manuální škálování bylo následující:

* **Přední zakončení:** 3
* **Fond pracovních procesů 1**: 10
* **Fond pracovních procesů 2**: 5
* **Fond pracovních procesů 3**: 5

Fond pracovních procesů 1 se používá pro produkční úlohy, zatímco fond pracovních procesů 2 a fond pracovních procesů 3 se používá pro úlohy pro zajištění kvality (QA) a vývojové úlohy.

Plány App Service pro QA a vývoj jsou nakonfigurovány na ruční škálování. Produkční App Service plán je nastaven na automatické škálování, aby bylo možné zabývat se kolísáním zatížení a provozu.

Jan je s aplikací velmi obeznámený. Ví, že špička zatížení je mezi 9:00 a 6:00 PM, protože se jedná o obchodní aplikaci (LOB), kterou zaměstnanci používají, když jsou v kanceláři. Využití se po dobu, kdy se uživatelé pro daný den provedlo, poklesne. Mimo špičku se zatížení stále používá, protože uživatelé mají přístup k aplikaci vzdáleně pomocí jejich mobilních zařízení nebo domácích počítačů. Plán produkčního App Service je už nakonfigurovaný na automatické škálování na základě využití CPU s těmito pravidly:

![Konkrétní nastavení pro obchodní aplikaci][asp-scale]

| **Profil automatického škálování – pracovní dny – App Service plán** | **Profil automatického škálování – víkendy – App Service plán** |
| --- | --- |
| **Název:** Profil dne v týdnu |**Název:** Profil víkendu |
| **Škálovat podle:** Pravidla plánování a výkonu |**Škálovat podle:** Pravidla plánování a výkonu |
| **Profil:** Všední dny |**Profil:** Volné |
| **Zadejte:** Vzorec |**Zadejte:** Vzorec |
| **Cílový rozsah:** 5 až 20 instancí |**Cílový rozsah:** 3 až 10 instancí |
| **Dny:** Pondělí, úterý, středa, čtvrtek, pátek |**Dny:** Sobota, neděle |
| **Čas spuštění:** 9:00 dop. |**Čas spuštění:** 9:00 dop. |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (horizontální navýšení kapacity)** |**Pravidlo automatického škálování (horizontální navýšení kapacity)** |
| **Prostředek:** Výroba (App Service Environment) |**Prostředek:** Výroba (App Service Environment) |
| **Metrika:** VČETNĚ |**Metrika:** VČETNĚ |
| **Operace:** Větší než 60% |**Operace:** Větší než 80% |
| **Doba trvání:** 5 minut |**Doba trvání:** 10 minut |
| **Časová agregace:** Vypočítat |**Časová agregace:** Vypočítat |
| **Akce:** Zvýšit počet o 2 |**Akce:** Zvýšit počet o 1 |
| Doba **vychladnutí (minuty):** 15 |Doba **vychladnutí (minuty):** 20 |
|  | |
| **Pravidlo automatického škálování (horizontální snížení kapacity)** |**Pravidlo automatického škálování (horizontální snížení kapacity)** |
| **Prostředek:** Výroba (App Service Environment) |**Prostředek:** Výroba (App Service Environment) |
| **Metrika:** VČETNĚ |**Metrika:** VČETNĚ |
| **Operace:** Méně než 30% |**Operace:** Méně než 20% |
| **Doba trvání:** 10 minut |**Trvání:** 15 minut |
| **Časová agregace:** Vypočítat |**Časová agregace:** Vypočítat |
| **Akce:** Snížit počet o 1 |**Akce:** Snížit počet o 1 |
| Doba **vychladnutí (minuty):** 20 |Doba **vychladnutí (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Míra inflace App Serviceho plánu
Plány App Service, které jsou nakonfigurovány pro automatické škálování, tak mají maximální sazbu za hodinu. Tato sazba se dá vypočítat na základě hodnot zadaných v pravidle automatického škálování.

Pro automatické škálování prostředí App Service je důležité pochopit a vypočítat *míru inflace App Service plánu* , protože změny měřítka fondu pracovních procesů nejsou okamžité.

Míra inflace App Serviceho plánu se vypočítává takto:

![App Service plánování výpočtu míry inflace.][ASP-Inflation]

Na základě pravidla automatického škálování – horizontálního škálování pro profil pracovního dne v plánu produkčního App Service:

![App Service plánu inflace pro pracovní dny založené na automatickém škálování – pravidlo navýšení kapacity.][Equation1]

V případě pravidla automatického škálování – pravidlo pro horizontální navýšení kapacity pro profil víkendu v produkčním App Service plánu se vzorec vyřeší takto:

![App Service plánu inflace pro víkendy na základě automatického škálování – pravidlo navýšení kapacity.][Equation2]

Tuto hodnotu lze také vypočítat pro operace horizontálního navýšení kapacity.

Na základě pravidla automatického škálování – horizontálního navýšení kapacity pro profil pracovního dne v plánu produkčního App Service by to vypadalo takto:

![App Service plánu inflace pro pracovní dny na základě pravidla automatického škálování – pravidlo horizontálního navýšení kapacity.][Equation3]

V případě pravidla automatického škálování – pravidlo horizontálního navýšení kapacity pro profil víkendu v produkčním App Service plánu se vzorec vyřeší takto:  

![App Service plánu inflace pro víkendy na základě pravidla automatického škálování – pravidlo horizontálního navýšení kapacity.][Equation4]

Plán produkčního App Service může dosáhnout maximální frekvence osmi instancí za hodinu během týdne a čtyř instancí za hodinu. Může vydávat instance s maximální rychlostí čtyř instancí za hodinu během týdne a šesti instancí za hodinu během víkendů.

Pokud je ve fondu pracovních procesů hostováno více plánů App Service, je třeba vypočítat celkovou míru *inflace* jako součet míry inflace pro všechny App Service plány, které jsou v daném fondu pracovních procesů hostovány.

![Celkový výpočet míry inflace pro více plánů App Service hostovaných ve fondu pracovních procesů.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Použití míry inflace App Serviceho plánu k definování pravidel automatického škálování fondu pracovních procesů
Fondy pracovních procesů, které hostují plány App Service nakonfigurované pro automatické škálování, je potřeba přidělit vyrovnávací paměti. Vyrovnávací paměť umožňuje operacím automatického škálování růst a zmenšení App Service plánu podle potřeby. Minimální vyrovnávací paměť by byla vypočtená celková míra inflace App Serviceho plánu.

Vzhledem k tomu, že při použití operací škálování App Service prostředí trvá určitý čas, jakákoliv změna by měla mít za následek další změny poptávky, ke kterým může dojít při probíhající operaci škálování. Pro uspokojení této latence doporučujeme, abyste používali vypočtenou celkovou míru inflace App Service plánu jako minimální počet instancí přidaných pro každou operaci automatického škálování.

Pomocí těchto informací může Jan definovat následující profil a pravidla automatického škálování:

![Pravidla profilace automatického škálování pro příklad LOB][Worker-Pool-Scale]

| **Profil automatického škálování – pracovní dny** | **Profil automatického škálování – víkendy** |
| --- | --- |
| **Název:** Profil dne v týdnu |**Název:** Profil víkendu |
| **Škálovat podle:** Pravidla plánování a výkonu |**Škálovat podle:** Pravidla plánování a výkonu |
| **Profil:** Všední dny |**Profil:** Volné |
| **Zadejte:** Vzorec |**Zadejte:** Vzorec |
| **Cílový rozsah:** 13 až 25 instancí |**Cílový rozsah:** 6 až 15 instancí |
| **Dny:** Pondělí, úterý, středa, čtvrtek, pátek |**Dny:** Sobota, neděle |
| **Čas spuštění:** 7:00 dop. |**Čas spuštění:** 9:00 dop. |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (horizontální navýšení kapacity)** |**Pravidlo automatického škálování (horizontální navýšení kapacity)** |
| **Prostředek:** Fond pracovních procesů 1 |**Prostředek:** Fond pracovních procesů 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Operace:** Méně než 8 |**Operace:** Méně než 3 |
| **Doba trvání:** 20 minut |**Doba trvání:** 30 minut |
| **Časová agregace:** Vypočítat |**Časová agregace:** Vypočítat |
| **Akce:** Zvýšit počet o 8 |**Akce:** Zvýšit počet o 3 |
| Doba **vychladnutí (minuty):** 180 |Doba **vychladnutí (minuty):** 180 |
|  | |
| **Pravidlo automatického škálování (horizontální snížení kapacity)** |**Pravidlo automatického škálování (horizontální snížení kapacity)** |
| **Prostředek:** Fond pracovních procesů 1 |**Prostředek:** Fond pracovních procesů 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Operace:** Větší než 8 |**Operace:** Větší než 3 |
| **Doba trvání:** 20 minut |**Trvání:** 15 minut |
| **Časová agregace:** Vypočítat |**Časová agregace:** Vypočítat |
| **Akce:** Snížit počet o 2 |**Akce:** Snížit počet o 3 |
| Doba **vychladnutí (minuty):** 120 |Doba **vychladnutí (minuty):** 120 |

Cílový rozsah definovaný v profilu se počítá minimálními instancemi definovanými v profilu pro App Service plán + vyrovnávací paměť.

Maximální rozsah by byl součet všech maximálních rozsahů všech plánů App Service hostovaných ve fondu pracovních procesů.

Počet zvýšení pro pravidla horizontálního navýšení kapacity by měl být nastaven na alespoň navýšení sazby App Service plánu inflace pro horizontální navýšení kapacity.

Počet zpomalení se dá upravit na něco v rozsahu od 1 do dvojnásobku nebo na1x poměrné hodnoty plánu App Service pro horizontální navýšení kapacity.

### <a name="autoscale-for-front-end-pool"></a>Automatické škálování pro front-end fond
Pravidla pro automatické škálování front-endu jsou jednodušší než u fondů pracovních procesů. Primárně byste měli  
Zajistěte, aby doba trvání měření a časovače cooldown zvážily, že operace škálování v plánu App Service nejsou okamžité.

V tomto scénáři Jan ví, že se míra chyb zvyšuje až po front-endy dosahující 80% využití procesoru, a nastaví pravidlo automatického škálování pro zvýšení instancí následujícím způsobem:

![Nastavení automatického škálování pro front-end fond.][Front-End-Scale]

| **Profil automatického škálování – přední zakončení** |
| --- |
| **Název:** Automatické škálování – front-endy |
| **Škálovat podle:** Pravidla plánování a výkonu |
| **Profil:** Běžně |
| **Zadejte:** Vzorec |
| **Cílový rozsah:** 3 až 10 instancí |
| **Dny:** Běžně |
| **Čas spuštění:** 9:00 dop. |
| **Časové pásmo:** UTC-08 |
|  |
| **Pravidlo automatického škálování (horizontální navýšení kapacity)** |
| **Prostředek:** Front-end fond |
| **Metrika:** VČETNĚ |
| **Operace:** Větší než 60% |
| **Doba trvání:** 20 minut |
| **Časová agregace:** Vypočítat |
| **Akce:** Zvýšit počet o 3 |
| Doba **vychladnutí (minuty):** 120 |
|  |
| **Pravidlo automatického škálování (horizontální snížení kapacity)** |
| **Prostředek:** Fond pracovních procesů 1 |
| **Metrika:** VČETNĚ |
| **Operace:** Méně než 30% |
| **Doba trvání:** 20 minut |
| **Časová agregace:** Vypočítat |
| **Akce:** Snížit počet o 3 |
| Doba **vychladnutí (minuty):** 120 |

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
