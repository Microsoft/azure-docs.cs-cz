---
title: Automatické škálování a App Service Environment v1 – Azure
description: Automatické škálování a App Service Environment
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
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270021"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatické škálování a App Service Environment v1

> [!NOTE]
> Tento článek je o App Service Environment v1.  Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

Podpora Azure App Service Environment *automatické škálování*. Je to možné automatického škálování jednotlivých fondů pracovních procesů podle metriky nebo plánu.

![Možnosti automatického škálování pro fond pracovních procesů.][intro]

Automatické škálování optimalizuje využití prostředků tím, že automatické zvětšování a zmenšování služby App Service environment vyhovující vašemu rozpočtu a načtěte profil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurace automatického škálování fondu pracovních procesů
Funkce automatického škálování z se zpřístupní **nastavení** kartu fond pracovních procesů.

![Karta nastavení fondu pracovních procesů.][settings-scale]

Odtud rozhraní by měl být ovládat, protože je stejným způsobem, že se zobrazuje při vertikálním navýšení plán služby App Service. 

![Ruční škálování nastavení.][scale-manual]

Můžete také nakonfigurovat profil automatického škálování.

![Nastavení automatického škálování.][scale-profile]

Profily automatického škálování jsou užitečné si nastavit limity na škálovací. Tímto způsobem může mít konzistentní výkon prostředí tak, že nastavíte hodnotu dolní mez rozsahu (1) a nákladů na předvídatelné zakončení nastavením horní mez (2).

![Nastavení škálování v profilu.][scale-profile2]

Jakmile nadefinujete profil, můžete přidat pravidla automatického škálování pro horizontální navýšení nebo snížení kapacity počtu instancí ve fondu pracovních procesů v rámci hranice definované profilem. Pravidla automatického škálování se na základě metrik.

![Pravítko měřítka.][scale-rule]

 Metriky front-endu ani fond pracovních procesů lze použít k definování pravidel automatického škálování. Tyto metriky jsou stejné metriky můžete sledovat v grafech okno prostředku nebo nastavit upozornění pro.

## <a name="autoscale-example"></a>Příklad automatického škálování
Automatické škálování aplikace služby App Service environment lze ukázat nejlepší ve scénáři s návodem.

Tento článek vysvětluje všechny nezbytné požadavky při nastavení automatického škálování. Tento článek vás provede interakcí, které souvisejí při zohlednit na automatické škálování služby App Service Environment, které jsou hostované ve službě App Service Environment.

### <a name="scenario-introduction"></a>Scénář Úvod
Frank je pro organizace, který migroval do služby App Service environment část zátěží, které spravuje správce systému.

Prostředí App Service je nakonfigurované pro ruční škálování následujícím způsobem:

* **Front-endů:** 3
* **Fond pracovních procesů 1**: 10
* **Fond pracovních procesů 2**: 5
* **Fond pracovních procesů 3**: 5

Fond pracovních procesů 1 se používá pro úlohy v produkčním prostředí, zatímco fond pracovních procesů 2 a fond pracovních procesů 3 se používá pro úlohy vývoje a kontroly kvality (dotazů a odpovědí).

Plány služby App Service pro kontrolu kvality a dev jsou nakonfigurovány k ruční škálování. Produkční plán služby App Service je nastavena na automatické škálování se kolísání zatížení a provozu.

Frank je velmi dobře známé s aplikací. Ví, že špičku zátěže jsou mezi 9:00:00 a 18:00:00, protože to je – obchodní aplikace (LOB), která zaměstnanci používají, pokud nejsou v kanceláři. Využití zahodí po tomto dokončení uživatelů pro daný den. Mimo špičku je stále nějaké zatížení vzhledem k tomu, že uživatelé vzdálený přístup k aplikaci s použitím vlastních mobilních zařízení nebo domácích počítačů. Produkční plán služby App Service je už nakonfigurovaný pro automatické škálování podle využití procesoru na základě následujících pravidel:

![Specifické nastavení pro obchodní aplikace.][asp-scale]

| **Profil automatického škálování – dny v týdnu – plán služby App Service** | **Profil automatického škálování – bez víkendů – plán služby App Service** |
| --- | --- |
| **Jméno:** Den v týdnu profilu |**Jméno:** Profil víkendu |
| **Škálování podle:** Pravidla výkonu a plánování |**Škálování podle:** Pravidla výkonu a plánování |
| **Profil:** Dny v týdnu |**Profil:** Víkendu |
| **Typ:** Opakování |**Typ:** Opakování |
| **Cílový rozsah:** 5 až 20 instancí |**Cílový rozsah:** 3 až 10 instancí |
| **Dny:** Pondělí, úterý, středa, čtvrtek, pátek |**Dny:** Sobota, neděle |
| **Čas spuštění:** 9:00:00 |**Čas spuštění:** 9:00:00 |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |**Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |
| **prostředek:** Výrobní (App Service Environment) |**prostředek:** Výrobní (App Service Environment) |
| **Metrika:** % CPU |**Metrika:** % CPU |
| **Operace:** Více než 60 % |**Operace:** Větší než 80 % |
| **Doba trvání:** 5 minut |**Doba trvání:** 10 minut |
| **Časová agregace:** Průměr |**Časová agregace:** Průměr |
| **Akce:** Zvýšit počet o 2 |**Akce:** Zvýšit počet o 1 |
| **Přestávka (minuty):** 15 |**Přestávka (minuty):** 20 |
|  | |
| **Pravidlo automatického škálování (Škálovací dolů)** |**Pravidlo automatického škálování (Škálovací dolů)** |
| **prostředek:** Výrobní (App Service Environment) |**prostředek:** Výrobní (App Service Environment) |
| **Metrika:** % CPU |**Metrika:** % CPU |
| **Operace:** Méně než 30 % |**Operace:** Méně než 20 % |
| **Doba trvání:** 10 minut |**Doba trvání:** 15 minut |
| **Časová agregace:** Průměr |**Časová agregace:** Průměr |
| **Akce:** Snížit počet o 1 |**Akce:** Snížit počet o 1 |
| **Přestávka (minuty):** 20 |**Přestávka (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Sazba inflaci plánu služby App Service
Plány služby App Service, které jsou nakonfigurované k automatickému škálování Uděláte to tak maximální rychlostí za hodinu. Tento kurz je vypočítat na základě hodnot zadaných v pravidle automatického škálování.

Principy a výpočtu *sazba inflaci plánu služby App Service* je důležité pro automatické škálování prostředí služby App Service, protože nejsou okamžité škálování změny fond pracovních procesů.

Sazba inflaci plánu služby App Service se vypočte takto:

![Výpočet inflaci kurzu plán služby App Service.][ASP-Inflation]

Automatické škálování – pravidlo vertikálně navýšit kapacitu pro den v týdnu profil produkční plán služby App Service podle:

![Sazba inflaci plánu služby App Service pro automatické škálování – pravidlo vertikálně navýšit kapacitu podle pracovní dny][Equation1]

Vzorec by v případě automatické škálování – pravidlo vertikálně navýšit kapacitu pro profil víkendu produkční plán služby App Service, přeložit na:

![Kurz inflaci plán služby App Service pro víkendy založené na automatické škálování – pravidlo vertikálně navýšit kapacitu.][Equation2]

Tato hodnota je také vypočítat pro vertikální snížení kapacity operace.

Na základě pro automatické škálování – škálování dolů pravidlo pro den v týdnu profil produkční plán služby App Service, to vypadá takto:

![Kurz inflaci plán služby App Service pro dny v týdnu založené na automatické škálování – škálování dolů pravidlo.][Equation3]

Vzorec by v případě automatické škálování – škálování dolů pravidlo pro profil víkendu produkční plán služby App Service, přeložit na:  

![Kurz inflaci plán služby App Service pro víkendy založené na automatické škálování – škálování dolů pravidlo.][Equation4]

Produkční plán služby App Service můžou růst s maximální mírou osmi instancí za hodinu v týdnu a čtyři instance/hod. během víkendu. To můžete uvolnit instance maximální rychlostí čtyři instance za hodinu v týdnu a šest instancí za hodinu během víkendu.

Pokud více plánů služby App Service jsou hostované ve fondu pracovních procesů, je nutné vypočítat *celková míra inflaci* jako součet míry inflaci pro všechny plány služby App Service, které jsou hostitelem v tomto fondu pracovních procesů.

![Celková rychlost inflaci výpočtu pro více plánů služby App Service, které jsou hostované ve fondu pracovních procesů.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Můžete definovat pravidla automatického škálování fondu pracovních procesů sazba inflaci plánu služby App Service
Fondy pracovních procesů, které jsou hostiteli plány služby App Service, které jsou nakonfigurované pro automatické škálování je potřeba přidělit vyrovnávací paměť kapacity. Vyrovnávací paměť umožňuje operace automatického škálování pro zvětšení a zmenšení plán služby App Service podle potřeby. Minimální vyrovnávací paměti by počítaný celkový počet aplikací služby plánování inflaci rychlost.

Protože operace škálování služby App Service prostředí trvat nějakou dobu použít, všechny změny by měl účet pro další změny poptávky, které může dojít, když probíhá operace škálování. Tak, aby vyhovovaly latence, doporučujeme použít počítaný celkový počet aplikací služby plánování inflaci rychlost jako minimální počet instancí, které jsou přidány pro každou operaci automatického škálování.

Pomocí těchto informací můžete definovat Frank následující profil automatického škálování a pravidla:

![Pravidla automatického škálování profilu například LOB.][Worker-Pool-Scale]

| **Profil automatického škálování – dny v týdnu** | **Profil automatického škálování – bez víkendů** |
| --- | --- |
| **Jméno:** Den v týdnu profilu |**Jméno:** Profil víkendu |
| **Škálování podle:** Pravidla výkonu a plánování |**Škálování podle:** Pravidla výkonu a plánování |
| **Profil:** Dny v týdnu |**Profil:** Víkendu |
| **Typ:** Opakování |**Typ:** Opakování |
| **Cílový rozsah:** 13 až 25 instancí |**Cílový rozsah:** instance 6-15 |
| **Dny:** Pondělí, úterý, středa, čtvrtek, pátek |**Dny:** Sobota, neděle |
| **Čas spuštění:** 7:00:00 |**Čas spuštění:** 9:00:00 |
| **Časové pásmo:** UTC-08 |**Časové pásmo:** UTC-08 |
|  | |
| **Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |**Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |
| **prostředek:** Fond pracovních procesů 1 |**prostředek:** Fond pracovních procesů 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Operace:** Méně než 8 |**Operace:** Menší než 3. |
| **Doba trvání:** 20 minut |**Doba trvání:** 30 minut |
| **Časová agregace:** Průměr |**Časová agregace:** Průměr |
| **Akce:** Zvýšit počet o 8 |**Akce:** Zvýšit počet o 3 |
| **Přestávka (minuty):** 180 |**Přestávka (minuty):** 180 |
|  | |
| **Pravidlo automatického škálování (Škálovací dolů)** |**Pravidlo automatického škálování (Škálovací dolů)** |
| **prostředek:** Fond pracovních procesů 1 |**prostředek:** Fond pracovních procesů 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Operace:** Větší než 8 |**Operace:** Větší než 3 |
| **Doba trvání:** 20 minut |**Doba trvání:** 15 minut |
| **Časová agregace:** Průměr |**Časová agregace:** Průměr |
| **Akce:** Snížit počet o 2 |**Akce:** Snížit počet o 3 |
| **Přestávka (minuty):** 120 |**Přestávka (minuty):** 120 |

Minimální instance, které jsou definovány v profilu pro plán služby App Service a vyrovnávací paměti je vypočítána cílový rozsah definovány v profilu.

Maximální rozsah by součet všech maximální rozsahy pro všechny plány služby App Service, které jsou hostované ve fondu pracovních procesů.

Zvýšit počet pro vertikální navýšení pravidla by měla nastavit na nejméně 1 X sazba inflaci plánu služby App pro škálování.

Snížit počet je možné upravit na něco mezi 1/2 X nebo 1 X sazba inflaci plánu služby App škálování dolů.

### <a name="autoscale-for-front-end-pool"></a>Automatické škálování pro front-endový fond
Pravidla pro automatické škálování front-endu jsou jednodušší než pro fondy pracovních procesů. Především měli byste  
Ujistěte se, že doba trvání měření a která časovače vezměte v úvahu, že nejsou okamžité operace škálování na plán služby App Service.

V tomto scénáři Frank ví, že míra chyb zvyšuje po dosažení front-endů 80 % využití CPU a nastaví pravidlo automatického škálování pro zvýšení instance následujícím způsobem:

![Nastavení automatického škálování pro front-endový fond.][Front-End-Scale]

| **Profil automatického škálování – přední končí** |
| --- |
| **Jméno:** Automatické škálování – přední končí |
| **Škálování podle:** Pravidla výkonu a plánování |
| **Profil:** každý den |
| **Typ:** Opakování |
| **Cílový rozsah:** 3 až 10 instancí |
| **Dny:** každý den |
| **Čas spuštění:** 9:00:00 |
| **Časové pásmo:** UTC-08 |
|  |
| **Pravidlo automatického škálování (vertikálně navýšit kapacitu)** |
| **prostředek:** Front-endový fond |
| **Metrika:** % CPU |
| **Operace:** Více než 60 % |
| **Doba trvání:** 20 minut |
| **Časová agregace:** Průměr |
| **Akce:** Zvýšit počet o 3 |
| **Přestávka (minuty):** 120 |
|  |
| **Pravidlo automatického škálování (Škálovací dolů)** |
| **prostředek:** Fond pracovních procesů 1 |
| **Metrika:** % CPU |
| **Operace:** Méně než 30 % |
| **Doba trvání:** 20 minut |
| **Časová agregace:** Průměr |
| **Akce:** Snížit počet o 3 |
| **Přestávka (minuty):** 120 |

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
