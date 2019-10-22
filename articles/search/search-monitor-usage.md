---
title: Monitorování využití prostředků a metrik dotazů pro vyhledávací službu – Azure Search
description: Povolte protokolování, Získejte metriky aktivity dotazů, využití prostředků a další systémová data z Azure Search služby.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.openlocfilehash: fe8061f8e99742f9dc5c1181235c4203aaad82ca
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72331213"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Monitorovat spotřebu prostředků a aktivity dotazů v Azure Search

Na stránce Přehled služby Azure Search můžete zobrazit systémová data o využití prostředků, metrikách dotazů a velikosti dostupné kvóty k vytvoření dalších indexů, indexerů a zdrojů dat. Portál můžete použít také ke konfiguraci Log Analytics nebo jiného prostředku, který se používá pro trvalé shromažďování dat. 

Nastavení protokolů je užitečné pro samoobslužnou diagnostiku a zachovávání provozní historie. Interně protokoly existují v back-endu po krátkou dobu, postačující pro šetření a analýzu, pokud zadáte lístek podpory. Pokud chcete mít kontrolu nad a přistupovat k informacím protokolu, měli byste nastavit jedno z řešení popsaných v tomto článku.

V tomto článku se dozvíte o možnostech monitorování, o tom, jak povolit protokolování a ukládání protokolů a jak zobrazit obsah protokolu.

## <a name="metrics-at-a-glance"></a>Rychlé metriky

Části **využití** a **monitorování** , které jsou na stránce Přehled integrované, se zaměřením na využití prostředků a metriky spuštění dotazů. Tyto informace budou k dispozici hned po zahájení používání služby bez nutnosti konfigurace. Tato stránka se aktualizuje každých několik minut. Pokud dokončujete rozhodnutí o tom, [která úroveň se má použít pro produkční úlohy](search-sku-tier.md), nebo jestli se má [upravit počet aktivních replik a oddílů](search-capacity-planning.md), můžou vám tyto metriky při rozhodování zobrazit, jak rychle se prostředky spotřebují. a jak dobře aktuální konfigurace zpracovává existující zatížení.

Na kartě **využití** se zobrazuje dostupnost prostředků vzhledem k aktuálním [limitům](search-limits-quotas-capacity.md). Následující obrázek je pro bezplatnou službu, která je omezené na 3 objekty každého typu a 50 MB úložiště. Základní nebo standardní služba má vyšší limity, a pokud nakročíte počty oddílů, maximální velikost úložiště se provedla poměrně.

![Stav použití vzhledem k platným omezením](./media/search-monitor-usage/usage-tab.png
 "Stav použití vzhledem k platným omezením")

## <a name="queries-per-second-qps-and-other-metrics"></a>Dotazy za sekundu (QPS) a další metriky

Karta **monitorování** zobrazuje klouzavý průměr pro metriky, jako jsou vyhledávací *dotazy za sekundu* (QPS), agregované za minutu. 
*Latence hledání* je doba, kterou vyhledávací služba potřebuje ke zpracování vyhledávacích dotazů, agregovaných za minutu. *Procento omezených vyhledávacích dotazů* (nezobrazený) je procento vyhledávacích dotazů, které byly omezeny, a také agregované za minutu.

Tato čísla jsou přibližná a jsou určena k získání Obecné informace o tom, jak dobře systém obsluhuje požadavky. Skutečný QPS může být vyšší nebo nižší než číslo hlášené na portálu.

![Aktivita dotazů za sekundu](./media/search-monitor-usage/monitoring-tab.png "Aktivita dotazů za sekundu")

## <a name="activity-logs"></a>Protokoly aktivit

**Protokol aktivit** shromažďuje informace z Azure Resource Manager. Příklady informací v protokolu aktivit zahrnují vytvoření nebo odstranění služby, aktualizaci skupiny prostředků, kontrolu dostupnosti názvů nebo získání přístupového klíče služby pro zpracování žádosti. 

K **protokolu aktivit** můžete přistupovat z levého navigačního podokna nebo z oznámení na panelu příkazů v horním okně nebo na stránce **diagnostikovat a řešit problémy** .

Pro úlohy v rámci služby, jako je vytvoření indexu nebo odstranění zdroje dat, uvidíte obecná oznámení, jako je například "získat klíč správce" pro každý požadavek, ale ne konkrétní akci. Pro tuto úroveň informací musíte povolit řešení pro monitorování doplňku.

## <a name="add-on-monitoring-solutions"></a>Řešení monitorování doplňku

Azure Search neukládá žádná data mimo objekty, které spravuje, což znamená, že data protokolu musí být ukládána externě. V případě, že chcete zachovat data protokolu, můžete nakonfigurovat kterýkoli z níže uvedených prostředků. 

V následující tabulce jsou porovnávány možnosti pro ukládání protokolů a přidání podrobného monitorování operací služby a úloh dotazů prostřednictvím Application Insights.

| Prostředek | Použití |
|----------|----------|
| [Protokoly Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Protokolované události a metriky dotazů na základě níže uvedených schémat. Události jsou protokolovány do Log Analytics pracovního prostoru. Můžete spouštět dotazy na pracovní prostor a vracet tak podrobné informace z protokolu. Další informace najdete v tématu [Začínáme s protokoly Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) . |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Protokolované události a metriky dotazů na základě níže uvedených schémat. Události se zaznamenávají do kontejneru objektů BLOB a ukládají se do souborů JSON. K zobrazení obsahu souboru použijte Editor JSON.|
| [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/) | Protokolované události a metriky dotazů na základě schémat popsaných v tomto článku. Tuto možnost vyberte jako alternativní službu pro shromažďování dat pro velmi velké protokoly. |

Protokoly Azure Monitor a BLOB Storage jsou k dispozici jako bezplatná služba, takže si ji můžete vyzkoušet bez poplatků za dobu života předplatného Azure. Application Insights se zaregistrovat a použít, pokud je velikost dat aplikace za určitých mezí (podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) ).

V další části se seznámíte s postupem povolení a používání úložiště objektů BLOB v Azure ke shromažďování a přístupu k datům protokolu vytvořeným pomocí operací Azure Search.

## <a name="enable-logging"></a>Povolit protokolování

Protokolování pro indexování a úlohy dotazů je ve výchozím nastavení vypnuté a závisí na řešeních doplňku pro infrastrukturu protokolování a dlouhodobém externím úložišti. Sám o sobě platí, že jediná trvalá data v Azure Search jsou objekty, které vytváří a spravují, takže protokoly se musí ukládat jinde.

V této části se dozvíte, jak používat úložiště objektů BLOB k ukládání protokolovaných událostí a dat metrik.

1. [Vytvořte účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) , pokud ho ještě nemáte. Můžete ji umístit do stejné skupiny prostředků, jako je Azure Search, aby se zjednodušilo pozdější vyčištění, pokud chcete odstranit všechny prostředky používané v tomto cvičení.

   Váš účet úložiště musí existovat ve stejné oblasti jako Azure Search.

2. Otevřete stránku Přehled vyhledávací služby. V levém navigačním podokně přejděte dolů na **sledování** a klikněte na **Povolit monitorování**.

   ![Povolit monitorování](./media/search-monitor-usage/enable-monitoring.png "Povolení monitorování")

3. Vyberte data, která chcete exportovat: protokoly, metriky nebo obojí. Můžete ho zkopírovat do účtu úložiště, odeslat ho do centra událostí nebo ho exportovat do Azure Monitor protokolů.

   V případě archivace do úložiště objektů BLOB musí existovat pouze účet úložiště. Kontejnery a objekty blob budou při exportu dat protokolu vytvořeny podle potřeby.

   ![Konfigurace archivu úložiště objektů BLOB](./media/search-monitor-usage/configure-blob-storage-archive.png "Konfigurace archivu úložiště objektů BLOB")

4. Uložte profil.

5. Testování protokolování vytvořením nebo odstraněním objektů (vytváří události protokolu) a odesláním dotazů (generuje metriky). 

Protokolování je povoleno, jakmile Profil uložíte. Kontejnery se vytvářejí pouze v případě, že existuje aktivita pro protokolování nebo měření. Když se data zkopírují do účtu úložiště, data se naformátují jako JSON a umístí se do dvou kontejnerů:

* Insights-logs-operationlogs: pro hledání protokolů přenosů
* Insights – metriky – pt1m: pro metriky

**Trvá to jednu hodinu, než se kontejnery zobrazí v úložišti objektů BLOB. Jeden objekt BLOB se za hodinu vychází z každého kontejneru.**

K zobrazení souborů můžete použít [Visual Studio Code](#download-and-open-in-visual-studio-code) nebo jiný Editor JSON. 

### <a name="example-path"></a>Příklad cesty

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Schéma protokolu
Objekty blob, které obsahují protokoly přenosů služby Search, jsou strukturované, jak je popsáno v této části. Každý objekt BLOB má jeden kořenový objekt nazvaný **záznamy** obsahující pole objektů log. Každý objekt BLOB obsahuje záznamy pro všechny operace, které byly provedeny během stejné hodiny.

| Name (Název) | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |Časové razítko operace |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/VÝCHOZÍ/POSKYTOVATELÉ/<br/> Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Vaše ResourceId |
| operationName |string |"Query. Search" |Název operace |
| operationVersion |string |"2019-05-06" |Použitá verze rozhraní API |
| category |string |"OperationLogs" |změnil |
| resultType |string |Nástup |Možné hodnoty: úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku HTTP |
| Trvání v MS |int |50 |Doba trvání operace v milisekundách |
| properties |object |Podívejte se na následující tabulku. |Objekt obsahující data specifická pro danou operaci |

**Schéma vlastností**

| Name (Název) | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| Popis |string |"GET/Indexes (' Content ')/docs" |Koncový bod operace |
| Dotaz |string |"? Search = AzureSearch & $count = true & API-Version = 2019-05-06" |Parametry dotazu |
| Dokumenty |int |42 |Počet zpracovaných dokumentů |
| indexName |string |"testindex" |Název indexu přidruženého k operaci |

## <a name="metrics-schema"></a>Schéma metrik

Pro požadavky na dotazy jsou zachyceny metriky.

| Name (Název) | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/VÝCHOZÍ/POSKYTOVATELÉ/<br/>Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |ID prostředku |
| metricName |string |Latence |název metriky |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |časové razítko operace |
| Vypočítat |int |64 |Průměrná hodnota nezpracovaných vzorků v časovém intervalu metriky |
| minimálně |int |37 |Minimální hodnota nezpracovaných vzorků v časovém intervalu metriky |
| velikosti |int |78 |Maximální hodnota nezpracovaných vzorků v časovém intervalu metriky |
| čtení |int |258 |Celková hodnota nezpracovaných vzorků v časovém intervalu metriky |
| count |int |4 |Počet nezpracovaných vzorků použitých k vygenerování metriky |
| timegrain |string |"PT1M" |Časový interval metriky v normě ISO 8601 |

Všechny metriky jsou hlášeny v intervalu 1 minuty. Každá metrika zpřístupňuje minimální, maximální a průměrné hodnoty za minutu.

V případě metriky SearchQueriesPerSecond je minimální hodnotou pro vyhledávací dotazy za sekundu, která byla zaregistrována během této minuty. Totéž platí pro maximální hodnotu. Průměrná hodnota je agregovaná napříč celou minutou.
Zamyslete se nad tímto scénářem v průběhu jedné minuty: jednu sekundu vysokého zatížení, která je maximální hodnota pro SearchQueriesPerSecond, následované 58 sekundami průměrného zatížení a nakonec jednu sekundou pouze s jedním dotazem, což je minimální hodnota.

Pro ThrottledSearchQueriesPercentage, minimum, maximum, průměr a součet musí mít všechny stejnou hodnotu: procento vyhledávacích dotazů, které byly omezeny, z celkového počtu vyhledávacích dotazů během jedné minuty.

## <a name="download-and-open-in-visual-studio-code"></a>Stažení a otevření v Visual Studio Code

K zobrazení souboru protokolu můžete použít libovolný editor JSON. Pokud ho nemáte, doporučujeme [Visual Studio Code](https://code.visualstudio.com/download).

1. V Azure Portal otevřete svůj účet úložiště. 

2. V levém navigačním podokně klikněte na **objekty blob**. Měli byste vidět **Insights-logs-operationlogs** a **Insights – metriky – pt1m**. Tyto kontejnery vytvoří Azure Search, když se data protokolu exportují do úložiště objektů BLOB.

3. Klikněte na hierarchii složek dolů, dokud nedosáhnete souboru. JSON.  K stažení souboru použijte kontextovou nabídku.

Po stažení souboru ho otevřete v editoru JSON, abyste mohli zobrazit jeho obsah.

## <a name="use-system-apis"></a>Použití systémových rozhraní API
REST API Azure Search a .NET SDK poskytují programový přístup k metrikám služeb, indexům a informacím indexeru a počtům dokumentů.

* [Získat statistiku služeb](/rest/api/searchservice/get-service-statistics)
* [Získat statistiku indexu](/rest/api/searchservice/get-index-statistics)
* [Počet dokumentů](/rest/api/searchservice/count-documents)
* [Získat stav indexeru](/rest/api/searchservice/get-indexer-status)

Pokud chcete povolit použití PowerShellu nebo rozhraní příkazového řádku Azure CLI, přečtěte si [tu](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)dokumentaci.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o správě služby a [výkonu a optimalizaci](search-performance-optimization.md) pro pokyny k ladění, [Spravujte službu Search na Microsoft Azure](search-manage.md) .
