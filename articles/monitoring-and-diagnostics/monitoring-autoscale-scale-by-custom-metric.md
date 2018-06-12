---
title: Škálování v Azure pomocí vlastní metriku
description: Zjistěte, jak se škálovat prostředek podle vlastní metriky v Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 739ef5423f7b1769fa793f0cac5306efa634b781
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262969"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Začínáme s automatické škálování podle vlastní metriky v Azure
Tento článek popisuje postup škálování prostředku podle vlastní metriky v portálu Azure.

Azure monitorování automatického škálování se vztahuje pouze na virtuální počítač škálování sady (VMSS), cloudové služby, plány služby app a prostředí app service. 

# <a name="lets-get-started"></a>Umožňuje Začínáme
Tento článek předpokládá, že máte webovou aplikaci pomocí nástroje application insights nakonfigurované. Pokud nemáte již, můžete [nastavte Application Insights pro váš web ASP.NET][1]

- Otevřete [portálu Azure][2]
- Klikněte na ikonu monitorování Azure v levém navigačním podokně.
  ![Spustit sledování Azure][3]
- Klikněte na nastavení automatického škálování, chcete-li zobrazit všechny prostředky, na které automatického škálování se vztahuje, společně s jeho aktuální stav škálování ![zjistit automatického měřítka v Azure monitorování][4]
- Otevřete okno 'Škálování' v Azure monitorování a vyberte prostředek, který chcete škálovat
> Poznámka: Následující postup použijte plán služby app service přidružené k webové aplikaci, která má Statistika aplikace nakonfigurovaná.
- V okně Nastavení škálování pro prostředek Všimněte si, že je aktuální počet instancí 1. Klikněte na 'Povolit škálování'.
  ![Nastavení škálování pro novou webovou aplikaci][5]
- Zadejte název pro nastavení škálování a klikněte na "Přidat pravidlo". Všimněte si, že pravidlo škálování možnostech, které se otevře jako kontext podokno v pravé straně. Ve výchozím nastavení nastaví možnost škálování vašeho počet instancí 1, pokud percetage procesoru prostředku je vyšší než 70 %. Změnit metriky zdroje v horní části na "Application Insights", vyberte prostředek Statistika aplikace v rozevírací nabídce 'prostředků a pak vyberte vlastní metrika na základě na kterou chcete škálovat.
  ![Škálování podle vlastní metriky][6]
- Podobně jako výše, přidejte pravidlo škálování, který bude škálovat v a snížit počet škálování o 1, pokud vlastní metrika je pod prahovou hodnotu.
  ![Škálování podle využití procesoru][7]
- Nastavit vy instance omezení. Například pokud chcete změnit měřítko mezi instancemi 2 až 5 v závislosti na vlastní metriky kolísání, nastavte minimální na "2", maximální "5" a "default" na "2"
> Poznámka: V případě, že je potíže při čtení metrika prostředků a aktuální kapacita je nižší než výchozí kapacity, pak pro zajištění dostupnosti prostředků, automatické škálování bude škálovat na výchozí hodnotu. Pokud je aktuální kapacita již vyšší než výchozí kapacita, nebude v škálovat škálování.
- Klikněte na 'uložit.

Blahopřejeme. Jste teď úspěšně vytvořili vaší škálování nastavení na automatické škálování webové aplikace založené na vlastní metriku.

> Poznámka: Stejný postup platí pro začít pracovat s rolí služby VMSS nebo cloud.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
