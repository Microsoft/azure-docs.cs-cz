---
title: Protokolování diagnostiky
titleSuffix: Azure Cognitive Services
description: Tato příručka obsahuje podrobné pokyny, jak povolit protokolování diagnostiky pro kognitivní služby Azure. Tyto protokoly poskytuje bohatě vybaveným a časté data o provozu prostředku, které se používají pro identifikaci problému a ladění.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155728"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Povolit protokolování diagnostiky pro Azure Cognitive Services

Tato příručka obsahuje podrobné pokyny, jak povolit protokolování diagnostiky pro kognitivní služby Azure. Tyto protokoly poskytuje bohatě vybaveným a časté data o provozu prostředku, které se používají pro identifikaci problému a ladění. Než budete pokračovat, musíte mít účet Azure s předplatným nejméně jedné služby Cognitive Services, jako například [vyhledávání na webu Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [hlasové služby](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), nebo [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Požadavky

Pokud chcete povolit protokolování diagnostiky, budete potřebovat někde ukládat data protokolu. Tento kurz používá Azure Storage a Log Analytics.

* [Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) – zachovává diagnostické protokoly pro zásady auditu, statické analýzy nebo pro zálohování. Účet úložiště nemusí být ve stejném předplatném jako prostředek, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -flexibilní protokol vyhledávání a analýzy nástroj, který umožňuje analýzy nezpracovaných protokolů generovaných prostředku Azure.

> [!NOTE]
> Další možnosti konfigurace jsou k dispozici. Další informace najdete v tématu [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-diagnostic-log-collection"></a>Povolit shromažďování diagnostických protokolů  

Začněme tím, že povolíte diagnostiku protokolování pomocí webu Azure portal.

> [!NOTE]
> Pokud chcete povolit tuto funkci pomocí Powershellu nebo rozhraní příkazového řádku Azure CLI, postupujte podle pokynů v [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

1. Přejděte na web Azure Portal. Poté vyhledejte a vyberte prostředek služeb Cognitive Services. Například předplatné pro vyhledávání na webu Bingu.   
2. Potom z nabídky levém navigačním panelu vyhledejte **monitorování** a vyberte **nastavení diagnostiky**. Tato obrazovka obsahuje všechny dříve vytvořené nastavení diagnostiky pro tento prostředek.
3. Pokud dříve vytvořený prostředek, který chcete použít, můžete ho teď vybrat. V opačném případě vyberte **+ přidat nastavení diagnostiky**.
4. Zadejte název pro nastavení. Potom vyberte **archivovat do účtu úložiště** a **odesílání do log Analytics**.
5. Po zobrazení výzvy ke konfiguraci, vyberte účet úložiště a pracovní prostor OMS, který chcete použít k uložení diagnostických protokolů. **Poznámka:** Pokud nemáte pracovní prostor OMS nebo účtu úložiště, postupujte podle výzev a vytvořte si ho.
6. Vyberte **auditu**, **Operace RequestResponse**, a **AllMetrics**. Nastavte dobu uchování dat diagnostických protokolů. Pokud zásady uchovávání informací je nastavena na hodnotu nula, události pro dané kategorie protokolu se ukládají po neomezenou dobu.
7. Klikněte na **Uložit**.

Může trvat až dvě hodiny, než je k dispozici pro dotazy a analýzu dat protokolování. Takže Nedělejte si starosti, pokud se nic nezobrazí okamžitě.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Zobrazit a exportovat diagnostických dat ze služby Azure Storage

Azure Storage je řešení úložiště robustní objektu, která je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat. V této části se dozvíte dotazování účtu úložiště pro celkový počet transakcí za období 30 dnů a export dat do Excelu.

1. Z portálu Azure portal vyhledejte prostředek služby Azure Storage, kterou jste vytvořili v předchozí části.
2. Z nabídky levém navigačním panelu vyhledejte **monitorování** a vyberte **metriky**.
3. K dispozici rozevírací seznamy slouží ke konfiguraci vašeho dotazu. V tomto příkladu nastavíme časový rozsah **posledních 30 dní** a metriku **transakce**.
4. Po dokončení dotazu se zobrazí vizualizaci transakce za posledních 30 dní. Chcete-li tato data exportovat, použijte **exportovat do Excelu** tlačítko umístěné v horní části stránky.

Další informace o tom, co můžete učinit spolu s diagnostickými daty v [služby Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Zobrazení protokolů ve službě Log Analytics

Postupujte podle těchto pokynů ke zkoumání dat log analytics pro váš prostředek.

1. Na webu Azure Portal, vyhledejte a vyberte **Log Analytics** z levé navigační nabídky.
2. Vyhledejte a vyberte prostředek, který jste vytvořili při povolování diagnostiky.
3. V části **Obecné**, vyhledejte a vyberte **protokoly**. Na této stránce můžete spouštět dotazy proti vaše protokoly.

### <a name="sample-queries"></a>Ukázkové dotazy

Tady je několik základních dotazů Kusto, která vám pomůže prozkoumat svá data protokolu.

Spuštění tohoto dotazu pro všechny diagnostické protokoly z Azure Cognitive Services pro zadané časové období:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Spuštění tohoto dotazu zobrazíte 10 nejnovější protokoly:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Spuštění tohoto dotazu do operace skupiny podle **prostředků**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Spusťte tento dotaz pro vyhledání průměrný čas potřebný k provedení operace:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Spuštění tohoto dotazu, chcete-li zobrazit objem operací v čase rozdělit podle OperationName s počty rozdělený na intervaly pro každý 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Další postup

* Pochopit postup při povolování protokolování a metrik a protokolů kategorií, které podporují různé služby Azure, přečtěte si i [přehled metrik](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) v Microsoft Azure a [přehled o diagnostické protokoly Azure ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) článků.
* Přečtěte si tyto články Další informace o službě event hubs:
  * [Co je Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Začínáme s Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Čtení [stáhnout metrik a diagnostických protokolů ze služby Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Čtení [Principy prohledávání protokolů v protokoly Azure monitoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
