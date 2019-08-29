---
title: Plán Premium pro Azure Functions (Preview) | Microsoft Docs
description: Podrobnosti a možnosti konfigurace (virtuální síť, bez počátečního startu, neomezené trvání spuštění) pro plán Azure Functions Premium.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: 2cc6493d01508d439d8dcef2d12ca1ea40632d81
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096233"
---
# <a name="azure-functions-premium-plan-preview"></a>Plán Azure Functions Premium (Preview)

Plán Azure Functions Premium je možnost hostování pro aplikace Function App. Plán Premium poskytuje funkce, jako je připojení k virtuální síti, žádný studený start a špičkový hardware.  Do stejného plánu Premium se dá nasadit víc aplikací Function App a plán vám umožní nakonfigurovat velikost instance COMPUTE, velikost základního plánu a maximální velikost plánu.  Porovnání plánu Premium a dalších typů plánování a hostování najdete v tématu [možnosti škálování a hostování funkcí](functions-scale.md).

## <a name="create-a-premium-plan"></a>Vytvořit plán Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Plán Premium můžete vytvořit také pomocí příkaz [AZ functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) v rozhraní příkazového řádku Azure CLI. Následující příklad vytvoří plán vrstvy _elastické úrovně Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

V tomto příkladu nahraďte `<RESOURCE_GROUP>` svou skupinou prostředků a `<PLAN_NAME>` názvem vašeho plánu, který je ve skupině prostředků jedinečný. Zadejte [podporovanou `<REGION>` ](#regions)hodnotu. Pokud chcete vytvořit plán Premium, který podporuje Linux, zahrňte `--is-linux` možnost.

Pomocí vytvořeného plánu můžete vytvořit aplikaci Function App pomocí [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Na portálu se současně vytvoří plán i aplikace. 

## <a name="features"></a>Funkce

K dispozici jsou následující funkce pro aplikace Functions nasazené do plánu Premium.

### <a name="pre-warmed-instances"></a>Předem zahřívání instance

Pokud v plánu spotřeby nejsou žádné události a spuštění, vaše aplikace může škálovat dolů na nulové instance. Když se přidají nové události v, musí být speciální instance specializovaná na svou aplikaci, která je na ní spuštěná.  Specializace nových instancí může v závislosti na aplikaci nějakou dobu trvat.  Tato další latence při prvním volání se často označuje jako studený start aplikace.

V plánu Premium můžete mít aplikaci předem zahřívání na určitém počtu instancí až do minimální velikosti plánu.  Předem zavedené instance také umožňují předem škálovat aplikaci před velkým objemem zátěže. Vzhledem k tomu, že se aplikace škáluje, nejprve se škáluje do předem zahřívání instancí. Další instance pokračují ve vyrovnávací paměti a zahřívá se hned po přípravě na další operaci škálování. Když máte vyrovnávací paměť předběžně zavedených instancí, můžete efektivně zabránit latenci při počátečním startu.  Předem zavedené instance jsou součástí plánu Premium a je potřeba, abyste zachovali aspoň jednu instanci, která je spuštěná a dostupná vždy, když je plán aktivní.

Počet předem zavedených instancí můžete v Azure Portal nakonfigurovat tak, že vyberete **Function App**a kliknete na kartu **funkce platformy** a vyberete možnosti horizontálního navýšení **kapacity** . V okně pro úpravu aplikace Function App jsou předem zavedené instance specifické pro danou aplikaci, ale minimální a maximální počet instancí platí pro celý plán.

![Nastavení elastického škálování](./media/functions-premium-plan/scale-out.png)

Můžete taky nakonfigurovat předem zavedené instance pro aplikaci pomocí Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Připojení k privátní síti

Azure Functions nasazené do plánu Premium využívá [novou integraci virtuální sítě pro webové aplikace](../app-service/web-sites-integrate-with-vnet.md).  Když nakonfigurujete aplikaci, může komunikovat s prostředky ve vaší virtuální síti nebo zabezpečená prostřednictvím koncových bodů služby.  V aplikaci jsou k dispozici také omezení IP pro omezení příchozího provozu.

Při přiřazování podsítě do aplikace Function App v plánu Premium budete potřebovat podsíť s dostatečnými IP adresami pro každou potenciální instanci. I když se maximální počet instancí může ve verzi Preview lišit, potřebujeme blok IP adres s minimálně 100 dostupnými adresami.

Další informace najdete v tématu [integrace aplikace Function App s virtuální](functions-create-vnet.md)sítí.

### <a name="rapid-elastic-scale"></a>Rychlé Elastické škálování

Další výpočetní instance se automaticky přidají do vaší aplikace pomocí stejné logiky rychlé škálování jako u plánu spotřeby.  Další informace o tom, jak škálování funguje, najdete v tématu [škálování a hostování funkcí](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Nevázaná doba běhu

Azure Functions v plánu spotřeby se pro jedno spuštění omezí na 10 minut.  V plánu Premium je doba běhu standardně 30 minut, aby se zabránilo provádění. Můžete ale [Upravit konfiguraci Host. JSON](./functions-host-json.md#functiontimeout) , abyste to nezajistili pro aplikace Premium Plan.

Ve verzi Preview není doba trvání zaručena po dobu 12 minut a bude mít nejlepší pravděpodobnost, že bude trvat déle než 30 minut, pokud se vaše aplikace škáluje nad rámec minimálního počtu pracovních procesů.

## <a name="plan-and-sku-settings"></a>Nastavení plánu a SKU

Při vytváření plánu nakonfigurujete dvě nastavení: minimální počet instancí (nebo velikost plánu) a maximální limit shlukování.  Minimální počet instancí plánu Premium je 1 a maximální nárůst v průběhu verze Preview je 20.  Minimální instance jsou rezervované a vždycky spuštěné.

> [!IMPORTANT]
> Za každou instanci přidělenou v minimálním počtu instancí se účtuje bez ohledu na to, jestli jsou funkce spuštěné nebo ne.

Pokud vaše aplikace vyžaduje instance nad rámec velikosti vašeho plánu, může pokračovat horizontální navýšení kapacity, dokud počet instancí nedosáhne maximálního limitu shlukování.  Účtují se za instance přesahující váš plán jenom v době, kdy jsou spuštěné a pronajaté.  Připravujeme úsilí, aby se vaše aplikace přihlásila na vymezený maximální limit, zatímco pro vaši aplikaci jsou zaručené minimální instance plánu.

Velikost plánu a maximum v Azure Portal můžete nakonfigurovat tak, že vyberete možnosti horizontálního navýšení **kapacity** v plánu nebo aplikaci Function App nasazené do tohoto plánu (v části **funkce platformy**).

Můžete taky zvýšit maximální limit shluku z Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostupné skladové položky instance

Při vytváření nebo škálování plánu si můžete vybrat mezi třemi velikostmi instancí.  Bude se vám účtovat celkový počet jader a využité paměti za sekundu.  Vaše aplikace se může podle potřeby automaticky škálovat na více instancí.  

|SKU|Jádra|Memory (Paměť)|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|S FREKVENCÍ|250 GB|

## <a name="regions"></a>Regions

Níže jsou uvedené aktuálně podporované oblasti pro verzi Public Preview pro každý operační systém.

|Oblast| Windows | Linux |
|--| -- | -- |
|Austrálie – východ| ✔ | |
|Austrálie – jihovýchod | ✔ | ✔ |
|Kanada – střed| ✔ |  |
|Střed USA| ✔ |  |
|Východní Asie| ✔ |  |
|East US | | ✔ |
|Východní USA 2| ✔ |  |
|Francie – střed| ✔ |  |
|Japonsko – východ|  | ✔ |
|Japonsko – západ| ✔ | |
|Jižní Korea – střed| ✔ |  |
|Střed USA – sever| ✔ |  |
|Severní Evropa| ✔ | ✔ |
|Střed USA – jih| ✔ |  |
|Jižní Indie | ✔ | |
|Jihovýchodní Asie| ✔ | ✔ |
|Spojené království – západ| ✔ |  |
|Západní Evropa| ✔ | ✔ |
|Indie – západ| ✔ |  |
|USA – západ| ✔ | ✔ |

## <a name="known-issues"></a>Známé problémy

Stav známých problémů [veřejné verze Preview](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues)můžete sledovat na GitHubu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Pochopení možností škálování a hostování Azure Functions](functions-scale.md)
