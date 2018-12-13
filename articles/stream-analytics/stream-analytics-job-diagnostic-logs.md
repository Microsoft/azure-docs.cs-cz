---
title: Řešení potíží s Azure Stream Analytics a využívat protokoly diagnostiky
description: Tento článek popisuje, jak analyzovat diagnostické protokoly v Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: db3c9874676e3240f6896c1e1ff8f873360c20d5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090818"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Řešení potíží s Azure Stream Analytics s využitím diagnostických protokolů

V některých případech úlohy Azure Stream Analytics neočekávaně zastaví zpracování. Je důležité mít možnost vyřešit tento typ události. Událost může být způsobena výsledek neočekávaný dotaz, připojení k zařízení nebo kvůli výpadku neočekávané služby. Protokoly diagnostiky ve službě Stream Analytics může pomoct identifikovat příčiny problémů při jejich výskytu a zkrácení času obnovení.

## <a name="log-types"></a>Typy protokolů

Stream Analytics nabízí dva typy protokolů: 
* [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (je pořád zapnutý). Protokoly aktivit poskytují přehled o operace provedené na úlohy.
* [Protokoly diagnostiky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (Konfigurovat). Diagnostické protokoly poskytují podrobnější přehled o všechno, co se stane s úlohou. Diagnostické protokoly spuštění při vytvoření úlohy a end při odstraňování úlohy. Při aktualizaci úlohy a během jejího běhu pokrývají události.

> [!NOTE]
> Služby, jako je Azure Storage, Azure Event Hubs a Azure Log Analytics můžete použít k analýze dat aplikace. Budou se vám účtovat podle cenového modelu pro služby.
>

## <a name="turn-on-diagnostics-logs"></a>Zapnout diagnostické protokoly

Diagnostické protokoly jsou **vypnout** ve výchozím nastavení. Chcete-li zapnout diagnostické protokoly, postupujte takto:

1.  Přihlaste se k webu Azure portal a přejděte do okna úloha streamování. V části **monitorování**vyberte **diagnostické protokoly**.

    ![Navigace v okně diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Vyberte **zapnout diagnostiku**.

    ![Zapnout diagnostické protokoly Stream Analytics](./media/stream-analytics-job-diagnostic-logs/turn-on-diagnostic-logs.png)

3.  Na **nastavení diagnostiky** stránky, pro **stav**vyberte **na**.

    ![Změna stavu pro diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/save-diagnostic-log-settings.png)

4.  Archivace cíle (účet úložiště, Log Analytics v Centru událostí), který chcete nastavte. Vyberte kategorie protokoly, které chcete shromažďovat (spuštění, vytváření obsahu). 

5.  Uložte novou konfiguraci diagnostiky.

Konfigurace diagnostiky trvá asi 10 minut se projeví. Poté, v protokolech začnou zobrazovat v nakonfigurovaných archivace cílové (ty se zobrazí na **diagnostické protokoly** stránky):

![Navigace v okně diagnostické protokoly – archivace cíle](./media/stream-analytics-job-diagnostic-logs/view-diagnostic-logs-page.png)

Další informace o konfiguraci diagnostiky najdete v tématu [shromažďování a používání diagnostická data z vašich prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Kategorie protokolu diagnostiky

V současné době zachycení jsme dvě kategorie protokoly diagnostiky:

* **Vytváření**. Zachycení protokolování událostí, které se vztahují k úloze vytváření operace: úlohy vytváření, přidávání a odstraňování vstupy a výstupy, přidávání a aktualizaci dotazu, spouštění a zastavování úlohy.
* **Spuštění**. Jsou zaznamenané události, ke kterým dochází při provádění úlohy:
    * Chyby připojení
    * Chyby zpracování dat, včetně:
        * Události, které nejsou v souladu s definicí dotazu (pole neodpovídající typy a hodnoty, chybějící pole a tak dále)
        * Chyby vyhodnocení výrazů
    * Další události a chyby

## <a name="diagnostics-logs-schema"></a>Schéma protokoly diagnostiky

Všechny protokoly se ukládají ve formátu JSON. Každý záznam obsahuje následující společných polí řetězec:

Název | Popis
------- | -------
time | Časové razítko (ve standardu UTC) v protokolu.
resourceId | ID prostředku, že operace konal úplně, velkými písmeny. Obsahuje ID předplatného, skupinu prostředků a název úlohy. Například   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategorie, buď protokolu **provádění** nebo **Authoring**.
operationName | Název operace, která je zaznamenána. Například **odesílat události: Chyba zápisu výstupu SQL do mysqloutput**.
status | Stav operace. Například **neúspěšné** nebo **Succeeded**.
úroveň | Úroveň protokolu. Například **chyba**, **upozornění**, nebo **informativní**.
properties | Podrobnosti konkrétní položky protokolu serializován jako řetězec formátu JSON. Další informace najdete v následující části.

### <a name="execution-log-properties-schema"></a>Schéma vlastnosti protokolu spuštění

Protokoly spuštění obsahují informace o události, ke kterým došlo během provádění úlohy Stream Analytics. Schéma vlastnosti se liší v závislosti na typu události. V současné době máme následující typy protokoly spuštění:

### <a name="data-errors"></a>Chyby dat

Všechny chyby, ke které dojde během úlohy zpracování dat je v této kategorii protokolů. Nejčastěji tyto protokoly se vytvoří během data načtená, serializaci a operace zápisu. Tyto protokoly neobsahují chyby připojení. K chybám připojení jsou považovány za obecné události.

Název | Popis
------- | -------
Zdroj | Název úlohy vstup nebo výstup, kde došlo k chybě.
Zpráva | Zpráva přidružená k chybě.
Typ | Typ chyby. Například **DataConversionError**, **CsvParserError**, nebo **ServiceBusPropertyColumnMissingError**.
Data | Obsahuje data, která slouží k vytvoření přesně najít zdroje chyby. Pro zkrácení, v závislosti na velikosti na základě práv subjektů.

V závislosti na tom **operationName** hodnota, data chyby mají následující schéma:
* **Serializace událostí**. Serializace událostí, ke kterým došlo během operace čtení událostí. K nim dojde, když data na vstupu nevyhovuje schématu dotaz pro některého z těchto důvodů:
    * *Neshoda typů při události (de) serializovat*: Určuje pole, která je příčinou chyby.
    * *Nelze přečíst událost, neplatná Serializační*: obsahuje informace o umístění vstupních dat, kde došlo k chybě. Obsahuje název objektu blob pro vstupní objekt blob, posun a ukázková data.
* **Odesílání událostí**. Odesílání událostí, ke kterým došlo během operace zápisu. Identifikují streamování událostí, která způsobila chybu.

### <a name="generic-events"></a>Obecné události

Obecné události pokrývají všechno ostatní.

Název | Popis
-------- | --------
Chyba | (volitelné) Informace o chybě. Obvykle se jedná o informace o výjimce, pokud je k dispozici.
Zpráva| Zpráva protokolu.
Typ | Typ zprávy. Mapuje na interní kategorizace chyb. Například **JobValidationError** nebo **BlobOutputAdapterInitializationFailure**.
ID korelace | [Identifikátor GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , který jednoznačně identifikuje provádění úlohy. Zahájení úlohy všechny položky protokolu spuštění od okamžiku, dokud je úloha pozastavena mají stejné **ID korelace** hodnotu.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
