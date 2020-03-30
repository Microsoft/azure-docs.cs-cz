---
title: Automatické škálování v Azure pomocí vlastní metriky
description: Přečtěte si, jak škálovat prostředky podle vlastní metriky v Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425115"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Začínáme s automatickým škálování podle vlastní metriky v Azure
Tento článek popisuje, jak škálovat prostředek podle vlastní metriky na webu Azure Portal.

Automatické škálování Azure Monitor uplatní jenom [pro škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), [aplikační služby – webové aplikace](https://azure.microsoft.com/services/app-service/web/), cluster Azure Data [Explorer](https://azure.microsoft.com/services/data-explorer/) ,   
Integrace Service Prostředí a [api management služby](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Umožňuje začít
Tento článek předpokládá, že máte webovou aplikaci s nakonfigurovanýmpřehledem aplikací. Pokud ještě nemáte, můžete nastavit [Application Insights pro ASP.NET web][1]

- Otevření [portálu Azure][2]
- Klikněte na ikonu Azure Monitor v levém navigačním podokně.
  ![Spuštění Azure Monitoru][3]
- Kliknutím na nastavení automatického škálování zobrazíte všechny prostředky, pro které ![se používá automatické škálování, spolu s aktuálním stavem automatického škálování Zjišťujte automatické škálování v Monitoru Azure.][4]
- Otevřete okno automatického škálování ve službě Azure Monitor a vyberte prostředek, který chcete škálovat.
  > Poznámka: Níže uvedené kroky používají plán služby App Service přidružený k webové aplikaci, která má nakonfigurované přehledy aplikací.
- V okně nastavení měřítka pro prostředek si všimněte, že počet aktuálních instancí je 1. Klikněte na "Povolit automatické škálování".
  ![Nastavení škálování pro novou webovou aplikaci][5]
- Zadejte název pro nastavení měřítka a klikněte na "Přidat pravidlo". Všimněte si možností pravidla škálování, které se otevřou jako podokno kontextu na pravé straně. Ve výchozím nastavení nastaví možnost škálovat počet instancí o 1, pokud procento procesoru prostředku překročí 70 %. Změňte zdroj metriky v horní části na "Application Insights", vyberte prostředek přehledy aplikací v rozevíracím seznamu Prostředek a pak vyberte vlastní metriku, na základě které chcete škálovat.
  ![Škálování podle vlastní metriky][6]
- Podobně jako výše uvedený krok přidejte pravidlo škálování, které bude škálovat a snižovat počet měřítek o 1, pokud je vlastní metrika pod prahovou hodnotou.
  ![Škálování na základě procesoru][7]
- Nastavte limity instancí. Chcete-li například škálovat mezi 2 až 5 instancemi v závislosti na vlastních výkyvech metrik, nastavte "minimum" na "2", "maximum" na "5" a "výchozí" na "2"
  > Poznámka: V případě, že dojde k potížím se čtením metrik prostředků a aktuální kapacita je nižší než výchozí kapacita, pak pro zajištění dostupnosti prostředku se automatické škálování navádí na výchozí hodnotu. Pokud je aktuální kapacita již vyšší než výchozí kapacita, automatické škálování nebude škálovat.
- Klikněte na 'Uložit'

Blahopřejeme. Nyní jste úspěšně vytvořili nastavení škálování pro automatické škálování webové aplikace na základě vlastní metriky.

> Poznámka: Stejné kroky platí pro zahájení práce s rolí VMSS nebo cloudové služby.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

