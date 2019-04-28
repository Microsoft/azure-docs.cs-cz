---
title: Plán služby Azure Functions Premium (preview) | Dokumentace Microsoftu
description: Podrobnosti a možnosti konfigurace (virtuální síť, bez úplné spuštění, dobu trvání neomezený počet spuštění) pro Azure Functions Premium plánu.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: d327146c4a1fa61e55bb904308038c1ce717123d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031161"
---
# <a name="azure-functions-premium-plan-preview"></a>Plán služby Azure Functions Premium (preview)

Plán Premium funkce Azure je možnost hostování pro aplikace function App. Plán Premium poskytuje funkce, jako je připojení virtuální sítě, bez úplné spuštění a premium hardwaru.  Více aplikací funkcí je možné nasadit do stejného plánu Premium, a plán můžete nakonfigurovat velikost výpočetních instancí, velikost základní plán a plán maximální velikost.  Porovnání plán Premium a dalších plánu a hostování typy najdete v tématu [funkce škálování a možnosti hostování](functions-scale.md).

> [!NOTE]
> Plán Premium, ve verzi preview v současnosti podporuje funkce spuštěné v .NET, uzel nebo Java přes infrastrukturu Windows.

## <a name="create-a-premium-plan"></a>Vytvoření plánu Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Plán Premium můžete vytvořit také z příkazového řádku Azure

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Funkce

Následující funkce jsou k dispozici funkce aplikace nasazené do plánu Premium.

### <a name="pre-warmed-instances"></a>Předem topným zařízením instancí

Pokud žádné události resp. dojde k ještě dnes v plánu Consumption, může vaše aplikace škálovat nula instance. Když se dělí na nové události, novou instanci je nutné zadat s vaší aplikací v něm spuštěný.  Specializace nové instance může trvat nějakou dobu v závislosti na aplikaci.  Tato další latence při prvním volání se často nazývá úplné spuštění aplikace.

V plánu Premium můžete mít aplikace předem zahřívají na zadaný počet instancí, až velikost vašeho plánu minimální.  Předem topným zařízením instance také umožňují předem škálovat aplikace před nadměrným zatížením. Jak aplikace horizontálně navýší kapacitu, nejprve se škáluje do předem topným zařízením instancí. Další instancí se dál do vyrovnávací paměti navýšení kapacity a teplé okamžitě v rámci přípravy na další operaci škálování. Tím, že vyrovnávací paměť předem topným zařízením instancí, můžete efektivně vyhnout latenci studený start.  Předem topným zařízením instance funkce je součástí plánu Premium a je potřeba nechat alespoň jednu instanci spuštěné a dostupné za všech okolností plán není aktivní.

Počet instancí předem topným zařízením na portálu Azure portal můžete nakonfigurovat tak, že vyberete **horizontální navýšení kapacity** v **funkce platformy** kartu.

![Nastavení elastické škálování](./media/functions-premium-plan/scale-out.png)

Můžete taky nakonfigurovat předem topným zařízením instancí aplikace pomocí Azure CLI

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Připojení k privátní síti

Využívá výhod platformy Azure Functions, které jsou nasazené na tarif Premium [nově zavedené integraci virtuální sítě pro službu web apps](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration).  Při konfiguraci, vaše aplikace můžou komunikovat s prostředky v rámci vaší virtuální sítě nebo zabezpečené pomocí koncových bodů služby.  Omezení IP adres jsou také k dispozici v aplikaci omezit příchozí provoz.

Při přiřazování podsíť k vaší aplikaci funkcí v plánu Premium, musíte pro každou instanci potenciální dostatek IP adres podsítě. I když maximální počet instancí se mohou lišit ve verzi preview, požadujeme, aby blok IP s minimálně 100 dostupnými adresami.

Další informace najdete v článku [integrovat své aplikaci function app pomocí virtuální sítě](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Rychlé elastické škálování

Pro svou aplikaci pomocí stejné logiky, Rychlé škálování jako plánu Consumption se automaticky přidají další výpočetní instance.  Další informace o tom, jak škálování funguje, najdete v článku [funkce hostování a škálování](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Doba trvání běhu nástrojů unbounded

Služba Azure Functions v plánu Consumption jsou omezené na 10 minut, než jedno provedení.  V plánu Premium doba trvání běhu výchozí hodnota je 30 minut, aby se zabránilo vyčerpává dlouho běžící operace provedení. Můžete však [změny konfigurace host.json](./functions-host-json.md#functiontimeout) to lze provést bez vazby u aplikací úrovně Premium plánu.

Doby trvání ve verzi preview, není zaručeno, že posledních 12 minut a bude mít největší šanci spuštěných déle než 30 minut, pokud vaše aplikace není škálování nad rámec jeho počet minimální pracovního procesu.

## <a name="plan-and-sku-settings"></a>Nastavení plánu a skladové položky

Při vytváření plánu, můžete nakonfigurovat dvě nastavení: minimální počet instancí (nebo plán velikosti) a shluků maximální limit.  Minimální instance plánu Premium je 1 a maximální shluků ve verzi preview je 20.  Minimální instance jsou vyhrazené a vždy spuštěné.

> [!IMPORTANT]
> Se vám účtovat každá instance přidělených v minimální počet instancí bez ohledu na to, pokud funkce jsou spuštěny, nebo ne.

Pokud vaše aplikace vyžaduje instance nad rámec velikost vašeho plánu, můžete pokračovat pro horizontální navýšení kapacity, dokud se limit maximální burst narazí na počet instancí.  Účtují se vám pro instance nad rámec velikost vašeho plánu pouze při jejich byly spuštěny a pronajatých vám.  Učiníme pokusí v aplikaci si tak, aby definovaný maximální limit škálování zatímco instancí minimální plánu je zaručeno, že pro vaši aplikaci.

Můžete nakonfigurovat plán velikost a maximální hodnoty na webu Azure Portal ve vybrané **horizontální navýšení kapacity** možnosti v plánu nebo aplikace function app nasadit do tohoto plánu (v části **funkce platformy**).

Může také zvýšit limit maximální burst z příkazového řádku Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>K dispozici instance SKU

Při vytváření naše škálování plánu, můžete mezi tři velikosti instance.  Bude se vám účtovat celkový počet jader a paměti spotřebované za sekundu.  Vaše aplikace může automaticky škálovat na více instancí podle potřeby.  

|Skladová jednotka (SKU)|Jádra|Memory (Paměť)|Úložiště|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

## <a name="regions"></a>Oblasti

V následující tabulce jsou aktuálně podporované oblasti pro verzi public preview.

|Oblast|
|--|
|Austrálie – východ|
|Austrálie – jihovýchod|
|Kanada – střed|
|USA – střed|
|Východní Asie|
|Východní USA 2|
|Francie – střed|
|Japonsko – západ|
|Korea – střed|
|Severní Evropa|
|Středojižní USA|
|Indie – jih|
|Jihovýchodní Asie|
|Spojené království – západ|
|Západní Evropa|
|Indie – západ|
|Západní USA|

## <a name="known-issues"></a>Známé problémy

Můžete sledovat stav známé problémy [verzi public preview na Githubu](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vysvětlení možností hostování a škálování Azure Functions](functions-scale.md)
