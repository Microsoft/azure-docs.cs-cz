---
title: Automatické škálování v Azure s použitím vlastních metrik
description: Zjistěte, jak můžete škálovat prostředek vlastní metrika v Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 97836c4160349b8095ba2095176783ae17b46e82
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216195"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Začínáme s automatické škálování na vlastní metrika v Azure
Tento článek popisuje, jak škálovat prostředek vlastní metriky na webu Azure portal.

Azure Monitor automatické škálování se vztahuje pouze na virtuální počítač Škálovací sady (VMSS), cloud services, plánů služby app service a app service Environment. 

# <a name="lets-get-started"></a>Umožňuje začít
Tento článek předpokládá, že máte webové aplikace pomocí application insights, které jsou nakonfigurované. Pokud je nemáte, můžete si [nastavení Application Insights pro váš web ASP.NET][1]

- Otevřít [webu Azure portal][2]
- Klikněte na ikonu Azure Monitor v levém navigačním podokně.
  ![Spusťte Azure Monitor][3]
- Klikněte na nastavení automatického škálování, chcete-li zobrazit všechny prostředky, pro které automatického škálování je použít společně s jeho aktuální stav automatického škálování ![zjistit automatické škálování ve službě Azure monitor][4]
- Otevře se okno "Automatického škálování" ve službě Azure Monitor a vyberte prostředek, který chcete škálovat
> Poznámka: Následující postup použijte plán služby app service přidružené webové aplikace, která má app insights nakonfigurované.
- V okně Nastavení škálování pro prostředek Všimněte si, že je aktuální počet instancí 1. Klikněte na "Povolit automatické škálování".
  ![Nastavení škálování pro novou webovou aplikaci][5]
- Zadejte název pro nastavení škálování a klikněte na "Přidat pravidlo". Všimněte si, že pravidla možností škálování, které se otevře jako kontextovém podokně na pravé straně. Ve výchozím nastavení nastaví možnost škálování vašeho počtu instancí o 1, pokud procesor percetage prostředku přesáhne 70 %. Změnit zdroj metriky v horní části na "Application Insights", vyberte prostředek app insights v rozevíracím seznamu "Prostředek" a pak vyberte vlastní metrika založené na který chcete škálovat.
  ![Škálování podle vlastních metrik][6]
- Podobně jako výše, přidejte pravidlo škálování, které budou škálovat v a snížit počet škálování o 1, pokud je vlastní metrika je pod prahovou hodnotu.
  ![Škálování podle využití procesoru][7]
- Vy nastavit limity instancí. Například pokud chcete škálovat mezi instancemi 2 – 5 v závislosti na vlastní metriky kolísání, nastavte "minimální" na "2", "maximální" na "5" a "default" na "2"
> Poznámka: V případě dojde k nějakému problému čtení metrik prostředku a aktuální kapacita bude pod výchozí kapacitou, pak a zajistit tak dostupnost prostředků, automatického škálování se horizontální navýšení kapacity na výchozí hodnotu. Pokud už je aktuální kapacita vyšší než výchozí kapacita pro dotazy, automatické škálování neprovede horizontální snížení.
- Klikněte na "uložit.

Blahopřejeme. Teď byl úspěšně vytvořen vaše nastavení automatického škálování škálovat webové aplikace na základě vlastní metriky.

> Poznámka: Stejný postup platí pro začít pracovat s rolí služby VMSS nebo v cloudu.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
