---
title: Azure Functions vyhrazené hostování
description: Přečtěte si o výhodách spuštění Azure Functions ve vyhrazeném App Servicem plánu hostování.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704561"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Vyhrazené plány hostování pro Azure Functions

Tento článek se týká hostování aplikace Function App v plánu App Service, včetně App Service Environment (pomocného mechanismu). Další možnosti hostování naleznete v [článku plán hostování](functions-scale.md).

Plán App Service definuje sadu výpočetních prostředků, které má aplikace běžet. Tyto výpočetní prostředky jsou obdobou pro [_serverovou farmu_](https://wikipedia.org/wiki/Server_farm) v části konvenční hostování. Jedna nebo více aplikací funkcí se dá nakonfigurovat tak, aby běžely na stejných výpočetních prostředcích (App Service plán), jako jiné aplikace App Service, jako jsou například webové aplikace. Tyto plány zahrnují úrovně Basic, Standard, Premium a izolované SKU. Podrobnosti o tom, jak plán App Service funguje, najdete v podrobném [přehledu Azure App Service plány](../app-service/overview-hosting-plans.md).

Vezměte v úvahu App Service plán v následujících situacích:

* Máte existující a nevyužité virtuální počítače, které již spouštějí jiné instance App Service.
* Chcete zadat vlastní image, na které se mají spouštět vaše funkce.

## <a name="billing"></a>Fakturace

Platíte za aplikace Function App v plánu App Service, stejně jako u jiných prostředků App Service. To se liší od [plánu spotřeby](consumption-plan.md) Azure Functions nebo hostování [plánu Premium](functions-premium-plan.md) , ve kterém jsou k dispozici cenové komponenty založené na spotřebě. Účtuje se vám jenom plán, bez ohledu na to, kolik aplikací funkcí nebo webových aplikací se v plánu spouští. Další informace najdete na stránce s [cenami App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Vždy zapnuto

Pokud spustíte v plánu App Service, měli byste povolit nastavení **vždycky zapnuto** , aby aplikace Function App běžela správně. V App Serviceovém plánu se modul runtime Functions po několika minutách nečinnosti neukončí, takže se vaše funkce vystaví jenom triggery HTTP. Nastavení **Always On** je k dispozici pouze v plánu App Service. V plánu spotřeby platforma automaticky aktivuje aplikace funkcí.

I při zapnuté funkci Always On je časový limit spuštění pro jednotlivé funkce řízen `functionTimeout` nastavením v [host.js](functions-host-json.md#functiontimeout) v souboru projektu.

## <a name="scaling"></a>Škálování

Pomocí App Serviceho plánu můžete ruční horizontální navýšení kapacity přidáním dalších instancí virtuálních počítačů. Můžete také povolit automatické škálování, i když automatické škálování bude pomalejší než Elastické škálování plánu Premium. Další informace najdete v tématu [Ruční nebo automatické škálování počtu instancí](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Horizontální navýšení kapacity můžete také škálovat tak, že vyberete jiný plán App Service. Další informace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Při spouštění funkcí JavaScriptu (Node.js) v plánu App Service byste měli zvolit plán, který má méně vCPU. Další informace najdete v tématu [Výběr plánů App Service s jedním jádrem](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Prostředí App Service

Spuštění v App Service Environment (pomocného programu) vám umožní plně izolovat vaše funkce a využívat vyšší množství instancí než plán App Service. Informace o tom, jak začít, najdete v tématu [Úvod do prostředí App Service](../app-service/environment/intro.md).

Pokud chcete aplikaci Function App spustit jenom ve virtuální síti, můžete to udělat pomocí [plánu Premium](functions-premium-plan.md). Další informace najdete v tématu [vytvoření Azure Functions přístupu k privátní lokalitě](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Další kroky

+ [Azure Functions možností hostování](functions-scale.md)
+ [Přehled plánu služby App Service](../app-service/overview-hosting-plans.md)
