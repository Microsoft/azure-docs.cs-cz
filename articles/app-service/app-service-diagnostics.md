---
title: Přehled diagnostiky Azure App Service | Microsoft Docs
description: Zjistěte, jak můžete potíže s vaší webové aplikace s diagnostikou služby App Service.
keywords: služby App service, služby azure app service, diagnostiky, podpora, webové aplikace, řešení potíží s návody pro Svépomocné řešení
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
ms.openlocfilehash: 50e0e9f5edc18aac42ee80e232f70e09736124bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Přehled diagnostiky Azure App Service 

Pokud používáte webovou aplikaci, kterou chcete připravit pro všechny problémy, které mohou vzniknout, a to z 500 chyb uživatelům informací, které váš webový server je mimo provoz. Diagnostika služby App Service je inteligentní a interaktivní prostředí týkající se řešení webové aplikace s nutná žádná konfigurace. Když spustíte problémy s vaší webové aplikace, služby App Service diagnostics bude odkazovat, co je špatně navést na náležité informace, které chcete rychle a snadno potíže vyřešit problém. 
 
I když toto prostředí je užitečný zejména, pokud máte problémy s s vaší webové aplikace za posledních 24 hodin, bude k dispozici pro vás k analýze za všech okolností diagnostiky grafy. Další nástroje pro odstraňování potíží a odkazy na dokumentaci užitečné a fóra nacházejí v pravém sloupci.

Diagnostika služby App Service funguje pro pouze vaší aplikace v systému Windows, ale také aplikace na [Linux/kontejnery](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro), [App Service Environment](https://docs.microsoft.com/en-us/azure/app-service/environment/intro), a [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Otevřete App Service diagnostics

Pro přístup k Diagnostika služby App Service, přejděte do služby App Service aplikace nebo služby App Service Environment ve [portál Azure](https://portal.azure.com). V levém navigačním panelu, klikněte na **Diagnostikujte a řešení problémů**. 

U Azure Functions, přejděte do aplikace pro funkce a v horním navigačním panelu a potom klikněte na **funkce** a vyberte **Diagnostikujte a řešení problémů** z **monitorování**části. 

![Domovská stránka](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Checkup stavu

Pokud neznáte čem je problém s vaší webové aplikace nebo nevíte, kde spustit vaše řešení potíží, stav je dobrým místem, kde spustit. Checkup stavu bude analýza webových aplikací do získáte rychlý a interaktivní přehled, který odkazuje na co je v pořádku a co je chyba oznamující, kde hledat prozkoumat problém. Jeho rozhraní inteligentního a interaktivní vám poskytne pokyny prostřednictvím proces řešení potíží.  

![Checkup stavu](./media/app-service-diagnostics/HealthCheckup2.png)

Pokud problém s konkrétní problém kategorie během posledních 24 hodin, můžete zobrazit sestavu úplné diagnostiky a Diagnostika služby App Service můžete být vyzváni k zobrazení pomoc více při řešení potíží a další kroky pro více návodné.

![Řešení potíží a další kroky](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Dlaždice zástupců

Pokud znáte přesně jaký druh řešení potíží s informací, které hledáte, dlaždice zástupce přejdete přímo na úplné diagnostiky sestavu kategorii problému, která vás zajímá. Ve srovnání s checkup stavu, dlaždice zástupce jsou další přímo, ale menší na základě přístup k vaší diagnostiky metriky. Jako součást zkratky dlaždice, to je také naleznete **diagnostické nástroje** který pokročilejší nástroje, které vám pomůže prozkoumat problémy související s problémy kódu aplikace, pomalost, připojovací řetězce a další. 

![Dlaždice zástupců](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnostická zpráva

Jestli chcete získat další informace po spuštění [stavu checkup](#health-checkup) nebo jste klikli na jednom z [dlaždici zkratky](#tile-shortcuts), úplné diagnostiky sestava zobrazí relevantní vykreslená metriky z posledních 24 hodin. Pokud vaše aplikace dojde k nějakému výpadku, je reprezentována oranžovou pod časovou osu. Můžete vybrat jednu z oranžové řádky vyberte výpadek viz poznámky o tomto výpadky a navrhované kroky pro řešení potíží. 

![Diagnostická zpráva](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Zkoumání problémů s kódu aplikací

Protože mnoho problémů aplikace souvisí s problémy v kódu aplikace, služby App Service diagnostics integruje s [Application Insights](https://azure.microsoft.com/services/application-insights/) zvýrazněte výjimky a problémy závislosti ke korelaci vybrané výpadek. Application Insights se musí aktivovat samostatně. 

Chcete-li zobrazit výjimky Application Insights a závislosti, vyberte **webové aplikace dolů** nebo **webové aplikace pomalé** dlaždici zástupce. 

![Application Insights](./media/app-service-diagnostics/AppInsights6.png)

