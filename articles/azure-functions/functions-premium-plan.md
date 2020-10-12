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
ms.openlocfilehash: a037c903a72ba79b79c7e6b011fe025aefd7b51d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578032"
---
# <a name="azure-functions-premium-plan"></a>Plán Azure Functions Premium

Plán Azure Functions Premium (někdy označovaný jako elastický plán Premium) je možnost hostování pro aplikace Function App. Plán Premium poskytuje funkce, jako je připojení k virtuální síti, žádný studený start a špičkový hardware.  Do stejného plánu Premium se dá nasadit víc aplikací Function App a plán vám umožní nakonfigurovat velikost instance COMPUTE, velikost základního plánu a maximální velikost plánu.  Porovnání plánu Premium a dalších typů plánování a hostování najdete v tématu [možnosti škálování a hostování funkcí](functions-scale.md).

## <a name="create-a-premium-plan"></a>Vytvořit plán Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Plán Premium můžete vytvořit také pomocí příkaz [AZ functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) v rozhraní příkazového řádku Azure CLI. Následující příklad vytvoří plán vrstvy _elastické úrovně Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

V tomto příkladu nahraďte `<RESOURCE_GROUP>` svou skupinou prostředků a `<PLAN_NAME>` názvem vašeho plánu, který je ve skupině prostředků jedinečný. Zadejte [podporovanou `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions)hodnotu. Pokud chcete vytvořit plán Premium, který podporuje Linux, zahrňte `--is-linux` možnost.

Pomocí vytvořeného plánu můžete vytvořit aplikaci Function App pomocí [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Na portálu se současně vytvoří plán i aplikace. Příklad kompletního skriptu Azure CLI najdete v tématu [Vytvoření aplikace Function App v plánu Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funkce

K dispozici jsou následující funkce pro aplikace Functions nasazené do plánu Premium.

### <a name="always-ready-instances"></a>Vždy připravené instance

Pokud v plánu spotřeby nejsou žádné události a spuštění, vaše aplikace se může škálovat na nulové instance. Když se přidají nové události v, musí být speciální instance specializovaná na svou aplikaci, která je na ní spuštěná.  Specializace nových instancí může v závislosti na aplikaci nějakou dobu trvat.  Tato další latence při prvním volání se často označuje jako studený start aplikace.

V plánu Premium můžete mít aplikaci vždycky připravenou na zadaný počet instancí.  Maximální počet vždy připravených instancí je 20.  Když události začnou aplikaci aktivovat, budou směrovány nejprve na instance vždy připravené.  Jelikož se funkce změní na aktivní, další instance se zahřeje jako vyrovnávací paměť.  Tato vyrovnávací paměť brání studeným startům pro nové instance potřebné během škálování.  Tyto instance ve vyrovnávací paměti se nazývají [předem zahřívání instance](#pre-warmed-instances).  Díky kombinaci instancí vždy připraveno a předem zahřívání vyrovnávací paměti může vaše aplikace efektivně eliminovat studené zahájení.

> [!NOTE]
> U každého plánu Premium bude vždy aspoň jedna aktivní (fakturovaná) instance.

Počet vždy připravených instancí můžete v Azure Portal nakonfigurovat tak, že vyberete svoji **Function App**a kliknete na kartu **funkce platformy** a vyberete možnosti **horizontálního** navýšení kapacity. V okně pro úpravu aplikace Function App jsou vždy připravené instance specifické pro danou aplikaci.

![Nastavení elastického škálování](./media/functions-premium-plan/scale-out.png)

Pro aplikaci můžete pomocí Azure CLI nakonfigurovat i vždy připravené instance.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>Předem zahřívání instance

Předem zahřívání instance jsou počet instancí zahřívání jako vyrovnávací paměť během událostí škálování a aktivace.  Předem zavedené instance pokračují do vyrovnávací paměti, dokud nedosáhnete maximálního limitu pro horizontální navýšení kapacity.  Výchozí hodnota předem zahřívání instance je 1 a většina scénářů by měla zůstat jako 1.  Pokud má aplikace dlouhou dobu zahřívání (například vlastní image kontejneru), můžete tuto vyrovnávací paměť zvětšit.  Předem zastaralá instance bude aktivní až po dostatečném využití všech aktivních instancí.

Vezměte v úvahu tento příklad, jakým způsobem fungují instance Always Ready a předem zahřívání instance.  Aplikace funkcí Premium má nakonfigurované pět instancí vždy připraveno a výchozí nastavení jedné předem zahřívání instance.  Když je aplikace nečinná a neaktivují se žádné události, aplikace se zřídí a spustí na pět instancí.  V tuto chvíli se vám neúčtují předem zavedená instance, protože se nepoužívají instance Always Ready a ještě není přidělená žádná předem zatrvalá instance.

Jakmile se první Trigger dostane do, stanou se pět instancí vždy připraveno aktivní a přidělí se předem zastaralá instance.  Aplikace je teď spuštěná se šesti zřízenými instancemi: pět instancí vždy připraveno na aktivní a šestá předem zahřívání a neaktivní vyrovnávací paměť.  Pokud se frekvence provádění stále zvětšuje, bude se nakonec využívat pět aktivních instancí.  Když se platforma rozhodne škálovat víc než pět instancí, bude se škálovat do předem zahřívání instance.  Pokud k tomu dojde, bude nyní existovat šest aktivních instancí a sedmá instance bude okamžitě zřízena a bude provedena vyplňování předem zahřívání vyrovnávací paměti.  Tato posloupnost škálování a před zahříváním bude pokračovat, dokud nedosáhnete maximálního počtu instancí pro aplikaci.  Žádné instance nebudou předem zahřívání ani aktivovány mimo maximum.

Počet předem zavedených instancí aplikace můžete upravit pomocí Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>Maximální počet instancí aplikace

Kromě [plánu maximálního počtu instancí](#plan-and-sku-settings)můžete nakonfigurovat maximálně jednu aplikaci.  Maximum aplikace se dá nakonfigurovat pomocí [limitu škálování aplikace](./functions-scale.md#limit-scale-out).

### <a name="private-network-connectivity"></a>Připojení k privátní síti

Azure Functions nasazené do plánu Premium využívá [novou integraci virtuální sítě pro webové aplikace](../app-service/web-sites-integrate-with-vnet.md).  Když nakonfigurujete aplikaci, může komunikovat s prostředky ve vaší virtuální síti nebo zabezpečená prostřednictvím koncových bodů služby.  V aplikaci jsou k dispozici také omezení IP pro omezení příchozího provozu.

Při přiřazování podsítě do aplikace Function App v plánu Premium budete potřebovat podsíť s dostatečnými IP adresami pro každou potenciální instanci. Vyžadujeme blok IP s minimálně 100 dostupnými adresami.

Další informace najdete v tématu [integrace aplikace Function App s virtuální](functions-create-vnet.md)sítí.

### <a name="rapid-elastic-scale"></a>Rychlé Elastické škálování

Další výpočetní instance se automaticky přidají do vaší aplikace pomocí stejné logiky rychlé škálování jako u plánu spotřeby. Aplikace ve stejném App Service plánu se škálují nezávisle na sobě, a to na základě potřeb jednotlivých aplikací. Aplikace Functions ve stejném App Service naplánují sdílení prostředků virtuálních počítačů, které vám pomůžou snížit náklady, pokud je to možné. Počet aplikací přidružených k virtuálnímu počítači závisí na tom, jaké aplikace a velikost virtuálního počítače.

Další informace o tom, jak škálování funguje, najdete v tématu [škálování a hostování funkcí](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Delší doba běhu

Azure Functions v plánu spotřeby se pro jedno spuštění omezí na 10 minut.  V plánu Premium je doba běhu standardně 30 minut, aby se zabránilo provádění. Můžete ale [upravit host.jsv konfiguraci](./functions-host-json.md#functiontimeout) , aby byla doba nevázaná pro aplikace plánu Premium (garantované 60 minut).

## <a name="plan-and-sku-settings"></a>Nastavení plánu a SKU

Při vytváření plánu jsou k dispozici dvě nastavení velikosti plánu: minimální počet instancí (nebo velikost plánu) a maximální limit shlukování.

Pokud vaše aplikace vyžaduje instance mimo instance Always Ready, může pokračovat horizontální navýšení kapacity, dokud počet instancí nedosáhne maximálního limitu shlukování.  Účtují se za instance přesahující váš plán jenom v době, kdy jsou spuštěné a přidělují se vám za sekundu.  Díky omezení velikosti vaší aplikace na vymezený maximální limit dosáhneme úsilí.

Velikost plánu a maximum v Azure Portal můžete nakonfigurovat výběrem možností **horizontálního** navýšení kapacity v plánu nebo aplikace Function App nasazené do tohoto plánu (v části **funkce platformy**).

Můžete taky zvýšit maximální limit shluku z Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

Minimální pro každý plán bude nejméně jedna instance.  Skutečný minimální počet instancí, které se automaticky nakonfigurují za vás, a to v závislosti na instancích Always Reading požadovaných aplikacemi v plánu.  Pokud například aplikace A požaduje pět instancí vždy připraveno a aplikace B požádá dva instance vždy připravené ve stejném plánu, bude minimální velikost plánu vypočítána jako pět.  App A bude běžet na všech 5 a aplikace B bude běžet jenom na 2.

> [!IMPORTANT]
> Za každou instanci přidělenou v minimálním počtu instancí se účtuje bez ohledu na to, jestli jsou funkce spuštěné nebo ne.

Ve většině případů by mělo být minimum vypočítaného minima dostačující.  Škálování nad rámec minima ale dosáhne nejvyšší intenzity.  Pokud nejsou k dispozici další instance, je možné, že v určitém časovém limitu může dojít k zpoždění v případě nepravděpodobného zvýšení kapacity.  Nastavením minimálního minimálního počtu automatického vypočítaného minima rezervujete instance předem.

Zvýšení vypočítaného minima pro plán se dá provést pomocí Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostupné skladové položky instance

Při vytváření nebo škálování plánu si můžete vybrat mezi třemi velikostmi instancí.  Bude se vám účtovat celkový počet jader a zřízených paměti za sekundu, které jsou pro každou instanci přiděleny.  Vaše aplikace se může podle potřeby automaticky škálovat na více instancí.  

|SKU|Cores|Paměť|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|S frekvencí|250 GB|

### <a name="memory-utilization-considerations"></a>Požadavky na využití paměti
Spuštění v počítači, který má více paměti, neznamená vždycky, že vaše aplikace Function App bude používat veškerou dostupnou paměť.

Například aplikace funkcí JavaScriptu je omezená na výchozí omezení paměti v Node.js. Pokud chcete zvýšit toto omezení pevné paměti, přidejte nastavení aplikace `languageWorkers:node:arguments` s hodnotou `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Maximální horizontální navýšení kapacity oblasti

Níže jsou uvedeny aktuálně podporované maximální hodnoty škálování pro jeden plán v každé oblasti a konfiguraci operačního systému. Pokud chcete požádat o zvýšení, otevřete prosím lístek podpory.

Kompletní regionální dostupnost funkcí najdete tady: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

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
> [Pochopení možností škálování a hostování Azure Functions](functions-scale.md)
