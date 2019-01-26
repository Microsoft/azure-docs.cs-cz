---
title: Monitorování aplikací - Azure App Service | Dokumentace Microsoftu
description: Další informace o monitorování aplikací ve službě Azure App Service pomocí webu Azure portal.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a5d4d13d8e60cd7f273363a9bc385098e15cbb71
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913152"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorování aplikací ve službě Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) poskytuje integrované funkce monitorování pro webové aplikace, back-endů mobilních a API apps v [webu Azure portal](https://portal.azure.com).

Na webu Azure Portal, můžete zkontrolovat *kvóty* a *metriky* pro aplikace, projděte si plán služby App Service a automaticky nastavit *výstrahy* a *škálování* založené na metriky.

## <a name="understand-quotas"></a>Vysvětlení kvóty

Aplikace, které jsou hostované ve službě App Service se vztahují na prostředky, které můžete použít určitá omezení. Omezení je definována v plánu služby App Service, který je spojen s aplikací.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Pokud je aplikace hostovaná v *Free* nebo *Shared* plán, omezení pro prostředky, které může aplikace používat, jsou definovány pomocí kvót.

Pokud je aplikace hostovaná v *základní*, *standardní*, nebo *Premium* plán, omezení pro prostředky, které můžete použít institut NIST *velikost* () Malá, střední, velká) a *počet instancí* (1, 2, 3 a tak dále) plánu služby App Service.

Kvóty pro Free nebo Shared aplikace jsou:

| Kvóta | Popis |
| --- | --- |
| **Využití procesoru (zkráceně)** | Procento využití procesoru pro tuto aplikaci povolené v intervalu 5 minut. Tato kvóta resetuje každých pět minut. |
| **Využití procesoru (den)** | Celkové procento využití procesoru pro tuto aplikaci za jeden den povolené. Tato kvóta resetuje o půlnoci UTC každých 24 hodin. |
| **Paměť** | Celková velikost paměti pro tuto aplikaci povolené. |
| **Bandwidth** | Celkový objem odchozí šířky pásma pro tuto aplikaci za jeden den povolené. Tato kvóta resetuje o půlnoci UTC každých 24 hodin. |
| **Filesystem** | Celková velikost úložiště povolené. |

Pouze pro aplikace, které jsou hostovány v kvóty *základní*, *standardní*, a *Premium* plány je systém souborů.

Další informace o konkrétní kvóty, limity a funkce, které jsou k dispozici pro různé skladové jednotky App Service najdete v tématu [limity předplatného Azure](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Vynucení kvót

Pokud překročí aplikace *procesoru (zkráceně)*, *procesoru (den)*, nebo *šířky pásma* kvóty, aplikace se zastaví, dokud se kvóta resetuje. Během této doby všechny žádosti přicházející způsobit chybu HTTP 403.

![Chyba 403 zprávy][http403]

Pokud dojde k překročení kvóty paměti aplikace, je restartovat aplikaci.

Pokud dojde k překročení kvóty systému souborů, jakékoli zápisu operace se nezdaří. Zapsat všechny zápisy do protokolů zahrnout selhání operací.

Můžete zvýšit nebo odebrat kvóty z vaší aplikace díky upgradu plánu služby App Service.

## <a name="understand-metrics"></a>Vysvětlení metriky

Metriky poskytují informace o aplikaci nebo služby App Service chování plánu.

Pro aplikace jsou k dispozici metriky:

| Metrika | Popis |
| --- | --- |
| **Průměrná doba odezvy** | Průměrná doba pro aplikaci pro požadavků, v milisekundách. |
| **Průměrná pracovní sada paměti** | Průměrné množství paměti používané aplikace, v megabajtech (MiB). |
| **Připojení** | Počet vázaných soketů v izolovaném prostoru (w3wp.exe a jeho podřízených procesů).  Vázané soketu je vytvořen zavoláním bind()/connect() rozhraní API a zůstane, dokud CloseHandle()/closesocket() je uzavřen uvedené soketu. |
| **Čas procesoru** | Procento využití procesoru používané aplikace v řádu sekund. Další informace o tuto metriku, naleznete v tématu [vs procesoru procentuální hodnota času procesoru](#cpu-time-vs-cpu-percentage). |
| **Aktuální sestavení** | Aktuální počet sestavení zavedených ve všech objektů třídy AppDomains v této aplikaci. |
| **Data v** | Množství příchozích šířky pásma používané aplikace v MiB. |
| **Výstupní data** | Množství využité aplikací v MiB odchozí šířky pásma. |
| **0. generace uvolňování pamětí** | Počet pokusů, které jsou objekty 0. generace uvolňování paměti shromážděných od spuštění procesu aplikace. Vyšší generace GC obsahují všechny nižší generace GC.|
| **1. generace uvolňování pamětí** | Počet pokusů, které jsou objekty 1. generace uvolňování paměti shromážděných od spuštění procesu aplikace. Vyšší generace GC obsahují všechny nižší generace GC.|
| **Uvolnění paměti generace 2** | Počet pokusů, které jsou objekty 2. generace uvolňování paměti shromážděných od spuštění procesu aplikace.|
| **Počet popisovačů** | Celkový počet popisovačů procesem aplikaci aktuálně otevřené.|
| **Http 2xx** | Počet požadavků, což vede k ≥ stavový kód HTTP 200, ale < 300. |
| **Http 3xx** | Počet požadavků, výsledkem je stavový kód HTTP ≥ 300, ale < 400. |
| **Http 401** | Počet požadavků, výsledkem je stavový kód HTTP 401. |
| **Http 403** | Počet požadavků, výsledkem je stavový kód HTTP 403. |
| **Http 404** | Počet požadavků, výsledkem je stavový kód HTTP 404. |
| **Http 406** | Počet požadavků, výsledkem je stavový kód HTTP 406. |
| **Http 4xx** | Počet požadavků, což vede k ≥ stavový kód HTTP 400, ale < 500. |
| **Chyby serveru http** | Počet požadavků, výsledkem je stavový kód HTTP ≥ 500, ale < 600. |
| **Vstupně-výstupních operací ostatní bajty za sekundu** | Míra vystavování bajtů na vstupně-výstupních operací, které neobsahují data, jako jsou operace řízení procesu aplikace.|
| **Vstupně-výstupních operací ostatní operace za sekundu** | Rychlost procesu aplikace žádání vstupně-výstupních operací, které jsou čtení a zápisu operace.|
| **Přečtené bajty vstupně-výstupních operací za sekundu** | Rychlost čtení bajtů z vstupně-výstupních operací procesu aplikace.|
| **Operace čtení vstupně-výstupních operací za sekundu** | Rychlost žádání procesu aplikace čtení vstupně-výstupních operací.|
| **Vstupně-výstupních operací zapsané bajty za sekundu** | Rychlost zápisu bajtů na vstupně-výstupních operací procesu aplikace.|
| **Vstupně-výstupní operace zápisu za sekundu** | Rychlost procesu aplikace žádání vstupně-výstupních operací zápisu.|
| **Pracovní sada paměti** | Aktuální velikost paměti, používat aplikace v MiB. |
| **Nesdílené bajty** | Nesdílené bajty je stávající velikost v bajtech, paměti, která byla přidělena procesu aplikace, které nemohou být sdíleny s jinými procesy.|
| **Požadavky** | Celkový počet požadavků bez ohledu na jejich výsledné stavového kódu protokolu HTTP. |
| **Požadavky ve frontě aplikace** | Počet požadavků ve frontě požadavků aplikace.|
| **Počet vláken** | Počet vláken v procesu aplikace aktuálně aktivní.|
| **Celkový počet domén aplikace** | Aktuální počet objektů třídy AppDomains načíst v této aplikaci.|
| **Celkový počet domén aplikace byla uvolněna** | Celkový počet objektů třídy AppDomains uvolněných od spuštění aplikace.|


Pro plán služby App Service jsou k dispozici metriky:

> [!NOTE]
> Metriky plánu služby App Service jsou k dispozici pouze pro plány v *základní*, *standardní*, a *Premium* úrovně.
> 

| Metrika | Popis |
| --- | --- |
| **Procentuální využití procesoru** | Průměr CPU použít napříč všemi instancemi plánu. |
| **Procento paměti** | Průměrná paměť použitá napříč všemi instancemi plánu. |
| **Data v** | Průměrná příchozí šířku pásma používanou napříč všemi instancemi plánu. |
| **Výstupní data** | Průměr odchozí šířka pásma použít napříč všemi instancemi plánu. |
| **Délka fronty disku** | Průměrný počet čtení a zápis požadavků, které byly zařazeny do fronty úložiště. Délka fronty disku je údaj o aplikaci, která může být zpomalení z důvodu nadměrného diskové vstupně-výstupních operací. |
| **Délka fronty http** | Průměrný počet požadavků HTTP, do kterých se nacházejí ve frontě, než jsou splněny. Vysokou nebo rostoucí délku fronty HTTP je příznakem plán při velkém zatížení. |

### <a name="cpu-time-vs-cpu-percentage"></a>Vs procesoru procentuální hodnota času procesoru
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existují dvě metriky, které odrážejí využití procesoru:

**Čas procesoru**: Užitečné pro aplikace hostované v bezplatné nebo sdílené plány, protože jeden z jejich kvóty je definováno během několika minut využití procesoru, které aplikace používá.

**Procentuální využití procesoru**: Užitečné pro aplikace hostované v plánech Basic, Standard a Premium, protože je možné škálovat navýšení kapacity. Procentuální využití procesoru je dobrá indikace toho celkové využití napříč všemi instancemi.

## <a name="metrics-granularity-and-retention-policy"></a>Metriky členitosti a zásadu uchovávání
Metriky pro aplikace a plán služby app service jsou zaznamenána a agregovat služba s následujícím rozlišeních a zásady uchovávání informací:

* **Minuta** členitosti metriky se uchovávají po dobu 30 hodin.
* **Hodina** členitosti metriky se uchovávají po dobu 30 dnů.
* **Den** členitosti metriky se uchovávají po dobu 30 dnů.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorování metrik na webu Azure Portal a kvóty
Chcete-li zkontrolovat stav různých kvóty a metriky, které ovlivňují aplikace, přejděte [webu Azure portal](https://portal.azure.com).

![Kvóty grafu na webu Azure Portal][quotas]

Chcete-li najít kvóty, vyberte **nastavení** > **kvóty**. V grafu můžete zkontrolovat: 
1. Název kvóty.
1. Interval jeho resetování.
1. Aktuální limit.
1. Jeho aktuální hodnota.

![Graf metrik na webu Azure Portal][metrics] můžete přístup k metrikám přímo z **prostředků** stránky. Přizpůsobení grafu: 
1. Vyberte graf.
1. Vyberte **upravit graf**.
1. Upravit **časový rozsah**.
1. Upravit **typ grafu**.
1. Upravte metriky, které chcete zobrazit.  

Další informace o metrikách najdete v tématu [monitorujete metriky služeb](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Výstrahy a automatické škálování
Metriky pro aplikaci nebo plán služby App Service můžete využívat na výstrahy. Další informace naleznete v tématu [Doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-alerts-portal.md).

Hostované v úrovně Basic, Standard nebo Premium služby App Service podporu automatického škálování plánů služby App Service aplikace. S automatickým Škálováním můžete nakonfigurovat pravidla, která umožňuje monitorovat metriky plánu služby App Service. Pravidla můžete zvýšit nebo snížit počet instancí, které můžou poskytovat další prostředky podle potřeby. Pravidla i vám může pomoct ušetřit peníze, když aplikace je nadměrně zřízený.

Další informace o automatické škálování najdete v tématu [škálování](../monitoring-and-diagnostics/insights-how-to-scale.md) a [osvědčené postupy pro automatické škálování Azure monitoru](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png