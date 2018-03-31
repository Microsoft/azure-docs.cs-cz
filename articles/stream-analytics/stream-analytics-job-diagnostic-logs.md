---
title: Řešení potíží s Azure Stream Analytics s protokoly diagnostiky | Microsoft Docs
description: Naučte se analýza protokolů diagnostiky z úlohy Stream Analytics v Microsoft Azure.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 164d522d7beaea222dbc408765877fa67a34c203
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Řešení potíží s Azure Stream Analytics s použitím protokolů diagnostiky

V některých případech úlohu služby Azure Stream Analytics neočekávaně zastaví zpracování. Je důležité, abyste mohli vyřešit tento druh událostí. Událost může být způsobena výsledek neočekávaný dotaz, připojení k zařízení nebo neočekávané služby výpadku. Diagnostické protokoly v Stream Analytics může pomoci určit příčinu problémů při jejich výskytu a zkrátit čas obnovení.

## <a name="log-types"></a>Typy protokolu

Stream Analytics poskytuje dva typy protokolů: 
* [Protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (je pořád zapnutý). Protokoly aktivity poskytují přehled o operace provedené na úlohách.
* [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (Konfigurovat). Diagnostické protokoly nabízejí širší přehled vše, co se stane s úlohou. Diagnostické protokoly spuštění při vytvoření úlohy a end při odstranění úlohy. Při aktualizaci úlohy, a když je spuštěná pokrývaly události.

> [!NOTE]
> Služby, jako je Azure Storage, Azure Event Hubs a Azure Log Analytics můžete použít k analýze dat neodpovídající. Budou se vám účtovat podle cenový model pro tyto služby.
>

## <a name="turn-on-diagnostics-logs"></a>Zapnout diagnostické protokoly

Diagnostické protokoly jsou **vypnout** ve výchozím nastavení. Chcete-li na protokolů diagnostiky, proveďte tyto kroky:

1.  Přihlaste se k portálu Azure a přejděte do okna úlohy streamování. V části **monitorování**, vyberte **protokolů diagnostiky**.

    ![Okno Navigace do protokolů diagnostiky](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Vyberte **zapněte diagnostiku**.

    ![Zapnout diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  Na **nastavení diagnostiky** stránky, pro **stav**, vyberte **na**.

    ![Změnit stav protokolů diagnostiky](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Archivace cíl (účet úložiště, centra událostí, analýzy protokolů), který chcete nastavte. Potom vyberte příslušné kategorie protokoly, které chcete shromažďovat (spuštění, vytváření obsahu). 

5.  Uložte novou konfiguraci diagnostiky.

Konfigurace diagnostiky trvá přibližně 10 minut vstoupily v platnost. Potom protokoly spuštění zobrazovaných v nakonfigurovaných archivace cíl (ty se zobrazí na **protokolů diagnostiky** stránky):

![Okno Navigace do protokolů diagnostiky - archivace cíle](./media/stream-analytics-job-diagnostic-logs/image4.png)

Další informace o konfiguraci diagnostiky najdete v tématu [shromažďovat a využívat diagnostická data z vašich prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Kategorie protokolu diagnostiky

V současné době jsme zaznamenat dvě kategorie protokolů diagnostiky:

* **Vytváření**. Záznamy protokolu událostí, které se vztahují k úloze vytváření operations: vytvoření, přidání a odstranění vstup a výstupy, přidávání a aktualizuje se dotaz, spouštění a zastavování úlohy úlohy.
* **Provádění**. Zachycuje události, ke kterým došlo během provádění úlohy:
    * K chybám připojení
    * Chyby zpracování dat, včetně:
        * Události, které nevyhovují definice dotazu (neodpovídající pole typy a hodnoty, chybějící pole a tak dále)
        * Chyby vyhodnocení výrazu
    * Ostatní události a chyby

## <a name="diagnostics-logs-schema"></a>Diagnostické protokoly schématu

Všechny protokoly se ukládají ve formátu JSON. Každá položka má následující běžné pole řetězce:

Název | Popis
------- | -------
time | Časové razítko (ve formátu UTC) v protokolu.
resourceId | ID prostředku, že operace byla provedena, velkými písmeny. Obsahuje ID předplatného, skupinu prostředků a název úlohy. Například   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategorie, buď protokolu **provádění** nebo **vytváření**.
operationName | Název operace, která je zaznamenána. Například **odesílat události: SQL výstup zapsat chyby do mysqloutput**.
status | Stav operace. Například **se nezdařilo** nebo **úspěšné**.
úroveň | Úroveň protokolu. Například **chyba**, **upozornění**, nebo **informační**.
properties | Podrobnosti konkrétní položky protokolu serializován jako řetězec formátu JSON. Další informace najdete v následujících částech.

### <a name="execution-log-properties-schema"></a>Spuštění protokolu vlastnosti schématu

Protokoly spouštění mít informace o událostech, ke kterým došlo během provádění úlohy Stream Analytics. Schéma vlastnosti se liší v závislosti na typu události. V současné době máme následující typy protokoly spouštění:

### <a name="data-errors"></a>Data chyb

Všechny chyby, ke které dochází při úlohy je zpracování dat je v této kategorii protokolů. Tyto protokoly nejčastěji se vytvoří během čtení, data serializace a operace zápisu. Tyto protokoly neobsahují chyby připojení. K chybám připojení jsou považovány za obecné události.

Název | Popis
------- | -------
Zdroj | Název úlohy vstupem nebo výstupem, kde došlo k chybě.
Zpráva | Zpráva přidružená k chybě.
Typ | Typ chyby. Například **DataConversionError**, **CsvParserError**, nebo **ServiceBusPropertyColumnMissingError**.
Data | Obsahuje data, které jsou užitečné pro přesně najít zdroji této chyby. Předmětem zkrácení, v závislosti na velikosti.

V závislosti na tom **operationName** hodnotu chyb dat mají následující schéma:
* **Serializace událostí**. Serializace událostí, ke kterým došlo během operace čtení událostí. K nim dojde, když data na vstupu nevyhovuje schématu dotazu pro jednu z následujících důvodů:
    * *Neshoda typu během události (de) serializovat*: identifikuje pole, která je příčinou chyby.
    * *Nelze přečíst událost, neplatný serializace*: uvádí informace o umístění vstupních dat, kde došlo k chybě. Obsahuje název objektu blob pro vstup objektu blob, posun a ukázku data.
* **Odesílání událostí**. Odesílání událostí, ke kterým došlo během operace zápisu. Identifikují událost streamování, který chybu způsobil.

### <a name="generic-events"></a>Obecné události

Obecné události zahrnují nic jiného.

Název | Popis
-------- | --------
Chyba | (volitelné) Informace o chybě. Obvykle se jedná o informace o výjimce, pokud je k dispozici.
Zpráva| Zprávy protokolu.
Typ | Typ zprávy. Mapuje interní kategorizaci chyb. Například **JobValidationError** nebo **BlobOutputAdapterInitializationFailure**.
ID korelace | [Identifikátor GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) který jednoznačně identifikuje provádění úlohy. Zahájení úlohy všech položek protokolů provádění od okamžiku, dokud je úloha pozastavena mít stejnou **ID korelace** hodnotu.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme s Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka jazyka dotazů Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
