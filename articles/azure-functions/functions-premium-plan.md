---
title: Plán Azure Functions Premium
description: Podrobnosti a možnosti konfigurace (virtuální síť, žádný studený start, neomezená doba provádění) pro plán Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276904"
---
# <a name="azure-functions-premium-plan"></a>Plán Azure Functions Premium

Plán Azure Functions Premium (někdy označovaný jako elastický plán Premium) je možnost hostování pro funkční aplikace. Plán Premium poskytuje funkce, jako je připojení virtuální sítě, žádný studený start a prémiový hardware.  Více funkčních aplikací lze nasadit do stejného plánu Premium a plán umožňuje konfigurovat velikost výpočetní instance, velikost základního plánu a maximální velikost plánu.  Porovnání plánu Premium a dalších typů plánu a hostování naleznete v [tématu škálování funkcí a možnosti hostování](functions-scale.md).

## <a name="create-a-premium-plan"></a>Vytvoření prémiového tarifu

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Můžete také vytvořit plán Premium pomocí [az functionapp plán vytvořit](/cli/azure/functionapp/plan#az-functionapp-plan-create) v Azure CLI. Následující příklad vytvoří plán úrovně _Elastic Premium 1:_

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

V tomto příkladu `<RESOURCE_GROUP>` nahraďte `<PLAN_NAME>` skupinou prostředků a názvem plánu, který je jedinečný ve skupině prostředků. Zadejte [ `<REGION>`podporovanou ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Chcete-li vytvořit plán Premium, `--is-linux` který podporuje Linux, zahrňte tuto možnost.

S vytvořeným plánem můžete použít [az functionapp vytvořit](/cli/azure/functionapp#az-functionapp-create) vytvořit aplikaci funkce. Na portálu jsou současně vytvořeny plán i aplikace. Příklad úplného skriptu vpříkazkového příkazu k Řešení Azure najdete [v tématu Vytvoření aplikace funkcí v plánu Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funkce

Následující funkce jsou k dispozici pro funkční aplikace nasazené do plánu Premium.

### <a name="pre-warmed-instances"></a>Předohřívané instance

Pokud žádné události a spuštění dojít dnes v plánu spotřebu, vaše aplikace může škálovat na nulu instancí. Když přijdou nové události, nová instance musí být specializovaná s vaší aplikací spuštěnou na ní.  Specializace nových instancí může v závislosti na aplikaci nějakou dobu trvat.  Tato další latence při prvním volání se často nazývá studený start aplikace.

V plánu Premium můžete aplikaci předehřívat na zadaný počet instancí, a to až do minimální velikosti plánu.  Předohřívané instance také umožňují předem škálovat aplikaci před vysokým zatížením. Jak se aplikace škáluje, nejprve se škáluje do předohřívaných instancí. Další instance nadále vyrovnávací paměti a okamžitě zahřát v rámci přípravy na další operace škálování. Tím, že vyrovnávací paměť předohřívaných instancí, můžete účinně vyhnout studené spuštění latence.  Předohřívané instance je funkce plánu Premium a musíte zachovat alespoň jednu instanci spuštěnou a dostupnou po celou dobu, kdy je plán aktivní.

Počet předehřívaných instancí na webu Azure Portal můžete nakonfigurovat tak, že vyberete **aplikaci funkce**, přejdete na kartu **Funkce platformy** a vyberete možnosti **horizontálnínavýšení kapacity.** V okně úprav aplikace funkce je předohřívaná instance specifická pro tuto aplikaci, ale minimální a maximální instance se vztahují na celý plán.

![Nastavení elastického měřítka](./media/functions-premium-plan/scale-out.png)

Můžete také nakonfigurovat předehřívané instance pro aplikaci pomocí rozhraní příkazového příkazu k řešení Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Připojení k privátní síti

Funkce Azure nasazené do plánu Premium využívají [výhod nové integrace virtuální sítě pro webové aplikace](../app-service/web-sites-integrate-with-vnet.md).  Když je vaše aplikace nakonfigurovaná, může komunikovat s prostředky v rámci vaší virtuální sítě nebo zabezpečená prostřednictvím koncových bodů služby.  Omezení IP adres jsou k dispozici také v aplikaci pro omezení příchozího provozu.

Při přiřazování podsítě k aplikaci funkcí v plánu Premium potřebujete podsíť s dostatkem IP adres pro každou potenciální instanci. Požadujeme IP blok s nejméně 100 dostupnými adresami.

Další informace najdete [v tématu integrace aplikace funkce s virtuální sítí](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Rychlé elastické měřítko

Další výpočetní instance se automaticky přidají pro vaši aplikaci pomocí stejné logiky rychlého škálování jako plán Spotřeba.  Další informace o tom, jak škálování funguje, najdete v [tématu Měřítko funkcí a hostování](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Delší doba trvání

Funkce Azure v plánu spotřeby jsou omezeny na 10 minut pro jedno spuštění.  V plánu Premium doba trvání spuštění výchozí 30 minut, aby se zabránilo spuštění útěku. [Konfiguraci souboru host.json](./functions-host-json.md#functiontimeout) však můžete upravit tak, aby byla pro aplikace plánu Premium neohraničená (zaručeně 60 minut).

## <a name="plan-and-sku-settings"></a>Nastavení plánu a skladové položky

Při vytváření plánu nakonfigurujete dvě nastavení: minimální počet instancí (nebo velikost plánu) a maximální limit sériového sutržení.  Minimální instance jsou vyhrazeny a vždy spuštěny.

> [!IMPORTANT]
> Poplatky se vám účtují za každou instanci přidělenou v minimálním počtu instancí bez ohledu na to, zda jsou funkce prováděny či nikoli.

Pokud vaše aplikace vyžaduje instance nad rámec velikosti plánu, může pokračovat v horizontálním navýšení kapacity, dokud počet instancí nedosáhne maximálního limitu sériového shlukování.  Fakturuje se vám za případy, které přesahují velikost plánu, pouze v době, kdy jsou spuštěny a pronajaty vám.  Vynasnažíme se škálovat na definovanou maximální limit, zatímco pro vaši aplikaci jsou zaručeny minimální instance plánu.

Velikost a maxima plánu můžete nakonfigurovat na webu Azure Portal výběrem možností **horizontálního navýšení kapacity** v plánu nebo aplikace funkce nasazené do tohoto plánu (v části **Funkce platformy).**

Můžete také zvýšit maximální limit burst z Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostupné souaplikace instance

Při vytváření nebo změně velikosti plánu si můžete vybrat mezi třemi velikostmi instancí.  Bude se vám účtovat celkový počet jader a paměti spotřebované za sekundu.  Vaše aplikace může automaticky horizontální navýšení kapacity na více instancí podle potřeby.  

|Skladová jednotka (SKU)|Cores|Memory (Paměť)|Úložiště|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Důležité informace o využití paměti
Spuštění na počítači s více paměti nemusí vždy znamenat, že vaše aplikace funkce bude používat všechny dostupné paměti.

Například aplikace funkce Jazyka JavaScript je omezena výchozím limitem paměti v souboru Node.js. Chcete-li tento pevný limit paměti `languageWorkers:node:arguments` zvýšit, `--max-old-space-size=<max memory in MB>`přidejte nastavení aplikace s hodnotou .

## <a name="region-max-scale-out"></a>Maximální měřítko oblasti

Níže jsou aktuálně podporované maximální škálování hodnoty pro jeden plán v každé oblasti a konfiguraci operačního systému. Chcete-li požádat o zvýšení, otevřete lístek podpory.

Kompletní regionální dostupnost funkcí naleznete zde: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region (Oblast)| Windows | Linux |
|--| -- | -- |
|Austrálie – střed| 20 | Není k dispozici |
|Austrálie – střed 2| 20 | Není k dispozici |
|Austrálie – východ| 100 | 20 |
|Austrálie – jihovýchod | 100 | 20 |
|Brazílie – jih| 60 | 20 |
|Střední Kanada| 100 | 20 |
|USA – střed| 100 | 20 |
|Východní Asie| 100 | 20 |
|USA – východ | 100 | 20 |
|USA – východ 2| 100 | 20 |
|Francie – střed| 100 | 20 |
|Německo – západ – střed| 100 | Není k dispozici |
|Japonsko – východ| 100 | 20 |
|Japonsko – západ| 100 | 20 |
|Jižní Korea – střed| 100 | 20 |
|USA – středosever| 100 | 20 |
|Severní Evropa| 100 | 20 |
|Norsko východ| 20 | 20 |
|USA – středojih| 100 | 20 |
|Indie – jih | 100 | Není k dispozici |
|Jihovýchodní Asie| 100 | 20 |
|Spojené království – jih| 100 | 20 |
|Spojené království – západ| 100 | 20 |
|Západní Evropa| 100 | 20 |
|Indie – západ| 100 | 20 |
|USA – středozápad| 20 | 20 |
|USA – západ| 100 | 20 |
|USA – západ 2| 100 | 20 |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Principy škálování funkcí Azure a možností hostování](functions-scale.md)
