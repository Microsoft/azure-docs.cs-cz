---
title: Protokolování diagnostiky
titleSuffix: Azure Cognitive Services
description: Tato příručka poskytuje podrobné pokyny, jak povolit diagnostické protokolování pro službu rozpoznávání Azure. Tyto protokoly poskytují bohatá a často používaná data o provozu prostředku, který se používá k identifikaci a ladění problémů.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 9660aa3923964392f1789570d26dd825e0fef350
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143194"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Povolení protokolování diagnostiky pro Azure Cognitive Services

Tato příručka poskytuje podrobné pokyny, jak povolit diagnostické protokolování pro službu rozpoznávání Azure. Tyto protokoly poskytují bohatá a často používaná data o provozu prostředku, který se používá k identifikaci a ladění problémů. Než budete pokračovat, musíte mít účet Azure s předplatným alespoň pro jednu službu pro rozpoznávání, například [vyhledávání na webu Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)nebo [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete povolit protokolování diagnostiky, budete muset někam ukládat data protokolu. V tomto kurzu se používá Azure Storage a Log Analytics.

* [Azure Storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) – uchovává protokoly diagnostiky pro audit zásad, statickou analýzu nebo zálohování. Účet úložiště nemusí být ve stejném předplatném, jako je prostředek vysílaný protokoly, pokud uživatel, který nastavení nakonfiguruje, má odpovídající přístup k oběma předplatným Azure RBAC.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) – flexibilní nástroj pro hledání a analýzu protokolů, který umožňuje analýzu nezpracovaných protokolů generovaných prostředkem Azure.

> [!NOTE]
> K dispozici jsou další možnosti konfigurace. Další informace najdete v tématu [shromažďování a využívání dat protokolů z prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Povolit shromažďování protokolů diagnostiky  

Pojďme začít tím, že povolíte protokolování diagnostiky pomocí Azure Portal.

> [!NOTE]
> Pokud chcete tuto funkci povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI, postupujte podle pokynů uvedených v tématu [shromáždění a využití dat protokolu z vašich prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Přejděte na Azure Portal. Pak vyhledejte a vyberte prostředek Cognitive Services. Například vaše předplatné Vyhledávání na webu Bingu.   
2. Dále v navigační nabídce vlevo vyhledejte **monitorování** a vyberte **nastavení diagnostiky**. Tato obrazovka obsahuje všechna dříve vytvořená nastavení diagnostiky pro tento prostředek.
3. Pokud existuje dříve vytvořený prostředek, který byste chtěli použít, můžete ho teď vybrat. V opačném případě vyberte **+ Přidat nastavení diagnostiky**.
4. Zadejte název nastavení. Pak vyberte možnost **archivovat do účtu úložiště** a **Odeslat do Log Analytics**.
5. Po zobrazení výzvy ke konfiguraci vyberte účet úložiště a pracovní prostor OMS, který chcete použít k ukládání diagnostických protokolů. **Poznámka**: Pokud nemáte účet úložiště nebo pracovní prostor OMS, vytvořte ho podle pokynů.
6. Vyberte **audit**, **operace RequestResponse**a **AllMetrics**. Pak nastavte dobu uchování dat diagnostického protokolu. Pokud jsou zásady uchovávání informací nastavené na hodnotu nula, ukládají se události pro tuto kategorii protokolu po neomezenou dobu.
7. Klikněte na **Uložit**.

Může trvat až dvě hodiny, než budou data protokolování k dispozici pro dotazování a analýzu. Nedělejte si starosti, Pokud nevidíte nic hned.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Zobrazení a export diagnostických dat z Azure Storage

Azure Storage je robustní řešení úložiště objektů, které je optimalizované pro ukládání velkých objemů nestrukturovaných dat. V této části se dozvíte, jak zadat dotaz na účet úložiště pro celkové transakce v průběhu 30 dnů a exportovat data do Excelu.

1. Z Azure Portal vyhledejte prostředek Azure Storage, který jste vytvořili v poslední části.
2. V navigační nabídce vlevo Najděte **monitorování** a vyberte **metriky**.
3. Pro konfiguraci dotazu použijte rozevírací seznam k dispozici. V tomto příkladu nastavíme časový rozsah na **posledních 30 dní** a metriku na **transakci**.
4. Po dokončení dotazu se v posledních 30 dnech zobrazí vizualizace transakce. Pokud chcete tato data exportovat, použijte tlačítko **exportovat do aplikace Excel** , které se nachází v horní části stránky.

Přečtěte si další informace o tom, co můžete dělat s diagnostickými daty v [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Zobrazení protokolů ve službě Log Analytics

Podle těchto pokynů Prozkoumejte data Log Analytics pro váš prostředek.

1. Z Azure Portal v navigační nabídce vlevo vyhledejte a vyberte **Log Analytics** .
2. Vyhledejte a vyberte prostředek, který jste vytvořili při povolování diagnostiky.
3. V části **Obecné**Najděte **protokoly**a vyberte je. Na této stránce můžete spouštět dotazy na vaše protokoly.

### <a name="sample-queries"></a>Ukázkové dotazy

Tady je několik základních dotazů Kusto, pomocí kterých můžete prozkoumat data protokolu.

Spustit tento dotaz pro všechny diagnostické protokoly z Azure Cognitive Services v zadaném časovém období:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Spusťte tento dotaz, aby se zobrazilo 10 nejnovějších protokolů:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Spusťte tento dotaz pro seskupení operací podle **prostředku**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Spusťte tento dotaz, abyste zjistili průměrnou dobu potřebnou k provedení operace:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Spusťte tento dotaz pro zobrazení objemu operací v průběhu času rozdělením podle hodnoty OperationName s počty rozdělený pro každé desítkách.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Další kroky

* Pokud chcete pochopit, jak povolit protokolování, a také kategorie metrik a protokolů, které jsou podporované různými službami Azure, přečtěte si [Přehled metrik](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) v tématu Microsoft Azure a [Přehled článků o diagnostických protokolech Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) .
* Přečtěte si tyto články, abyste se seznámili s centry událostí:
  * [Co je služba Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Začínáme s Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Přečtěte si téma [stažení metrik a diagnostické protokoly z Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Přečtěte si [vysvětlení hledání v protokolu v](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)protokolech Azure monitor.
