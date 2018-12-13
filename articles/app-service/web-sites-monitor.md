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
ms.date: 11/28/2017
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 103b5c1d2bc70f187b6e65a9fa9d80a35ad8e0c1
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321575"
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Postup: Sledování aplikací ve službě Azure App Service
[App Service](https://go.microsoft.com/fwlink/?LinkId=529714) poskytuje integrované funkce monitorování v [webu Azure portal](https://portal.azure.com).
Na webu Azure portal umožňuje zkontrolovat **kvóty** a **metriky** pro aplikace, stejně jako plán služby App Service, nastavení **výstrahy** a dokonce i **škálování**  automaticky v závislosti na tyto metriky.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Principy kvóty a metriky
### <a name="quotas"></a>Kvóty
Aplikace hostované ve službě App Service podléhají určité *omezení* na prostředky, můžete použít. Omezení jsou definovány **plán služby App Service** přidružené k aplikaci.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Pokud je aplikace hostovaná v **Free** nebo **Shared** plánování a omezení pro aplikace můžete používat prostředky, které jsou definovány pomocí **kvóty**.

Pokud je aplikace hostovaná v **základní**, **standardní** nebo **Premium** plánování a omezení pro prostředky, můžete použít určil institut NIST **velikost**(Malá, střední, velká) a **počet instancí** (1, 2, 3,...) z **plán služby App Service**.

**Kvóty** pro **Free** nebo **Shared** aplikace jsou:

* **CPU(Short)**
  * Procento využití procesoru, které jsou povolené pro tuto aplikaci v intervalu 5 minut. Tato kvóta resetuje každých pět minut.
* **CPU(Day)**
  * Celkové procento využití procesoru povolený pro tuto aplikaci za jeden den. Tato kvóta resetuje o půlnoci UTC každých 24 hodin.
* **Paměť**
  * Celkové množství paměti pro tuto aplikaci povolená.
* **Šířka pásma**
  * Celkové množství odchozích šířky pásma pro tuto aplikaci za jeden den povolené.
    Tato kvóta resetuje o půlnoci UTC každých 24 hodin.
* **systém souborů**
  * Celková velikost úložiště, které jsou povoleny.

Pouze pro aplikace hostované na kvótu **základní**, **standardní**, a **Premium** plány je **systému souborů**.

Další informace o konkrétní kvóty, limity a funkce dostupné na různých skladových položkách App Service najdete tady: [Omezení služeb v předplatném Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Vynucení kvót
Pokud překročí aplikace **procesoru (zkráceně)**, **procesoru (den)**, nebo **šířky pásma** kvóta pak aplikace je zastavena, dokud se kvóta resetuje. Během této doby všechny žádosti přicházející vést **HTTP 403**.
![][http403]

Pokud aplikace **paměti** překročena kvóta, pak se aplikace restartuje.

Pokud **systému souborů** překročila kvótu a jakékoli zápisu operace se nezdaří, který zahrnuje všechny zápisy do protokolů.

Kvóty můžete zvýšit nebo z vaší aplikace odebrán, upgradujte plán služby App Service.

### <a name="metrics"></a>Metriky
**Metriky** poskytují informace o aplikaci nebo chování plán služby App Service.

Pro **aplikace**, jsou dostupné metriky:

* **Průměrná doba odezvy**
  * Průměrná doba pro aplikaci pro požadavků v ms.
* **Průměrná pracovní sada paměti**
  * Průměrná velikost paměti v MIB, které aplikace používá.
* **Čas procesoru**
  * Procento využití procesoru v řádu sekund používané aplikace. Další informace o tuto metriku naleznete v tématu: [Vs procesoru procentuální hodnota času procesoru](#cpu-time-vs-cpu-percentage)
* **Data v**
  * Množství příchozích šířky pásma využívané aplikace v MIB.
* **Výstupní data**
  * Množství využité aplikací v MIB odchozí šířky pásma.
* **Http 2xx**
  * Počet požadavků, výsledkem je stavový kód protokolu HTTP > = 200, ale < 300.
* **Http 3xx**
  * Počet požadavků, výsledkem je stavový kód protokolu HTTP > = 300, ale < 400.
* **Http 401**
  * Počet požadavků, které jsou výsledkem je stavový kód HTTP 401.
* **Http 403**
  * Počet požadavků, které jsou výsledkem je stavový kód HTTP 403.
* **Http 404**
  * Počet požadavků, které jsou výsledkem je stavový kód HTTP 404.
* **HTTP 406**
  * Počet požadavků, které jsou výsledkem je stavový kód HTTP 406.
* **Http 4xx**
  * Počet požadavků, výsledkem je stavový kód protokolu HTTP > = 400, ale < 500.
* **Chyby serveru http**
  * Počet požadavků, výsledkem je stavový kód protokolu HTTP > = 500, ale < 600.
* **Pracovní sada paměti**
  * Aktuální velikost paměti, používat aplikace v MIB.
* **Požadavky**
  * Celkový počet požadavků bez ohledu na jejich výsledné stavového kódu protokolu HTTP.

Pro **plán služby App Service**, jsou dostupné metriky:

> [!NOTE]
> Metriky plánu služby App Service jsou dostupné jenom pro plány **základní**, **standardní**, a **Premium** úrovně.
> 
> 

* **Procentuální využití procesoru**
  * Průměr CPU použít napříč všemi instancemi plánu.
* **Procento paměti**
  * Průměrná paměť použitá napříč všemi instancemi plánu.
* **Data v**
  * Průměrná příchozí šířku pásma používanou napříč všemi instancemi plánu.
* **Výstupní data**
  * Průměr odchozí šířka pásma použít napříč všemi instancemi plánu.
* **Délka fronty disku**
  * Průměrný počet čtení a zápis požadavků, které byly zařazeny do fronty úložiště. Délka fronty disku je údaj o aplikaci, která může být zpomalení z důvodu nadměrného diskové vstupně-výstupních operací.
* **Délka fronty http**
  * Průměrný počet požadavků HTTP, do kterých se nacházejí ve frontě, než jsou splněny. Vysokou nebo rostoucí délku fronty HTTP je příznakem plán při velkém zatížení.

### <a name="cpu-time-vs-cpu-percentage"></a>Vs procesoru procentuální hodnota času procesoru
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existují dvě metriky, které odrážejí využití procesoru. **Čas procesoru** a **procento využití procesoru**

**Čas procesoru** je užitečné pro aplikace hostované v **Free** nebo **Shared** plány, protože jeden z jejich kvóty je definovaný během několika minut využití procesoru, které aplikace používá.

**Procentuální využití procesoru** je užitečné pro aplikace hostované v **základní**, **standardní**, a **premium** plány vzhledem k tomu, že lze škálovat. Procentuální využití procesoru je dobrá indikace toho celkové využití napříč všemi instancemi.

## <a name="metrics-granularity-and-retention-policy"></a>Metriky Členitosti a zásady uchovávání informací
Metriky pro aplikace a plán služby app service jsou protokolovány a agregované podle této služby s využitím následujících rozlišeních a zásady uchovávání informací:

* **Minuta** členitosti metriky se uchovávají po dobu **30 hodin**
* **Hodina** členitosti metriky se uchovávají po dobu **30 dnů**
* **Den** členitosti metriky se uchovávají po dobu **30 dnů**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorování kvóty a metriky na webu Azure Portal.
Můžete zkontrolovat stav různých **kvóty** a **metriky** by to ovlivnilo aplikace [webu Azure portal](https://portal.azure.com).

![][quotas]
**Kvóty** najdete v části Nastavení >**kvóty**. Uživatelského rozhraní vám umožní zkontrolovat: (1) název kvóty, (2) jeho interval resetování, (3) jeho aktuální limit a (4) aktuální hodnotu.

![][metrics]
**Metriky** můžete přistupovat přímo na stránce prostředků. Můžete také upravit graf podle: (1) **klikněte na tlačítko** na ně a (2) vyberte **upravit graf**.
Odsud můžete změnit (3) **časový rozsah**, (4) **typ grafu**a (5) **metriky** k zobrazení.  

Další informace o metrikách tady: [Monitorování metrik služeb](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Výstrahy a automatické škálování
Metriky pro plán služby aplikace nebo služby App Service můžete využívat na výstrahy. Další informace o tom najdete v tématu [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-alerts-portal.md).

Aplikace služby App Service, které jsou hostované v basic, standard nebo premium služby App Service plány podpory **automatického škálování**. Automatické škálování umožňuje konfigurovat pravidla, která umožňuje monitorovat metriky plánu služby App Service. Pravidla můžete zvýšit nebo snížit počet instancí nabízí další zdroje informací, podle potřeby. Pravidla i vám může pomoct ušetřit peníze, když aplikace je nadměrně zřízený. Další informace o automatickém škálování tady: [Jak škálovat](../monitoring-and-diagnostics/insights-how-to-scale.md) a zde [osvědčené postupy pro automatické škálování Azure monitoru](../azure-monitor/platform/autoscale-best-practices.md)

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
