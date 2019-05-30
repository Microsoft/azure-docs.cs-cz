---
title: Zvolte analýzy v reálném čase a streamování zpracování technologie v Azure
description: Další informace o tom, jak vybrat přímo v reálném čase analýzy a zpracování technologie streamování pro sestavení aplikace v Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242693"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Zvolte analýzy v reálném čase a streamování zpracování technologie v Azure

Některé služby nejsou k dispozici pro zpracování datových proudů v Azure a analýzy v reálném čase. Tento článek obsahuje informace potřebné k rozhodování, technologii, která je nejvhodnější pro vaši aplikaci.

## <a name="when-to-use-azure-stream-analytics"></a>Kdy použít Azure Stream Analytics

Azure Stream Analytics je doporučenou službu pro stream analytics v Azure. Je určená pro širokou škálu scénářů, které patří, ale nejsou omezené na:

* Řídicí panely pro vizualizaci dat
* V reálném čase [výstrahy](stream-analytics-set-up-alerts.md) z dočasné a prostorových vzorů nebo anomálií
* Extrakce, transformace, načítání (ETL)
* [Model Event Sourcing](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Přidání Azure Stream Analytics úlohy do vaší aplikace je nejrychlejší způsob, jak získat streaming analytics nahoru a běží v Azure pomocí jazyka SQL už znáte. Azure Stream Analytics je služba úlohy, není potřeba trávit čas správu clusterů, takže není nutné se starat o výpadků díky 99,9 % podle smlouvy SLA na úrovni projektu. Fakturace se provádí taky na úrovni úlohy vytváření nízké náklady na spuštění (jednu jednotku streamování), ale škálovatelné (až 192 jednotek streamování). Je mnohem méně nákladné spustit několik úloh Stream Analytics, než je spustit a spravovat cluster.

Azure Stream Analytics obsahuje celou řadu možností out-of-the-box. Můžete okamžitě využít následující funkce bez jakékoli další konfigurace:

* Integrované dočasné operátory, jako například [agregací](stream-analytics-window-functions.md), dočasná spojení a dočasné analytických funkcí.
* Nativní Azure [vstupní](stream-analytics-add-inputs.md) a [výstup](stream-analytics-define-outputs.md) adaptéry
* Podpora pro pomalé změnu [odkazují na data](stream-analytics-use-reference-data.md) (označované také jako tabulky vyhledávání), včetně spojením s geoprostorové referenčních dat pro monitorování geografických zón.
* Integrovaná řešení, jako například [detekce anomálií](stream-analytics-machine-learning-anomaly-detection.md)
* Více časových oken ve stejném dotazu
* Možnost vytvořit více dočasné operátory v libovolné posloupnosti.
* Pod 100 ms celkovou latenci ze vstupu přicházejících u služby Event Hubs do výstupního cílová ve službě Event Hubs, včetně zpoždění sítě z a do služby Event Hubs v Vysoká propustnost

## <a name="when-to-use-other-technologies"></a>Kdy použít jiné technologie

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Budete muset ze vstupních nebo výstupních k Kafka

Azure Stream Analytics není máte Apache Kafka vstup nebo výstup adaptér. Pokud máte události doručení nebo nutné odeslat Kafka a není nutné požadavek na spuštění clusteru Kafka, můžete pokračovat k odesílání událostí do služby Event Hubs pomocí rozhraní API Event Hubs Kafka beze změny odesílatele události použití Stream Analytics. Pokud je potřeba spouštět Kafka cluster, můžete použít strukturovaného streamování Sparku, která plně podporuje [Azure Databricks](../azure-databricks/index.yml), nebo Storm na [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Chcete zadat vlastní deserializers, funkcí UDF a UDA v jiném jazyce než v jazyce JavaScript neboC#

Azure Stream Analytics podporuje uživatelem definované funkce (UDF) nebo uživatelem definované agregace (UDA) v jazyce JavaScript pro cloudové úlohy a C# pro úlohy IoT Edge. C#uživatelem definované deserializers jsou také podporovány. Pokud chcete implementovat deserializátor, uživatelem definovanou FUNKCI nebo UDA v jiných jazycích, jako je Java nebo Python, můžete použít strukturovaného streamování Sparku. Můžete také spustit službu Event Hubs **EventProcessorHost** na virtuálních počítačích pro zpracování libovolných datových proudů.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Je vaše řešení ve více cloudu nebo v místním prostředí

Azure Stream Analytics je proprietární technologii od Microsoftu a je dostupná pouze na Azure. Pokud potřebujete řešení jeho přenositelnost napříč Cloudy nebo na místě, vezměte v úvahu opensourcových technologií, jako je například strukturovaného streamování Sparku nebo Storm.

## <a name="next-steps"></a>Další postup

* [Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)
* [Vytvoření úlohy Stream Analytics pomocí Azure Powershellu](stream-analytics-quick-create-powershell.md)
* [Vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Vytvoření úlohy Stream Analytics pomocí Visual Studio Code](quick-create-vs-code.md)