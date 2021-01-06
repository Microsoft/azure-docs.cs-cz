---
title: Plán Azure Functions Premium
description: Podrobnosti a možnosti konfigurace (virtuální síť, bez počátečního startu, neomezené trvání spuštění) pro plán Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: d944512e5f6126920ab4fba99fb70513b93177ba
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936817"
---
# <a name="azure-functions-premium-plan"></a>Plán Azure Functions Premium

Plán Azure Functions Premium (někdy označovaný jako elastický plán Premium) je možnost hostování pro aplikace Function App. Další možnosti plánu hostování naleznete v [článku plán hostování](functions-scale.md).

Hostování plánu Premium poskytuje vašim funkcím následující výhody:

* Vyhněte se studeným startům s trvalou teplou instancí
* Připojení k virtuální síti.
* Neomezené trvání spuštění s garantovanou 60 minutami.
* Velikosti instancí úrovně Premium: jedna jádro, dvě jádro a čtyři základní instance.
* Přesnější ceny v porovnání s plánem spotřeby.
* Přidělování aplikací s vysokou hustotou pro plány s více aplikacemi Function App.

Pokud používáte plán Premium, instance Azure Functions hostitele se přidají a odeberou na základě počtu příchozích událostí, stejně jako u [plánu spotřeby](consumption-plan.md). Do stejného plánu Premium se dá nasadit víc aplikací Function App a plán vám umožní nakonfigurovat velikost instance COMPUTE, velikost základního plánu a maximální velikost plánu. 

## <a name="billing"></a>Fakturace

Faktura za plán Premium vychází z počtu základních sekund a paměti přidělených napříč instancemi. Tato fakturace se liší od plánu spotřeby, který se účtuje podle spuštění a využité paměti. S plánem Premium se neúčtují žádné poplatky za spuštění. Nejméně jedna instance musí být každému plánu vždy přidělena. Výsledkem této fakturace je minimální měsíční cena za aktivní plán bez ohledu na to, jestli je funkce aktivní nebo nečinná. Mějte na paměti, že všechny aplikace Function App v plánu Premium sdílí přidělené instance. Další informace najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-premium-plan"></a>Vytvořit plán Premium

Když vytvoříte aplikaci funkcí v Azure Portal, bude výchozím plánem spotřeby. Pokud chcete vytvořit aplikaci Function App, která běží v plánu Premium, musíte explicitně vytvořit App Service plán pomocí jedné z SKU _elastické úrovně Premium_ . Vytvořená aplikace Function App je pak hostována v tomto plánu. Azure Portal usnadňuje vytvoření plánu Premium i aplikace Function App. V rámci stejného plánu Premium můžete spustit více než jednu aplikaci Function App, ale obě jsou spouštěny ve stejném operačním systému (Windows nebo Linux). 

V následujících článcích se dozvíte, jak vytvořit aplikaci funkcí s plánem Premium, a to buď prostřednictvím kódu programu, nebo v Azure Portal:

+ [Azure Portal](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Šablona Azure Resource Manageru](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>Eliminace studeného startu

Když se události nebo provádění nevyskytnou v plánu spotřeby, vaše aplikace se může škálovat na nulové instance. V případě, že se nové události přidávají v, musí být specializovaná nová instance s vaší aplikací, která je v ní spuštěná. Specializace nových instancí může v závislosti na aplikaci nějakou dobu trvat. Tato další latence při prvním volání se často označuje jako _studený start_ aplikace.

Plán Premium nabízí dvě funkce, které společně umožňují efektivně eliminovat studená spuštění ve vašich funkcích: _vždy připravené instance_ a _předem zahřívání instance_. 

### <a name="always-ready-instances"></a>Vždy připravené instance

V plánu Premium můžete mít aplikaci vždycky připravenou na zadaný počet instancí. Maximální počet vždy připravených instancí je 20. Když události začnou aplikaci aktivovat, budou nejprve směrovány na instance Always Ready. Jelikož se funkce změní na aktivní, další instance se zahřeje jako vyrovnávací paměť. Tato vyrovnávací paměť brání studeným startům pro nové instance potřebné během škálování. Tyto instance ve vyrovnávací paměti se nazývají [předem zahřívání instance](#pre-warmed-instances). Díky kombinaci instancí vždy připraveno a předem zahřívání vyrovnávací paměti může vaše aplikace efektivně eliminovat studené zahájení.

> [!NOTE]
> Každý plán Premium má vždycky alespoň jednu aktivní (fakturované) instanci.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Počet vždy připravených instancí můžete v Azure Portal nakonfigurovat tak, že vyberete svoji **Function App** a kliknete na kartu **funkce platformy** a vyberete možnosti **horizontálního** navýšení kapacity. V okně pro úpravu aplikace Function App jsou vždy připravené instance specifické pro danou aplikaci.

![Nastavení elastického škálování](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Pro aplikaci můžete pomocí Azure CLI nakonfigurovat i vždy připravené instance.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Předem zahřívání instance

Předem zahřívání instance jsou instance zahřívání jako vyrovnávací paměť během událostí škálování a aktivace. Předem zavedené instance pokračují do vyrovnávací paměti, dokud nedosáhnete maximálního limitu pro horizontální navýšení kapacity. Výchozí počet předem zahřívání instancí je 1. ve většině scénářů by tato hodnota měla zůstat jako 1.

Pokud má aplikace dlouhou zahřívání (například vlastní image kontejneru), může být potřeba tuto vyrovnávací paměť zvětšit. Předem zahřívání instance bude aktivní až po dostatečném využití všech aktivních instancí.

Vezměte v úvahu tento příklad, jak jsou instance Always-Read a předem zapracované instance společné. Aplikace funkcí Premium má nakonfigurované pět instancí vždy připraveno a výchozí nastavení jedné předem zahřívání instance. Když je aplikace nečinná a neaktivují se žádné události, aplikace se zřídí a spustí s pěti instancemi. V tuto chvíli se vám neúčtují předem zavedená instance, protože se nepoužívají instance pro vždycky připravené a nepřiřazují se žádné předem zahřívání instance.

Jakmile se první Trigger dostane do, stanou se pět instancí Always-Read aktivní a přidělí se předem zastaralá instance. Aplikace je teď spuštěná se šesti zřízenými instancemi: pět instancí vždy připraveno na aktivní a šestá předem zahřívání a neaktivní vyrovnávací paměť. Pokud se frekvence provádění stále zvyšuje, budou se nakonec používat pět aktivních instancí. Když se platforma rozhodne o škálování mimo pět instancí, škáluje se do předem zahřívání instance. Pokud k tomu dojde, teď se zobrazí šest aktivních instancí a sedmá instance se okamžitě zřídí a vyplní předem zastaralou vyrovnávací paměť. Tato sekvence škálování a předběžného zahřívání pokračuje, dokud nedosáhnete maximálního počtu instancí pro aplikaci. Žádné instance nejsou předem zahříváníelné nebo aktivované mimo maximum.

Počet předem zavedených instancí aplikace můžete upravit pomocí Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Maximální počet instancí aplikace Function App

Kromě [plánu maximálního počtu instancí](#plan-and-sku-settings)můžete nakonfigurovat maximálně jednu aplikaci. Maximum aplikace se dá nakonfigurovat pomocí [limitu škálování aplikace](./event-driven-scaling.md#limit-scale-out).

## <a name="private-network-connectivity"></a>Připojení k privátní síti

Aplikace Function App nasazené do plánu Premium můžou využít [integraci virtuální sítě pro webové aplikace](../app-service/web-sites-integrate-with-vnet.md). Když nakonfigurujete aplikaci, může komunikovat s prostředky ve vaší virtuální síti nebo zabezpečená prostřednictvím koncových bodů služby. V aplikaci jsou k dispozici také omezení IP pro omezení příchozího provozu.

Při přiřazování podsítě do aplikace Function App v plánu Premium budete potřebovat podsíť s dostatečnými IP adresami pro každou potenciální instanci. Vyžadujeme blok IP s minimálně 100 dostupnými adresami.

Další informace najdete v tématu [integrace aplikace Function App s virtuální](functions-create-vnet.md)sítí.

## <a name="rapid-elastic-scale"></a>Rychlé Elastické škálování

Další výpočetní instance se automaticky přidají do vaší aplikace pomocí stejné logiky rychlé škálování jako u plánu spotřeby. Aplikace ve stejném App Service plánu se škálují nezávisle na sobě, a to na základě potřeb jednotlivých aplikací. Aplikace Functions ve stejném App Service naplánují sdílení prostředků virtuálních počítačů, které vám pomůžou snížit náklady, pokud je to možné. Počet aplikací přidružených k virtuálnímu počítači závisí na tom, jaké aplikace a velikost virtuálního počítače.

Další informace o tom, jak škálování funguje, najdete [v tématu škálování na základě událostí v Azure Functions](event-driven-scaling.md).

## <a name="longer-run-duration"></a>Delší doba běhu

Azure Functions v plánu spotřeby se pro jedno spuštění omezí na 10 minut. V plánu Premium je doba běhu standardně 30 minut, aby se zabránilo provádění. Můžete ale [upravit host.jsv konfiguraci](./functions-host-json.md#functiontimeout) , aby byla doba nevázaná pro aplikace plánu Premium. Když nastavíte neohraničenou dobu trvání, vaše aplikace Function App je zaručená běžet aspoň po 60 minutách. 

## <a name="plan-and-sku-settings"></a>Nastavení plánu a SKU

Při vytváření plánu jsou k dispozici dvě nastavení velikosti plánu: minimální počet instancí (nebo velikost plánu) a maximální limit shlukování.

Pokud vaše aplikace vyžaduje instance mimo instance, které jsou vždy připravené, může pokračovat horizontální navýšení kapacity, dokud počet instancí nedosáhne maximálního limitu shlukování. Účtují se za instance přesahující váš plán jenom v době, kdy jsou spuštěné a přidělují se vám za sekundu. Platforma díky tomu nejlépe omezuje kapacitu aplikace na definovaný maximální limit.

Velikost plánu a maximum v Azure Portal můžete nakonfigurovat výběrem možností **horizontálního** navýšení kapacity v plánu nebo aplikace Function App nasazené do tohoto plánu (v části **funkce platformy**).

Můžete taky zvýšit maximální limit shluku z Azure CLI:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Minimální pro každý plán bude nejméně jedna instance. Skutečný minimální počet instancí, které se automaticky nakonfigurují za vás, a to v závislosti na instancích Always Reading požadovaných aplikacemi v plánu. Pokud například aplikace A požaduje pět instancí vždy připraveno a aplikace B požádá dva instance vždy připravené ve stejném plánu, bude minimální velikost plánu vypočítána jako pět. App A bude běžet na všech 5 a aplikace B bude běžet jenom na 2.

> [!IMPORTANT]
> Za každou instanci přidělenou v minimálním počtu instancí se účtuje bez ohledu na to, jestli jsou funkce spuštěné nebo ne.

Ve většině případů je toto minimální vypočítané minimum dostačující. Škálování nad rámec minima ale dosáhne nejvyšší intenzity. Je možné, že pokud nejsou k dispozici další instance, může být v určité době nepravděpodobné, že se v určitém časovém limitu může zpozdit škálování. Nastavením minimálního minimálního počtu automatického vypočítaného minima rezervujete instance předem.

Zvýšení vypočítaného minima pro plán se dá provést pomocí Azure CLI.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>Dostupné skladové položky instance

Při vytváření nebo škálování plánu si můžete vybrat mezi třemi velikostmi instancí. Bude se vám účtovat celkový počet jader a zřízených paměti za sekundu, které jsou pro každou instanci přiděleny. Vaše aplikace se může podle potřeby automaticky škálovat na více instancí.

|SKU|Cores|Paměť|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|S frekvencí|250 GB|

### <a name="memory-usage-considerations"></a>Požadavky na využití paměti

Spuštění v počítači, který má více paměti, vždy neznamená, že aplikace Function App používá veškerou dostupnou paměť.

Například aplikace funkcí JavaScriptu je omezená na výchozí omezení paměti v Node.js. Pokud chcete zvýšit toto omezení pevné paměti, přidejte nastavení aplikace `languageWorkers:node:arguments` s hodnotou `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Maximální horizontální navýšení kapacity oblasti

Níže jsou uvedeny aktuálně podporované maximální hodnoty škálování pro jeden plán v každé oblasti a konfiguraci operačního systému. Chcete-li požádat o zvýšení, můžete otevřít lístek podpory.

Prohlédněte si kompletní regionální dostupnost funkcí na webu [Azure](https://azure.microsoft.com/global-infrastructure/services/?products=functions).

|Oblast| Windows | Linux |
|--| -- | -- |
|Austrálie – střed| 100 | Není k dispozici |
|Austrálie – střed 2| 100 | Není k dispozici |
|Austrálie – východ| 100 | 20 |
|Australia Southeast | 100 | 20 |
|Brazil South| 100 | 20 |
|Střední Kanada| 100 | 20 |
|Střední USA| 100 | 20 |
|Čína – východ 2| 100 | 20 |
|Čína – sever 2| 100 | 20 |
|Východní Asie| 100 | 20 |
|East US | 100 | 20 |
|USA – východ 2| 100 | 20 |
|Francie – střed| 100 | 20 |
|Německo – středozápad| 100 | Není k dispozici |
|Japan East| 100 | 20 |
|Japonsko – západ| 100 | 20 |
|Jižní Korea – střed| 100 | 20 |
|Jižní Korea – jih| Není k dispozici | 20 |
|USA – středosever| 100 | 20 |
|Severní Evropa| 100 | 20 |
|Norsko – východ| 100 | 20 |
|Středojižní USA| 100 | 20 |
|Indie – jih | 100 | Není k dispozici |
|Southeast Asia| 100 | 20 |
|Švýcarsko – sever| 100 | Není k dispozici |
|Švýcarsko – západ| 100 | Není k dispozici |
|Spojené království – jih| 100 | 20 |
|Spojené království – západ| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginie| 100 | 20 |
|USNat východ| 100 | Není k dispozici |
|USNat – západ| 100 | Není k dispozici |
|West Europe| 100 | 20 |
|Západní Indie| 100 | 20 |
|USA – středozápad| 100 | 20 |
|USA – západ| 100 | 20 |
|Západní USA 2| 100 | 20 |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Pochopení možností hostování Azure Functions](functions-scale.md)
