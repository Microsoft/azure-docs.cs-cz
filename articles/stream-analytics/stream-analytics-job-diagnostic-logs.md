---
title: Řešení potíží s Azure Stream Analytics a využívat protokoly diagnostiky
description: Tento článek popisuje, jak analyzovat diagnostické protokoly v Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: seodec18
ms.openlocfilehash: e712dfd755082e6b36066b0058ec18545d5c8417
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412831"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Řešení potíží s Azure Stream Analytics s využitím diagnostických protokolů

V některých případech úlohy Azure Stream Analytics neočekávaně zastaví zpracování. Je důležité mít možnost tento druh události vyřešit. Selhání může nastat výsledek neočekávaný dotaz, připojení k zařízení nebo kvůli výpadku neočekávané služby. Protokoly diagnostiky ve službě Stream Analytics může pomoct identifikovat příčiny problémů, pokud dojde k a zkrácení času obnovení.

## <a name="log-types"></a>Typy protokolů

Stream Analytics nabízí dva typy protokolů:

* [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (AlwaysOn), které poskytují přehled o operace provedené na úlohy.

* [Protokoly diagnostiky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (možnost konfigurace), které poskytují podrobnější přehled o všechno, co, který se stane s úlohou. Při odstraňování úlohy, protokoly diagnostiky spuštění při vytvoření úlohy a ukončení. Při aktualizaci úlohy a během jejího běhu pokrývají události.

> [!NOTE]
> Služby, jako je Azure Storage, Azure Event Hubs a Azure Log Analytics můžete použít k analýze dat aplikace. Budou se vám účtovat podle cenového modelu pro služby.

## <a name="debugging-using-activity-logs"></a>Ladění pomocí aktivity protokoly

Protokoly aktivit jsou standardně povoleny a poskytnout podrobný přehled o operacích prováděných při vaší úlohy Stream Analytics. Informace v protokolech aktivit může pomoct odhalit jeho příčinu problémů vliv na vaši úlohu. Proveďte následující kroky a použít protokoly aktivit ve službě Stream Analytics:

1. Přihlaste se k Azure portal a vyberte **protokolu aktivit** pod **přehled**.

   ![Stream Analytics protokolu aktivit](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Zobrazí se seznam operací, které byly provedeny. Všechny operace, která způsobila selhání úlohy má bublinu red informace.

3. Kliknutím na operaci zobrazíte jeho souhrnné zobrazení. Informace v tomto poli je často omezené. Přečtěte si další podrobnosti o operaci, klikněte na tlačítko **JSON**.

   ![Stream Analytics aktivitu protokolu operace souhrnu](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Přejděte dolů k položce **vlastnosti** část JSON, který poskytuje podrobnosti o chybě, která způsobila selhání operace. V tomto příkladu k selhání došlo kvůli chybě v běhu from out of hodnoty zeměpisné šířky vázaná.

   ![Podrobnosti o chybě JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Můžete provést opravné akce, které na základě chybové zprávy ve formátu JSON. V tomto příkladu zkontroluje, aby je zeměpisná šířka hodnota v rozmezí od-90 stupňů a 90 stupňů je potřeba přidat do dotazu.

6. Pokud chybová zpráva v protokolech aktivit se užitečné identifikovat hlavní příčinu, povolení diagnostických protokolů a používat službu Log Analytics.

## <a name="send-diagnostics-to-log-analytics"></a>Odeslání diagnostiky do Log Analytics

Důrazně doporučujeme zapnout diagnostické protokoly a jejich odesílání do Log Analytics. Diagnostické protokoly jsou **vypnout** ve výchozím nastavení. Chcete-li zapnout diagnostické protokoly, postupujte takto:

1.  Přihlaste se k webu Azure portal a přejděte do vaší úlohy Stream Analytics. V části **monitorování**vyberte **diagnostické protokoly**. Potom vyberte **zapnout diagnostiku**.

    ![Navigace v okně diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Vytvoření **název** v **nastavení diagnostiky** a zaškrtněte políčko vedle položky **odesílat do Log Analytics**. Pak přidejte existující nebo vytvořte novou **pracovní prostor Log analytics**. Zaškrtněte políčka pro **provádění** a **Authoring** pod **protokolu**, a **AllMetrics** pod **METRIKA** . Klikněte na **Uložit**.

    ![Nastavení pro diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Při spuštění vaší úlohy Stream Analytics, diagnostické protokoly jsou směrovány do vašeho pracovního prostoru Log Analytics. Přejděte do pracovního prostoru Log Analytics a zvolte **protokoly** pod **Obecné** oddílu.

   ![Protokoly log Analytics v části Obecné části](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Je možné [napsat vlastní dotaz](../azure-monitor/log-query/get-started-portal.md) hledat podmínky, rozpoznávejte trendy, analyzovat vzory a poskytují přehledy na základě vašich dat. Například můžete napsat dotaz pro filtrování pouze diagnostické protokoly, které mají zpráva "úloha streamování se nezdařilo." Diagnostické protokoly z Azure Stream Analytics se ukládají v **AzureDiagnostics** tabulky.

   ![Diagnostika dotazu a výsledky](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Pokud máte dotaz, který je vyhledat správný protokoly, uložte ho výběrem **Uložit** a zadejte název a kategorie. Potom můžete vytvořit výstrahu tak, že vyberete **nové pravidlo upozornění**. Dále určete se vyskytl výstražný stav. Vyberte **podmínku** a zadejte prahovou hodnotu a frekvenci, s jakou se vyhodnotí toto vlastní protokol hledání.  

   ![Vyhledávání diagnostických protokolů](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Vyberte skupinu akcí a zadejte podrobnosti výstrahy, jako je název a popis, abyste mohli vytvořit pravidlo upozornění. Diagnostické protokoly různé úlohy můžou směrovat do stejného pracovního prostoru Log Analytics. Můžete nastavit upozornění, jakmile, které fungují na všech úloh.  

## <a name="diagnostics-log-categories"></a>Kategorie protokolu diagnostiky

V současné době zachycení jsme dvě kategorie protokoly diagnostiky:

* **Vytváření**: Shromažďuje události protokolu, které se vztahují k úloze vytváření operace, jako je vytvoření úlohy, přidávání a odstraňování vstupy a výstupy, přidávání a aktualizaci dotazu a spouštění nebo zastavování úlohy.

* **Spuštění**: Jsou zaznamenané události, ke kterým dochází při provádění úlohy.
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
properties | Podrobnosti konkrétní položky protokolu serializován jako řetězec formátu JSON. Další informace najdete v následující části v tomto článku.

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
    * *Nelze přečíst událost, neplatná Serializační*: Obsahuje informace o umístění vstupních dat, kde došlo k chybě. Obsahuje název objektu blob pro vstupní objekt blob, posun a ukázková data.
* **Odesílání událostí**. Odesílání událostí, ke kterým došlo během operace zápisu. Identifikují streamování událostí, která způsobila chybu.

### <a name="generic-events"></a>Obecné události

Obecné události pokrývají všechno ostatní.

Název | Popis
-------- | --------
Chyba | (volitelné) Informace o chybě. Obvykle to je informace o výjimce, pokud je k dispozici.
Zpráva| Zpráva protokolu.
Typ | Typ zprávy. Mapuje na interní kategorizace chyb. Například **JobValidationError** nebo **BlobOutputAdapterInitializationFailure**.
ID korelace | [Identifikátor GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , který jednoznačně identifikuje provádění úlohy. Zahájení úlohy všechny položky protokolu spuštění od okamžiku, dokud je úloha pozastavena mají stejné **ID korelace** hodnotu.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
