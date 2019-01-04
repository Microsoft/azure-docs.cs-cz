---
title: Plánování a spuštění pomocí služby Data Factory | Dokumentace Microsoftu
description: Další aspekty plánování a spouštění aplikačního modelu služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: a70c3ddb624639411dbee961b1c4d59ac1277147
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016082"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory plánování a provádění
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [spouštění kanálů a triggery](../concepts-pipeline-execution-triggers.md) článku.

Tento článek vysvětluje aspekty plánování a spouštění aplikačního modelu služby Azure Data Factory. Tento článek předpokládá, že chápete základní informace o konceptech modelu aplikace služby Data Factory, včetně aktivit, kanálů, propojené služby a datové sady. Základní koncepty služby Azure Data Factory najdete v následujících článcích:

* [Úvod do služby Data Factory](data-factory-introduction.md)
* [Kanály](data-factory-create-pipelines.md)
* [Datové sady](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Počátečním a koncovým časem kanálu
Kanál je aktivní jenom mezi jeho **start** čas a **end** čas. Nebude provedena před časem spuštění nebo po času ukončení. Pokud kanál je pozastavený, není spuštěn bez ohledu na jejich počáteční a koncový čas. Pro kanál ke spuštění by neměly být pozastaveno. V definici kanálu najdete tato nastavení (spuštění, ukončení, pozastaveno): 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Další informace najdete v těchto vlastností [vytvářet kanály](data-factory-create-pipelines.md) článku. 


## <a name="specify-schedule-for-an-activity"></a>Zadejte plán pro aktivitu
Není kanál, který se spouští. Je aktivity v kanálu, které jsou spouštěny v rámci celkového kanálu. Plán opakování pro aktivitu můžete určit pomocí **Plánovač** části kódu JSON aktivity. Například můžete naplánovat aktivity ke spuštění každou hodinu následujícím způsobem:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak je znázorněno v následujícím diagramu, určení, že plán pro aktivitu vytváří řadu přeskakujícího okna s v časem spuštění a ukončení. Aktivační událost pro přeskakující okna jsou řada pevné velikosti překrývat, souvislých časových intervalů. Tyto logické přeskakující okna pro aktivity se nazývají **okna aktivit**.

![Příklad aktivity scheduleru](media/data-factory-scheduling-and-execution/scheduler-example.png)

**Plánovač** vlastnost pro aktivitu je volitelná. Pokud určíte tuto vlastnost, musí odpovídat tempo, kterou zadáte v definici výstupní datovou sadu aktivity. Výstupní datové sady v současné době řídí plán. Proto je nutné výstupní datovou sadu vytvořit i v případě, aktivita nevytváří žádný výstup. 

## <a name="specify-schedule-for-a-dataset"></a>Zadejte plán pro datové sady
Každá aktivita v kanálu služby Data Factory může mít nula nebo více vstupních **datových sad** a vygenerovat výstupní datové sady. Pro aktivitu, můžete zadat tempo, ve kterém jsou k dispozici vstupní data nebo výstupní data vytvořená pomocí **dostupnosti** části v definicích datových sad. 

**Frekvence** v **dostupnosti** část Určuje časovou jednotku. Frekvence povolené hodnoty jsou: Minuta, hodina, den, týden a měsíc. **Interval** multiplikátor pro četnost určuje vlastnosti v části dostupnost. Příklad: Pokud frekvence je nastavená na den a interval je nastavená na hodnotu 1 pro výstupní datovou sadu, je každý den vytváří výstupní data. Pokud chcete zadat frekvenci jako minutu, doporučujeme nastavit interval na menší než 15. 

V následujícím příkladu se vstupní data je k dispozici po hodinách a výstupní data se vytvářejí každou hodinu (`"frequency": "Hour", "interval": 1`). 

**Vstupní datová sada:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Výstupní datová sada**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

V současné době **výstupní datovou sadu řídí plán**. Jinými slovy plánu určeného pro výstupní datová sada se používá ke spuštění aktivity za běhu. Proto je nutné výstupní datovou sadu vytvořit i v případě, aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. 

V následující definici kanálu **Plánovač** vlastnost se používá k určení plánu pro aktivitu. Tato vlastnost je nepovinná. Plán pro aktivitu v současné době musí odpovídat plánu určeného pro výstupní datovou sadu.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

V tomto příkladu aktivita spouští každou hodinu mezi počátečním a koncovým časem kanálu. Výstupní data se vytvářejí každou hodinu pro tři hodiny windows (8: 00 - 9 AM, 9: 00 - 10: 00 a 10: 00 - 11 AM). 

Každá jednotka dat spotřebované nebo vyrobený proud podle počtu spuštění aktivit je volána **datový řez**. Následující diagram ukazuje příklad aktivity s jednu vstupní datovou sadu a jednu výstupní datovou sadu: 

![Dostupnost scheduleru](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagram znázorňuje hodinové datové řezy vstupní a výstupní datové sady. Diagram znázorňuje tři vstupní řezy, které jsou připravené ke zpracování. Aktivita AM 10-11 se v průběhu vytváření výstupní řez AM 10-11. 

Je možné otevřít časový interval přidružené aktuálního řezu v datové sadě JSON pomocí proměnných: [Vlastnosti SliceStart](data-factory-functions-variables.md#data-factory-system-variables) a [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobně můžete přístup k spojená s oknem aktivity na základě WindowStart a WindowEnd časový interval. Plán aktivity musí odpovídat plánu výstupní datovou sadu aktivity. Proto hodnoty vlastnosti SliceStart a SliceEnd jsou stejné jako hodnoty WindowStart a WindowEnd v uvedeném pořadí. Další informace o těchto proměnných najdete v tématu [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md#data-factory-system-variables) článků.  

Tyto proměnné můžete použít pro různé účely, ve vašem JSON. Například je použít k výběru dat z vstupní a výstupní datovou sadu představující data časových řad (například: 8 AM, AM 9). Tento příklad také používá **WindowStart** a **WindowEnd** vyberte relevantní data pro aktivitu spustit a zkopírujte ho do objektu blob s příslušnou **folderPath**. **FolderPath** parametrizované mít samostatnou složku pro každou hodinu.  

V předchozím příkladu plán zadaný pro vstupní a výstupní datové sady je stejný (každou hodinu). Pokud vstupní datová sada pro aktivitu je k dispozici v různých intervalech, Dejme tomu, že každých 15 minut, aktivity, která vytváří tento výstupní datovou sadu stále spouští jednou za hodinu je výstupní datovou sadu řídí plán aktivity. Další informace najdete v tématu [Model datové sady s jinou frekvencí](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Zásady a dostupnosti datové sady
Jste viděli využití frekvence a intervalu vlastnosti v části dostupnost v definici datové sady. Existuje několik dalších vlastností, které ovlivňují, plánování a provádění aktivity. 

### <a name="dataset-availability"></a>Dostupnost datové sady 
Následující tabulka popisuje vlastnosti, které můžete použít v **dostupnosti** části:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkční prostředí řez datové sady.<br/><br/><b>Podporované frekvence</b>: Minuta, hodina, den, týden, měsíc |Ano |Není k dispozici |
| interval |Určuje multiplikátor pro četnost<br/><br/>"Interval četnosti x" Určuje, jak často se řez.<br/><br/>Pokud potřebujete datové sady na průřezem podle počtu hodin, nastavíte <b>frekvence</b> k <b>hodinu</b>, a <b>interval</b> k <b>1</b>.<br/><br/><b>Poznámka:</b> Pokud chcete zadat frekvenci jako minutu, doporučujeme nastavit interval na menší než 15 |Ano |Není k dispozici |
| Styl |Určuje, zda by měl být řez na začátek/konec intervalu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Pokud je nastaven styl EndOfInterval Frequency je nastavená na měsíc, řez na poslední den v měsíci. Pokud je nastaven styl StartOfInterval, řez v první den v měsíci.<br/><br/>Pokud je nastaven styl EndOfInterval Frequency je nastavená na den, řez za poslední hodinu dne.<br/><br/>Je-li Frequency je nastavená na Hour a je nastaven styl EndOfInterval, řez je vytvořen na konec hodiny. Například pro určitý řez pro dobu 13: 00 – 2 hodin řez ve 14. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase plánovačem slouží k výpočtu hranice řez datové sady. <br/><br/><b>Poznámka:</b> Pokud je AnchorDateTime částí data, která jsou podrobnější než je četnost podrobnější částí ignorovány. <br/><br/>Například pokud <b>interval</b> je <b>každou hodinu</b> (frekvence: hour a interval je: (1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b>, pak bude <b>minuty a sekundy</b> části AnchorDateTime jsou ignorovány. |Ne |01/01/0001 |
| Posun |Interval TimeSpan, podle kterého se posune začátku a konce všechny řezy datové sady. <br/><br/><b>Poznámka:</b> Pokud nejsou zadány anchorDateTime a posun, výsledkem je kombinované shift. |Ne |Není k dispozici |

### <a name="offset-example"></a>Příklad posunutí
Ve výchozím nastavení, každý den (`"frequency": "Day", "interval": 1`) řezy začínají na čas UTC 00: 00 (půlnoc). Pokud chcete čas spuštění jako čas UTC 6: 00, nastavte posun, jak je znázorněno v následujícím fragmentu kódu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Příklad anchorDateTime
V následujícím příkladu je vytvořen datové sady jednou za 23 hodin. První řez začíná časový limit určený parametrem anchorDateTime, která je nastavena na `2017-04-19T08:00:00` (Světový čas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Posun a styl příklad
Následující datová sada je měsíční datová sada a je vytvořen na 3. v každém měsíci v 8:00:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Datové sady zásad
Datová sada může mít definované zásad ověřování, která určuje, jak můžete data generovaná spuštění řezu ověřit dřív, než bude připravený k použití. V takových případech po spuštění, řezu výstupní stav řezu se změní na **čekání** s substatus z **ověření**. Po ověření řezy stav řezu se změní na **připravené**. Pokud datový řez byl vytvořen, ale bylo neúspěšné ověřování, nebudou zpracovány spuštění aktivit pro podřízené kolekce obsahuje nějaké řezy, které jsou závislé na tomto intervalu. [Monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md) popisuje různé stavy datové řezy ve službě Data Factory.

**Zásady** oddíl v definici datové sady definuje kritéria nebo podmínky, které musí splnit řezy datové sady. Následující tabulka popisuje vlastnosti, které můžete použít v **zásady** části:

| Název zásady | Popis | Použít na | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Ověří, jestli data **objektů blob v Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob |Ne |Není k dispozici |
| minimumRows | Ověří, jestli data v **Azure SQL database** nebo **tabulek v Azure** obsahuje minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

#### <a name="examples"></a>Příklady
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Další informace o těchto vlastností a příkladů, najdete v části [vytvoření datových sad](data-factory-create-datasets.md) článku. 

## <a name="activity-policies"></a>Zásady aktivit
Zásady ovlivňují chování za běhu aktivity, konkrétně v případě, že zpracování řezu tabulku. Následující tabulka obsahuje podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet spuštění paralelní aktivity, které mohou probíhat na jiné kolekce obsahuje nějaké řezy. Například pokud aktivitu musí projít velké sady dostupných dat, mají větší hodnotu souběžnosti urychlí dat zpracovává. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datové řezy, které jsou zpracovávány.<br/><br/>Pokud máte 2 řezy (jeden situaci v 16: 00 a jiný v 17: 00) a oba jsou čeká na provedení. Pokud jste nastavili executionPriorityOrder bude NewestFirst, řez v 17: 00, je zpracován jako první. Podobně pokud nastavíte executionPriorityORder bude OldestFIrst, pak v 16: 00 zpracování řezu se. |
| retry |Integer<br/><br/>Maximální hodnota je 10 |0 |Počet opakování, než se zpracování dat pro řez je označen jako selhání. Spuštění aktivit pro datový řez je opakovat maximálně určený počet opakování. Opakování se provádí co nejdříve po selhání. |
| timeout |Časový interval |00:00:00 |Časový limit aktivity. Příklad: 00:10:00 (implikuje časový limit 10 minut)<br/><br/>Pokud hodnotu nezadáte, nebo je 0, je neomezený časový limit.<br/><br/>Pokud doba zpracování dat na určitý řez překročí hodnota časového limitu, bude zrušen a se systém pokusí o opakování zpracování. Počet opakovaných pokusů závisí na vlastnosti opakování. Pokud dojde k vypršení časového limitu, je stav nastaven na vypršel časový limit. |
| zpoždění |Časový interval |00:00:00 |Zadejte zpoždění před zpracování dat řezu.<br/><br/>Spuštění aktivit pro datový řez se spustí po zpoždění je za očekávanou dobu spuštění.<br/><br/>Příklad: 00:10:00 (implikuje prodlevě o délce 10 minut) |
| opakování po delší době |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet opakování po delší době pokusů, než se řez spuštění se nezdařilo.<br/><br/>pokusy o opakování po delší době jsou rozmístěné ve longRetryInterval. Proto pokud je třeba zadat dobu mezi opakovanými pokusy, použijte opakování po delší době. Pokud je určen jak opakování, longRetry, jednotlivé pokusy o opakování po delší době zahrnuje opakovaných pokusů a je maximální počet pokusů o opakování * opakování po delší době.<br/><br/>Například, pokud budeme mít následující nastavení v zásadách aktivit:<br/>Zkuste to znovu: 3<br/>opakování po delší době: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládá se jenom jeden řez ke spuštění (stav Čeká) a pokaždé, když se nezdaří spuštění aktivity. Zpočátku bude pokusy o 3 po sobě jdoucích provedení. Po každý pokus o stav řezu bude opakovat. Po první 3 pokusy přes stav řezu bude LongRetry.<br/><br/>Po hodině (to znamená, longRetryInteval hodnota) bude další sadu 3 pokusy po sobě jdoucích spuštění. Potom by se stav řezu a byste se pokusit žádné další pokusy. Proto celkové 6 byly provedeny pokusy.<br/><br/>Pokud žádné spuštění úspěšné, stav řezu bude připravená a nedochází k pokusům o žádné další pokusy.<br/><br/>opakování po delší době může použít v situacích, kdy závislá data přibývají Nedeterministický časy nebo celkové prostředí je v nestabilním stavu za zpracování dat dojde k. V takových případech to uděláte, opakované pokusy sebou nemusí pomáhá a tím po uplynutí čas výsledky v požadované výstupu.<br/><br/>Word upozornění: není nastavený pro opakování po delší době nebo longRetryInterval vysoké hodnoty. Obvykle vyšší hodnoty znamenají další systémové problémy. |
| longRetryInterval |Časový interval |00:00:00 |Prodleva mezi pokusy o opakování po delší době |

Další informace najdete v tématu [kanály](data-factory-create-pipelines.md) článku. 

## <a name="parallel-processing-of-data-slices"></a>Paralelní zpracování datové řezy
Počáteční datum pro kanál můžete nastavit v minulosti. Pokud tak učiníte, Data Factory automaticky vypočítá všechny datové řezy (back výplně) v minulosti a zahájí zpracování je. Příklad: Pokud vytvoříte kanál s počátečním datem datum 2017-04-01 a 2017-04-10 je aktuální datum. Je-li tempo výstupní datovou sadu denně, Data Factory spustí zpracování všech řezů z 2017-04-01 do 2017-04-09 okamžitě, protože počáteční datum je v minulosti. Řezu 2017-04-10 není zpracován ještě protože hodnota vlastnosti stylu v části Dostupnost je EndOfInterval ve výchozím nastavení. Nejstarší řez se zpracovává nejprve jako výchozí je hodnota executionPriorityOrder OldestFirst. Popis vlastnosti style, naleznete v tématu [dostupnosti datové sady](#dataset-availability) oddílu. Popis části executionPriorityOrder najdete v tématu [zásady aktivit](#activity-policies) oddílu. 

Vyplněný back datové řezy pro paralelní zpracování nastavení můžete nakonfigurovat **souběžnosti** vlastnost **zásady** části kódu JSON aktivity. Tato vlastnost určuje počet spuštění paralelní aktivity, které mohou probíhat na jiné kolekce obsahuje nějaké řezy. Výchozí hodnota pro vlastnost souběžnosti je 1. Proto jeden řez zpracovat v čase ve výchozím nastavení. Maximální hodnota je 10. Když kanál musí projít velké sady dostupných dat, mají větší hodnotu souběžnosti urychlí dat zpracovává. 

## <a name="rerun-a-failed-data-slice"></a>Opětovné spuštění neúspěšné datový řez
Když dojde k chybě při zpracování datový řez, můžete zjistit proč pomocí oken webu Azure portal nebo monitorování a Správa aplikace zpracování řezu se nezdařilo. Zobrazit [monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md) nebo [monitorování a Správa aplikací](data-factory-monitor-manage-app.md) podrobnosti.

Zvažte následující příklad, který ukazuje dvě aktivity. Aktivity "activity1" a aktivita 2. Aktivity "activity1" využívá určitý řez Dataset1 a vytváří určitý řez Dataset2, která je využívána "activity2" k vytvářela řez konečná datová sada jako vstup.

![Neúspěšné řezu](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram ukazuje, že mimo tři poslední řezy, došlo k chybě pro Dataset2 vytváření řez AM 9-10. Objekt pro vytváření dat automaticky sleduje závislosti pro datovou sadu řad čas. V důsledku toho se nespustí spuštění aktivit pro příjem dat řezu 9-10: 00.

Nástroje monitorování a správa data Factory umožňují přejít k podrobnostem diagnostické protokoly pro neúspěšné řez snadné najít původní příčinu problému a jeho řešení. Po opravě problému, můžete snadno začít aktivitu spustit pro vytvoření neúspěšné řez. Další informace o tom, jak spustit znovu a Beru na vědomí přechodů mezi stavy pro datové řezy, naleznete v tématu [monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md) nebo [monitorování a Správa aplikací](data-factory-monitor-manage-app.md).

Po opětovném spuštění výseč 9-10 AM **Dataset2**, Data Factory začíná spuštění řezu závislé 9-10 AM konečná datová sada.

![Opětovné spuštění neúspěšné řezu](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Kanál může obsahovat víc než jednu aktivitu. Pokud máte více aktivit v kanálu a výstup aktivity není vstup jiné aktivity, mohou se aktivity spouštět souběžně Pokud kolekce obsahuje nějaké řezy vstupní data pro aktivity je připravené.

Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Tyto aktivity mohou být ve stejném kanálu nebo v různých kanálech. Druhá aktivita spustí, pouze když první z nich dokončen úspěšně.

Představte si třeba následující případ, ve kterém má kanál dvě aktivity:

1. Aktivita A1, který vyžaduje externí vstupní datová sada D1 a vytváří výstupní datovou sadu D2.
2. Aktivita A2, který vyžaduje vstup z datové sady D2 a vytváří výstupní datovou sadu D3.

V tomto scénáři aktivity A1 a A2, byly ve stejném kanálu. Aktivita A1 spustí, když jsou k dispozici externí data a četnost naplánovaných dostupnosti je dosaženo. A2 spuštění aktivit při plánované řezy z D2 budou k dispozici a je dosaženo frekvence naplánovaná dostupnost. Pokud dojde k chybě v jednom z kolekce obsahuje nějaké řezy v datové sadě D2, nespustí se pro tuto řez A2, dokud nebude k dispozici.

Zobrazení diagramu se obě aktivity v kanálu stejné by vypadalo podobně jako v následujícím diagramu:

![Řetězení aktivit v kanálech stejné](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak už bylo zmíněno dříve, aktivity může být v různých kanálech. V takové situaci zobrazení diagramu vypadat podobně jako v následujícím diagramu:

![Řetězení aktivit v dva kanály](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Najdete v článku [kopírovat postupně](#copy-sequentially) v dodatku příklad části.

## <a name="model-datasets-with-different-frequencies"></a>Model datové sady s jinou frekvencí
Frekvence pro vstupní a výstupní datové sady a plán okna aktivity v ukázkách, byly stejné. Některé scénáře vyžadují možnost výstup s frekvencí jiný než frekvence jeden nebo více vstupů. Služba data Factory podporuje tyto scénáře modelování.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Příklad 1: Vytvoření sestavy denní výstup pro vstupní data, která je k dispozici každou hodinu
Představte si třeba situaci, ve kterém můžete mít vstupní měření data ze senzorů, které jsou k dispozici každou hodinu v úložišti objektů Blob v Azure. Chcete vytvořit denní agregované sestavy pomocí statistiky, jako je průměr, maximální a minimální za den s [aktivita hive služby Data Factory](data-factory-hive-activity.md).

Zde je, jak můžete modelovat se může tento scénář s Data Factory:

**Vstupní datová sada**

Hodinové vstupní soubory jsou vynechány ve složce pro daný den. Dostupnost pro vstup nastavený na **hodinu** (frekvence: Hodina, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Výstupní datová sada**

Jeden výstupní soubor se vytvoří každý den ve složce den. Dostupnost výstupu nastavený na **den** (frekvence: Den a intervalu: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivitu: aktivitu hive v kanálu**

Skript hive obdrží příslušné *data a času* informace jako parametry, které používají **WindowStart** proměnné, jak je znázorněno v následujícím fragmentu kódu. Skript hive používá tuto proměnnou k načtení dat z správnou složku dne a spusťte agregace generovat výstup.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Následující diagram znázorňuje scénář z hlediska datových závislostí.

![Data závislostí](./media/data-factory-scheduling-and-execution/data-dependency.png)

Výstupní řez pro každý den, závisí na 24 hodinových řezů ze vstupní datovou sadu. Tyto závislosti služby Data Factory automaticky vypočítá podle zjištění vstupní datové řezy, které patří do stejné období jako výstupní řez bude vytvořen. Pokud některý z 24 vstupní řezy není k dispozici, čeká vstupní řez bude připravené před spuštěním denní aktivity při spuštění služby Data Factory.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Příklad 2: Určení závislostí s výrazy a funkce Data Factory
Uvažujme jiný scénář. Předpokládejme, že máte aktivitu hivu, která zpracovává dva vstupní datové sady. Každý den jeden z nich má nová data, ale jeden z nich načte nová data, každý týden. Předpokládejme, že chcete provést spojení mezi dvěma vstupy a vygenerovat výstup každý den.

Jednoduchým přístupem, ve které Data Factory automaticky zjistí, vpravo vstupní řezy ke zpracování přizpůsobením do výstupu, který čas datového řezu období nebude fungovat.

Je nutné zadat, že pro každé spuštění aktivity objektu pro vytváření dat by měl použít minulý týden datový řez pro týdenní vstupní datové sady. Při použití Azure Data Factory funkcí jak je znázorněno v následujícím fragmentu kódu k implementaci tohoto chování.

**Vstup1: Objektů blob v Azure**

První vstup je na objekt blob Azure, denně aktualizují.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Vstup2: Objektů blob v Azure**

Vstup2 je objekt blob systému Azure se každý týden aktualizuje.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Výstup: Objektů blob v Azure**

Jeden výstupní soubor se vytvoří každý den ve složce daného dne. Dostupnost výstupu nastavena na **den** (frekvence: Den, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivitu: aktivitu hive v kanálu**

Aktivita hive přebírá dva vstupy a vytváří výstupní řez každý den. Můžete každý den výstupní řez závisí na vstupní řez předcházející týdnu pro týdenní vstup následujícím způsobem.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Zobrazit [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md) seznam funkce a systémové proměnné, které služba Data Factory podporuje.

## <a name="appendix"></a>Příloha

### <a name="example-copy-sequentially"></a>Příklad: Zkopírujte postupně
Je možné spustit více operací kopírování jeden po druhém sekvenční/seřazené způsobem. Například může mít dvě aktivity kopírování v kanálu (CopyActivity1 a CopyActivity2) s následující výstupní datové sady vstupních dat:   

CopyActivity1

Vstup: Datová sada. Výstup: Dataset2.

CopyActivity2

Vstup: Dataset2.  Výstup: Dataset3.

CopyActivity2 spustí jenom v případě, CopyActivity1 proběhla úspěšně a Dataset2 je k dispozici.

Tady je ukázka kódu JSON kanálu:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Všimněte si, že v tomto příkladu je výstupní datové sady první aktivity kopírování (Dataset2) zadán jako vstup druhé aktivity. Proto druhá aktivita spustí pouze v případě, že výstupní datovou sadu z první aktivity je připravený.  

V tomto příkladu CopyActivity2 může mít různé vstupu, například Dataset3, ale zadat Dataset2 jako vstup do CopyActivity2, takže aktivity se nespustí, dokud se nedokončí CopyActivity1. Příklad:

CopyActivity1

Vstup: Dataset1. Výstup: Dataset2.

CopyActivity2

Vstupy: Dataset3, Dataset2. Výstup: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Všimněte si, že v tomto příkladu jsou dva vstupní datové sady určené pro druhou aktivitu kopírování. Pokud jsou zadány více vstupů, pouze první vstupní datová sada se používá pro kopírování dat, ale jiné datové sady se používají jako závislosti. CopyActivity2 začnou, až po splnění následujících podmínek:

* CopyActivity1 byla úspěšně dokončena a Dataset2 je k dispozici. Tato datová sada se nepoužívá při kopírování dat do Dataset4. Funguje jen jako závislost plánování pro CopyActivity2.   
* Dataset3 je k dispozici. Tato datová sada představuje data, která se zkopíruje do cíle. 
