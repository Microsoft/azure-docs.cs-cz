---
title: Protokolování diagnostiky
titleSuffix: Azure Cognitive Services
description: Tato příručka obsahuje podrobné pokyny, které umožňují protokolování diagnostiky pro službu Azure Cognitive Service. Tyto protokoly poskytují bohaté, časté údaje o operaci prostředku, které se používají pro identifikaci problému a ladění.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827903"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Povolení protokolování diagnostiky pro služby Azure Cognitive Services

Tato příručka obsahuje podrobné pokyny, které umožňují protokolování diagnostiky pro službu Azure Cognitive Service. Tyto protokoly poskytují bohaté, časté údaje o operaci prostředku, které se používají pro identifikaci problému a ladění. Než budete pokračovat, musíte mít účet Azure s předplatným alespoň jedné služby Cognitive Service, jako je [například Webové vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Hlasové služby](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)nebo [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Požadavky

Chcete-li povolit protokolování diagnostiky, budete muset někde uložit data protokolu. Tento kurz používá Azure Storage a Log Analytics.

* [Úložiště Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) – uchovádiagnostické protokoly pro audit zásad, statickou analýzu nebo zálohování. Účet úložiště nemusí být ve stejném předplatném jako prostředek emitující protokoly, pokud uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma odběrům.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) – flexibilní nástroj pro vyhledávání a analýzu protokolů, který umožňuje analýzu nezpracovaných protokolů generovaných prostředkem Azure.

> [!NOTE]
> K dispozici jsou další možnosti konfigurace. Další informace najdete v [tématu Shromažďování a využívání dat protokolu z prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Povolit shromažďování diagnostických protokolů  

Začněme povolením protokolování diagnostiky pomocí portálu Azure.

> [!NOTE]
> Chcete-li tuto funkci povolit pomocí prostředí PowerShell nebo v příkazovém příkazovém příkazu Konahovat, použijte pokyny uvedené v části [Shromažďování a využívání dat protokolu z prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Přejděte na Azure Portal. Potom vyhledejte a vyberte prostředek služeb Cognitive Services. Například vaše předplatné vyhledávání Bing na webu.   
2. Dále v levé navigační nabídce vyhledejte **položku Monitoring** a vyberte **možnost Nastavení diagnostiky**. Tato obrazovka obsahuje všechna dříve vytvořená diagnostická nastavení pro tento prostředek.
3. Pokud existuje dříve vytvořený prostředek, který chcete použít, můžete jej vybrat nyní. V opačném případě vyberte **+ Přidat diagnostické nastavení**.
4. Zadejte název nastavení. Pak vyberte **Archivovat do účtu úložiště** a **Odeslat do protokolu Analytics**.
5. Po zobrazení výzvy ke konfiguraci vyberte účet úložiště a pracovní prostor OMS, který chcete použít k uložení diagnostických protokolů. **Poznámka:** Pokud nemáte účet úložiště nebo pracovní prostor OMS, postupujte podle pokynů k jeho vytvoření.
6. Vyberte **audit**, **Odpověď na požadavek**a **AllMetrics**. Potom nastavte dobu uchování dat diagnostického protokolu. Pokud je zásada uchovávání informací nastavena na nulu, události pro tuto kategorii protokolu jsou uloženy po neomezenou dobu.
7. Klikněte na **Uložit**.

Může trvat až dvě hodiny, než protokolování dat je k dispozici pro dotazování a analýzu. Takže se nebojte, pokud nevidíte nic hned.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Zobrazení a export diagnostických dat z Azure Storage

Azure Storage je robustní řešení úložiště objektů, které je optimalizované pro ukládání velkého množství nestrukturovaných dat. V této části se naučíte dotazovat se účtu úložiště na celkové transakce za 30 denní časový rámec a exportovat data do aplikace Excel.

1. Na webu Azure Portal vyhledejte prostředek Azure Storage, který jste vytvořili v poslední části.
2. V levé navigační nabídce vyhledejte **položku Monitoring** a vyberte **metriky**.
3. Ke konfiguraci dotazu použijte dostupné rozevírací pravidla. V tomto příkladu nastavíme časový rozsah na **Posledních 30 dní** a metriku na **Transaction**.
4. Po dokončení dotazu se zobrazí vizualizace transakce za posledních 30 dní. Chcete-li tato data exportovat, použijte tlačítko **Exportovat do aplikace Excel** umístěné v horní části stránky.

Přečtěte si další informace o tom, co můžete dělat s diagnostickými daty ve [Službě Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Zobrazení protokolů ve službě Log Analytics

Podle těchto pokynů prozkoumejte data analýzy protokolů pro váš prostředek.

1. Na portálu Azure vyhledejte a vyberte **Analýzu protokolů** z levé navigační nabídky.
2. Vyhledejte a vyberte prostředek, který jste vytvořili při povolení diagnostiky.
3. V části **Obecné**vyhledejte a vyberte **protokoly**. Na této stránce můžete spouštět dotazy proti protokolům.

### <a name="sample-queries"></a>Ukázkové dotazy

Zde je několik základních dotazů Kusto, které můžete použít k prozkoumání dat protokolu.

Spusťte tento dotaz pro všechny diagnostické protokoly ze služby Azure Cognitive Services za zadané časové období:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Spuštěním tohoto dotazu zobrazíte 10 nejnovějších protokolů:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Spusťte tento dotaz do seskupení operací podle **prostředku**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Spusťte tento dotaz a vyhledejte průměrnou dobu, kterou trvá provedení operace:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Spuštěním tohoto dotazu zobrazíte objem operací v čase rozdělený podle OperationName s počty přihrádami pro každých 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Další kroky

* Pokud chcete pochopit, jak povolit protokolování, a také metriky a kategorie protokolů, které jsou podporované různými službami Azure, přečtěte si [přehled metrik](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) v Microsoft Azure a Přehled článků [diagnostických protokolů Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)
* V těchto článcích se dozvíte o centrech událostí:
  * [Co je služba Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Začínáme s Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Přečtěte si [metriky stahování a diagnostické protokoly z Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Přečtěte si [pochopit hledání protokolu v protokolech Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
