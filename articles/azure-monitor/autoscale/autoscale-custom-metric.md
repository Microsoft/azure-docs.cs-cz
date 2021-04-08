---
title: Automatické škálování v Azure s využitím vlastní metriky
description: Naučte se škálovat prostředky podle vlastní metriky v Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 8e744e6a91eb6fbe23a6b45f95c39b1acfdcb61f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610931"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Začínáme s automatickým škálováním podle vlastní metriky v Azure
Tento článek popisuje, jak škálovat prostředek podle vlastní metriky v Azure Portal.

Automatické škálování Azure Monitor platí jenom pro [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [cluster Azure Průzkumník dat](https://azure.microsoft.com/services/data-explorer/) .   
Služby prostředí integrační služby a [API Management](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Umožňuje začít
V tomto článku se předpokládá, že máte nakonfigurované webové aplikace s Application Insights. Pokud ho ještě nemáte, můžete [pro web ASP.NET nastavit Application Insights][1] .

- Otevřít [Azure Portal][2]
- V levém navigačním podokně klikněte na ikonu Azure Monitor.
  ![Spustit Azure Monitor][3]
- Kliknutím na nastavení automatického škálování zobrazíte všechny prostředky, pro které se má použít automatické škálování, spolu s aktuálním stavem ![ automatického škálování zjišťovat automatické škálování ve službě Azure monitor.][4]
- Otevřete okno Automatické škálování v Azure Monitor a vyberte prostředek, který chcete škálovat.
  > Poznámka: následující postup používá plán služby App Service přidružený k webové aplikaci, která má nakonfigurované App Insights.
- V okně nastavení škálování pro prostředek si všimněte, že aktuální počet instancí je 1. Klikněte na Povolit automatické škálování.
  ![Nastavení škálování pro novou webovou aplikaci][5]
- Zadejte název nastavení škálování a klikněte na Přidat pravidlo. Všimněte si možností pravidla škálování, která se otevře jako kontextové podokno na pravé straně. Ve výchozím nastavení nastaví možnost škálování počtu instancí o 1, pokud procento prostředku procesoru překročí 70%. Změňte zdroj metriky v horní části na "Application Insights", vyberte prostředek App Insights v rozevíracím seznamu prostředek a pak vyberte vlastní metriku podle toho, kterou chcete škálovat.
  ![Škálování podle vlastní metriky][6]
- Podobně jako v předchozím kroku přidejte pravidlo škálování, které se bude škálovat a snížit počet stupnic podle 1, pokud je vlastní metrika pod prahovou hodnotou.
  ![Škálování na základě procesoru][7]
- Nastavte limity instancí. Pokud například chcete škálovat mezi 2-5 instancemi v závislosti na vlastních výkyvech metrik, nastavte ' minimum ' na ' 2 ', ' maximum ' na ' 5 ' a ' výchozí ' na hodnotu ' 2 '.
  > Poznámka: v případě, že dojde k potížím při čtení metriky prostředků a aktuální kapacita je pod výchozí kapacitou, pak bude automatické škálování škálovat na výchozí hodnotu. Pokud je aktuální kapacita již vyšší než výchozí kapacita, automatické škálování se nebude škálovat.
- Klikněte na Uložit.

Blahopřejeme. Teď jste úspěšně vytvořili nastavení škálování pro automatické škálování webové aplikace na základě vlastní metriky.

> Poznámka: stejný postup platí pro zahájení práce s rolí VMSS nebo cloudové služby.

<!--Reference-->
[1]: ../app/asp-net.md
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png
