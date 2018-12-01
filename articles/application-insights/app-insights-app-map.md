---
title: Mapa aplikace ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování topologie komplexních aplikací s Mapa aplikace
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 1ecdbdfb657d0372fea87c4260226f9de8ded9ce
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682499"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikace: Třídění distribuovaných aplikací

Mapa aplikace umožňuje přímé výkonnostní kritické body nebo selhání hotspotům pro všechny součásti distribuované aplikace. Každý uzel na mapě představuje určité součásti aplikace nebo její závislosti; a má stav klíčového ukazatele výkonu a výstrahy stavu. Můžete kliknutím z libovolné komponenty na podrobnější diagnostiku, jako jsou události služby Application Insights. Pokud vaše aplikace používá služby Azure, můžete také kliknout prostřednictvím a Diagnostika Azure, jako je SQL Database Advisor doporučení.

## <a name="what-is-a-component"></a>Co je komponenta?

Součásti jsou umožňují nezávislé nasazení částí aplikace distribuovat/mikroslužeb. Vývojáři provozní týmy a týmy mají viditelnost na úrovni kódu nebo přístup k telemetrii generovanou těmito součásti aplikace. 

* Komponenty se liší od "zjištěnou" externí závislosti, jako je SQL, atd centra událostí, které tým nebo organizace nemusí mít přístup k (kód nebo telemetrické údaje).
* Komponenty jsou spuštěny na libovolný počet instancí role/server/kontejner.
* Součástí může být samostatný instrumentačních klíčů Application Insights (i v případě, že předplatná se liší) nebo různé role, generování sestav na jeden Instrumentační klíč Application Insights. Mapování prostředí ve verzi preview se zobrazí komponenty bez ohledu na to, jak jsou nastavené.

## <a name="composite-application-map"></a>Mapa kompozitní aplikace

Zobrazí se topologie celou aplikaci napříč několika úrovněmi souvisejících aplikací komponenty. Součástí může být různé prostředky Application Insights nebo různé role v jediném prostředku. Mapa aplikace vyhledá součásti podle následujícího volání závislostí protokolu HTTP mezi servery pomocí Application Insights SDK nainstalovat. 

Toto prostředí začíná progresivní zjišťování komponent. Při prvním načtení mapy aplikace, se spouštějí sady dotazů ke zjištění součásti související se tuto komponentu. Tlačítko v levém horním se aktualizuje počet součástí v aplikaci při jejich zjištění. 

Po kliknutí na tlačítko "Součásti mapy aktualizace", mapy aktualizují se všechny součásti zjištěny, dokud, které ukazují.

Pokud jsou všechny komponenty rolí v rámci jednoho prostředku Application Insights, pak tento krok zjišťování se nevyžaduje. Počáteční načtení pro takové aplikace budou mít všechny jeho součásti.

![Snímek obrazovky aplikace mapy](media/app-insights-app-map/001.png)

Jedním z klíčových cílů s toto prostředí je možné k vizualizaci komplexní topologie spolu se stovkami komponenty.

Klikněte na libovolné součásti najdete v článku související přehledy a přejít k výkonu a selhání prostředí pro třídění pro danou součást.

![Kontextová nabídka](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>Prověřit chyby

Vyberte **vyšetřování chyb** ke spuštění v podokně selhání.

![Snímek obrazovky prošetřit selhání tlačítko](media/app-insights-app-map/investigate-failures.png)

![Snímek obrazovky selhání prostředí](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>Prověřit výkon

Řešení potíží s vyberte problémy výkonu **vyšetřování výkonu**

![Snímek obrazovky zkoumání výkonu tlačítko](media/app-insights-app-map/investigate-performance.png)

![Snímek obrazovky výkon](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>Přejít na podrobnosti

Vyberte **přejděte na podrobnosti** prozkoumat prostředí začátku do konce transakce, které může nabídnout zobrazení provést na úrovni zásobníku volání.

![Snímek obrazovky tlačítko Přejít na podrobnosti](media/app-insights-app-map/go-to-details.png)

![Snímek obrazovky podrobností transakcí začátku do konce](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Zobrazit v Analytics

Pro dotazování a klikněte na tlačítko Další vaší aplikace data prozkoumat **zobrazit v analytics**.

![Snímek obrazovky zobrazení v tlačítko analytics](media/app-insights-app-map/view-in-analytics.png)

![Snímek obrazovky s analytics prostředí](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>Výstrahy

Chcete-li zobrazit aktivní výstrahy a základní pravidla, které výstrahy se tak být tiggered, vyberte **výstrahy**.

![Snímek obrazovky tlačítka pro výstrahy](media/app-insights-app-map/alerts.png)

![Snímek obrazovky s analytics prostředí](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Váš názor
Zadejte prosím zpětnou vazbu prostřednictvím možnosti portálu zpětnou vazbu.

![Obrázek MapLink 1](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>Další postup

* [Azure Portal](https://portal.azure.com)
