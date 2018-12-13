---
title: Přehled diagnostiky – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak je řešit problémy s vaší webovou aplikací pomocí diagnostiky služby App Service.
keywords: služby App service, služby azure app service, Diagnostika, podpora, webové aplikace s řešením problémů, samoobslužné podpory
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: a8b256f43d8e4103404ab4276431ceb06d9ed36a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255138"
---
# <a name="azure-app-service-diagnostics-overview"></a>Přehled diagnostiky služby Azure App Service 

Pokud používáte webovou aplikaci, budete chtít připravit pro všechny problémy, které mohou vzniknout z 500 chyb pro uživatele o tom, že váš web je vypnutý. Diagnostika služby App Service je inteligentní a interaktivní prostředí při odstraňování webové aplikace bez nezbytné konfigurace. Když narazíte na problémy s vaší webovou aplikací, bude bod diagnostiky App Service, co je špatně a provede vás na ty správné informace k snadno a rychle odstraňovat potíže a řešit potíže. 
 
I když toto prostředí je nejužitečnější, pokud máte problémy s vaší webovou aplikací během posledních 24 hodin, bude diagnostických grafů si můžete analyzovat za všech okolností. Další nástroje pro odstraňování potíží a odkazy na užitečné dokumentace a fóra, jsou umístěny v pravém sloupci.

Diagnostika služby App Service funguje nejen vaší aplikace na Windows, ale také aplikace na [Linux/kontejnery](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [služby App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro), a [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Otevřít diagnostiky App Service

Pro přístup k diagnostice App Service, přejděte do App Service Environment ve vaší aplikaci služby App Service [webu Azure portal](https://portal.azure.com). V levém navigačním panelu klikněte na **diagnostikovat a řešit problémy**. 

Pro službu Azure Functions, přejděte do aplikace function app a v horním navigačním panelu a potom klikněte na **funkce platformy** a vyberte **diagnostikovat a řešit problémy** z **monitorování**oddílu. 

![Domovská stránka](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Kontrola stavu

Pokud nevíte, co je špatně s vaší webovou aplikací nebo nevíte, kde proces řešení potíží vaše potíže, kontrola stavu je dobrým začátkem. Kontrola stavu analyzuje vaše webové aplikace, abyste získali rychlém, interaktivním přehledu, která poukazuje na co je v pořádku a v čem je problém, o tom, kde hledat k prošetření problému. Rozhraní intelligent a interaktivní vám poskytne pokyny k řešení problémů s procesem.  

![Kontrola stavu](./media/app-service-diagnostics/HealthCheckup2.png)

Pokud s konkrétním problémem kategorií během posledních 24 hodin zjistí problém, můžete zobrazit úplné diagnostickou zprávu a diagnostice App Service můžete být vyzváni k zobrazení více řešení problémů s Rady a další kroky pro prostředí více s asistencí.

![Řešení potíží a další kroky](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Klávesové zkratky dlaždice

Pokud víte přesně jaký druh řešení potíží s informací, které hledáte, klávesové zkratky dlaždici přejdete přímo na úplné diagnostickou sestavu kategorii problému, který vás zajímá. Ve srovnání s Kontrola stavu, dlaždice zkratky jsou určeny další přímo, ale menší s asistencí pro přístup k diagnostických metrik. Jako součást klávesové zkratky dlaždice, toto je také kde najdete **diagnostické nástroje** což jsou další pokročilé nástroje, které vám pomůže prozkoumat problémy související s problémy s kódem aplikace, pomalého chodu, připojovací řetězce a další. 

![Klávesové zkratky dlaždice](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnostickou sestavu

Určuje, zda chcete získat další informace po spuštění [Kontrola stavu](#health-checkup) nebo jste klikli na jednom z [dlaždici zkratky](#tile-shortcuts), úplné diagnostickou sestavu zobrazit, relevantní grafické metriky za posledních 24 hodin. Pokud vaše aplikace dojde žádné výpadky, je reprezentována oranžovou pod na časové ose. Můžete vybrat jeden z pruhů oranžovou a vyberte výpadek viz poznámky o této výpadky a navrhované kroky pro řešení potíží. 

![Diagnostickou sestavu](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Zkoumání potíží s kódem aplikace

Protože mnoho problémů s aplikací se vztahují na problémy v kódu aplikace, diagnostiky App Service integruje [Application Insights](https://azure.microsoft.com/services/application-insights/) výjimky a problémy s závislostí ke korelaci s vybranou výpadky. Application Insights nutné ho zakazovat samostatně. 

Chcete-li zobrazit výjimek Application Insights a závislosti, vyberte **vypnutí webové aplikace** nebo **webové aplikace pomalé** dlaždici klávesové zkratky. 

![Application insights](./media/app-service-diagnostics/AppInsights6.png)

