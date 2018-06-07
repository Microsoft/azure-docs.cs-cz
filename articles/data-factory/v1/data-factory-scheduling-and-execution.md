---
title: Plánování a provádění pomocí služby Data Factory | Microsoft Docs
description: Další aspekty plánování a provádění aplikačního modelu služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f18d6817d3a04ad787888ba058e1251303e575a7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622403"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory plánování a provádění
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [kanálu spouštěcí a aktivační události](../concepts-pipeline-execution-triggers.md) článku.

Tento článek vysvětluje aspekty plánování a spouštění aplikačního modelu služby Azure Data Factory. Tento článek předpokládá, že chápete základní informace o objektu pro vytváření dat aplikací modelu koncepty, včetně aktivit, kanálů, propojené služby a datové sady. Základní koncepty objektu pro vytváření dat Azure najdete v následujících článcích:

* [Úvod do služby Data Factory](data-factory-introduction.md)
* [Kanály](data-factory-create-pipelines.md)
* [Datové sady](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Počáteční a koncový čas kanálu
Kanál je aktivní jenom mezi jeho **spustit** čas a **end** čas. Nebude provedena před časem zahájení nebo po koncovém čase. Pokud kanálu pozastaví, nebude provedena bez ohledu na jeho počáteční a koncový čas. Pro kanál ke spuštění by neměl být pozastavena. Zjistíte, tato nastavení (zahájení, ukončení, pozastavena) v definici kanál: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Další informace najdete v těchto vlastností [vytvořit kanály](data-factory-create-pipelines.md) článku. 


## <a name="specify-schedule-for-an-activity"></a>Zadejte plán pro aktivitu
Není kanálu, který se spustí. Je aktivity v kanálu, které se provádějí v kontextu kanálu. Můžete zadat plán opakování pro aktivitu pomocí **Plánovač** část JSON aktivity. Například můžete naplánovat aktivity ke spuštění každou hodinu následujícím způsobem:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak je znázorněno v následujícím diagramu, zadat že plán pro aktivitu vytvoří řadu přeskakujícího windows se v kanálu spuštění a ukončení. Přeskakující windows jsou řadu pevné velikosti nepřekrývají, souvislý časové intervaly. Tyto logické přeskakující windows pro aktivitu se nazývají **aktivity windows**.

![Příklad aktivitu plánovače](media/data-factory-scheduling-and-execution/scheduler-example.png)

**Plánovač** vlastnost pro aktivitu je nepovinná. Pokud určíte tuto vlastnost, musí se shodovat cadence, které zadáte v definici výstupní datovou sadu aktivity. Výstupní datové sady v současné době řídí plán. Proto je nutné vytvořit datovou sadu výstupů i v případě, že aktivita nevytváří žádný výstup. 

## <a name="specify-schedule-for-a-dataset"></a>Zadejte plán pro datové sady
Aktivita v kanálu pro vytváření dat může trvat vstup nula nebo více **datové sady** a vytvoří výstupní datové sady. Pro aktivitu, můžete zadat cadence, kdy je k dispozici vstupních dat nebo výstupní data se vytvářejí pomocí **dostupnosti** část v definicích datovou sadu. 

**Frekvence** v **dostupnosti** část Určuje časovou jednotku. Frekvence povolené hodnoty jsou: minutu, hodinu, den, týden a měsíce. **Interval** vlastnost v části Dostupnost určuje multiplikátor pro četnost. Například: Pokud frekvence je nastavená na den a interval je nastaven na hodnotu 1 pro datovou sadu výstupů, výstupní data se vytvoří každý den. Pokud zadáte četnost jako minutu, doporučujeme nastavit interval na menší než 15. 

V následujícím příkladu vstupních dat je k dispozici každou hodinu a výstupní data se vytvářejí každou hodinu (`"frequency": "Hour", "interval": 1`). 

**Vstupní datové sady:** 

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


**Výstupní datové sady**

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

V současné době **výstupní datovou sadu disky plán**. Jinými slovy zadaný pro výstupní datovou sadu plán slouží ke spuštění aktivity za běhu. Proto je nutné vytvořit datovou sadu výstupů i v případě, že aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. 

V následující definici kanál **Plánovač** vlastnost se používá k určení plán aktivity. Tato vlastnost je nepovinná. Plán pro aktivitu v současné době musí odpovídat plánu, zadaný pro výstupní datovou sadu.
 
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

V tomto příkladu aktivity spouští každou hodinu mezi počáteční a koncový čas kanálu. Výstupní data se vytvářejí každou hodinu pro windows 3 hodiny (8: 00 - 9 AM, 9: 00 – 10: 00 a 10 AM - 11 AM). 

Je volána jednotlivých jednotek data využívat nebo vyprodukované aktivitu spustit **datový řez**. Následující diagram ukazuje příklad aktivitu jednu vstupní datovou sadu a jednu výstupní datovou sadu: 

![Dostupnost plánovače](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagram znázorňuje hodinové datové řezy vstupní a výstupní datové sady. Diagram zobrazuje tři vstupní řezy, které jsou připravené ke zpracování. Aktivita 10 11 AM probíhá, vytváření výstupní řez AM 10 11. 

Dostanete časový interval přidružené spuštění aktuálního řezu v datové sadě JSON pomocí proměnných: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) a [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobně můžete přistupovat časový interval, který je přidružený okně aktivita pomocí WindowStart a WindowEnd. Plán aktivity musí odpovídat plán výstupní datovou sadu aktivity. Proto SliceStart a SliceEnd hodnoty jsou stejné jako hodnoty WindowStart a WindowEnd v uvedeném pořadí. Další informace o těchto proměnných najdete v tématu [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md#data-factory-system-variables) články.  

Tyto proměnné můžete použít pro jiné účely vaše aktivity JSON. Například je můžete použít k výběru dat z vstupní a výstupní datové sady reprezentující data časové řady (například: 8: 00 do 9: 00). Tento příklad také používá **WindowStart** a **WindowEnd** vyberte relevantní data pro aktivitu spustit a zkopírujte jej do objektu blob s příslušnou **folderPath**. **FolderPath** Parametrizovaná mít samostatnou složku pro každou hodinu.  

V předchozím příkladu plán zadaný pro vstupní a výstupní datové sady je stejné (každou hodinu). Pokud vstupní datovou sadu aktivity je k dispozici na jinou frekvenci, například každých 15 minut, aktivity, která vytváří tento výstupní datovou sadu stále běží jednou za hodinu jako výstupní datovou sadu se řídí plán aktivity. Další informace najdete v tématu [modelu datové sady s jinou frekvencí](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Dostupnost datové sady a zásady
Jste viděli využití frekvence a intervalu vlastností v části dostupnosti definice datové sady. Existuje několik dalších vlastností, které mají vliv na plánování a provádění aktivity. 

### <a name="dataset-availability"></a>Datovou sadu dostupnosti 
Následující tabulka popisuje vlastnosti, které můžete použít v **dostupnosti** části:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkční řez datovou sadu.<br/><br/><b>Podporované frekvence</b>: minutu, hodinu, den, týden, měsíc |Ano |Není k dispozici |
| interval |Určuje multiplikátor pro četnost<br/><br/>"Frekvence x interval" Určuje, jak často se vytvářejí řez.<br/><br/>Pokud budete potřebovat datovou sadu, která se rozříznut hodinu, nastavíte <b>frekvence</b> k <b>hodinu</b>, a <b>interval</b> k <b>1</b>.<br/><br/><b>Poznámka:</b>: Pokud zadáte četnost jako minutu, doporučujeme nastavit interval na menší než 15 |Ano |Není k dispozici |
| Styl |Určuje, zda by měl být na zahájení a ukončení intervalu předložen řez.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Pokud je nastavena frekvence měsíc a styl je nastaven na EndOfInterval, řez vytváří poslední den v měsíci. Pokud je styl nastavené na StartOfInterval, řez vytváří první den v měsíci.<br/><br/>Pokud je nastavena frekvence den a styl je nastaven na EndOfInterval, řez se vytvářejí za poslední hodinu dne.<br/><br/>Pokud je nastavena frekvence hodinu a styl je nastaven na EndOfInterval, řez se vytvářejí na konci za hodinu. Například pro řez dobu 13: 00 – 14: 00, je řez vytvořeného ve 2. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase plánovačem slouží k výpočtu hranice řez datovou sadu. <br/><br/><b>Poznámka:</b>: Pokud AnchorDateTime má částí data, která jsou podrobnější než je četnost pak podrobnější části jsou ignorovány. <br/><br/>Například pokud <b>interval</b> je <b>každou hodinu</b> (frekvence: hodinu a intervalu: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b>, pak se <b>minuty a sekundy</b> částí AnchorDateTime jsou ignorovány. |Ne |01/01/0001 |
| Posun |Časový interval, ve kterém jsou zapuštěno počáteční a koncová všech řezech datovou sadu. <br/><br/><b>Poznámka:</b>: Pokud jsou zadané anchorDateTime i posun, výsledkem je kombinovaná shift. |Ne |Není k dispozici |

### <a name="offset-example"></a>Příklad posunutí
Ve výchozím nastavení, každý den (`"frequency": "Day", "interval": 1`) řezy spuštění na čas UTC 12: 00 (půlnoc). Pokud chcete, aby čas spuštění jako čas UTC 6: 00, nastavte posun, jak je znázorněno v následujícím fragmentu kódu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Příklad anchorDateTime
V následujícím příkladu se sada vytváří jednou za 23 hodin. První řez spustí v době určeného anchorDateTime, který je nastaven na `2017-04-19T08:00:00` (Světový čas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Posun nebo styl příklad
Tyto datové sady je měsíční datová sada a vytváří 3rd v každém měsíci v 8:00 AM (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Datovou sadu zásad
Datovou sadu, může mít definované zásady ověřování, která určuje, jak může být ověřen data generována řez provádění předtím, než je připraven ke spotřebování. V takových případech po dokončení provádění řez výstupní stav řezu se změní na **čekání** s substatus z **ověření**. Po ověření řezy jsou stav řezu se změní na **připraven**. Pokud datový řez pochází, ale nebyla úspěšná ověření, nebudou zpracovány spuštění aktivity pro příjem dat datové řezy, které závisí na tento řez. [Monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md) popisuje různé stavy datové řezy ve službě Data Factory.

**Zásad** oddíl v definici datové sady definuje kritéria nebo podmínku, musíte splnit řezy datovou sadu. Následující tabulka popisuje vlastnosti, které můžete použít v **zásad** části:

| Název zásady | Popis | Použít | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Ověří, jestli data v **objektů blob v Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob |Ne |Není k dispozici |
| minimumRows | Ověří, jestli data v **Azure SQL database** nebo **tabulky Azure** obsahuje minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

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

Další informace o těchto vlastnostech a příklady naleznete v tématu [vytvoření datových sad](data-factory-create-datasets.md) článku. 

## <a name="activity-policies"></a>Zásady aktivit
Zásady ovlivňují chování běhu aktivity, konkrétně při zpracování řezu tabulky. Následující tabulka obsahuje podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| Souběžnosti |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet spuštění paralelní aktivity, které se může stát při jiné řezy. Například pokud aktivitu musí projít, velké sady dostupných dat, mají větší hodnotu souběžnosti urychluje zpracování dat. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datové řezy, které jsou zpracovávány.<br/><br/>Pokud máte 2 řezy (jeden situaci ve 4 a další v 17: 00) a jsou obě čekající na zpracování. Pokud jste nastavili executionPriorityOrder být NewestFirst, je nejprve zpracování řezu v 17: 00. Podobně pokud nastavíte executionPriorityORder být OldestFIrst, pak ve 4 zpracování řezu se. |
| retry |Integer<br/><br/>Maximální hodnota může být 10 |0 |Počet opakování, než se zpracování dat pro řez je označen jako selhání. Provedení aktivity pro datový řez je opakovat až zadaný počet. Opakovaném provádí co nejdříve po selhání. |
| timeout |Časový interval |00:00:00 |Časový limit aktivity. Příklad: 00:10:00 (znamená časový limit 10 minut)<br/><br/>Pokud hodnota není zadána nebo je 0, časový limit je nekonečno.<br/><br/>Pokud bude čas zpracování dat na řez překročí hodnota časového limitu, se zruší a systém se pokusí opakujte zpracování. Počet pokusů, závisí na vlastnost opakování. Když dojde k vypršení časového limitu, je stav nastaven na TimedOut. |
| Zpoždění |Časový interval |00:00:00 |Zadejte zpoždění před zpracování dat řezu spustí.<br/><br/>Provádění aktivity pro datový řez se spustí po zpoždění očekávaný čas spuštění.<br/><br/>Příklad: 00:10:00 (znamená zpoždění 10 minut) |
| opakování po delší době |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet dlouho opakování pokusů, než řez spuštění se nezdařilo.<br/><br/>pokusy o opakování po delší době jsou rozmístěny ve longRetryInterval. Takže pokud je třeba zadat čas mezi pokusy o opakování, použijte opakování po delší době. Pokud jsou zadané opakování a opakování po delší době, jednotlivé pokusy o opakování po delší době zahrnuje opakovaných pokusů a je maximální počet pokusů o opakování * opakování po delší době.<br/><br/>Například, pokud bychom měli následující nastavení v zásadách aktivit:<br/>Opakujte: 3<br/>opakování po delší době: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládá se jenom jeden řez provést (stav Čeká) a provedení aktivity pokaždé, když dojde k chybě. Nejdřív by 3 provádění po sobě jdoucích pokusů. Po každém pokusu o stav řezu bude opakovat. Po první 3 pokusy jsou přes, bude stav řezu opakování po delší době.<br/><br/>Po hodině (který je na longRetryInteval hodnota) bude další sadu 3 provádění po sobě jdoucích pokusů. Poté stav řezu by se nezdařilo a by se pokus o žádné další opakování. Proto celkové 6 pokusy byly provedeny.<br/><br/>Pokud žádné spuštění úspěšné, stav řezu by mít připravené a jsou pokus o žádné další opakování.<br/><br/>opakování po delší době je možné použít situace, kdy závislé data dorazí na Nedeterministický časy nebo je v nestabilním stavu v rámci které zpracování dat dojde celém prostředí. V takových případech to, které opakování, jedna po druhé nemusí být úspěšná a díky tomu v intervalech čas má za následek požadované výstup.<br/><br/>Word varování: nenastavujte vysoké hodnoty pro opakování po delší době nebo longRetryInterval. Vyšší hodnoty obvykle implikují dalších systémových otázek. |
| longRetryInterval |Časový interval |00:00:00 |Prodleva mezi pokusy o opakování dlouho |

Další informace najdete v tématu [kanály](data-factory-create-pipelines.md) článku. 

## <a name="parallel-processing-of-data-slices"></a>Paralelní zpracování datové řezy
Počáteční datum pro kanál můžete nastavit v minulosti. Pokud tak učiníte, Data Factory automaticky vypočítá všechny datové řezy (back výplněmi) v minulosti a zahájí zpracování je. Například: Pokud vytvoření kanálu s počátečním datem 2017-04-01 a aktuální datum následuje 2017-04-10. Pokud cadence výstupní datové sady je denně, Data Factory spustí zpracování všech řezech z 2017-04-01 do 2017-04-09 okamžitě, protože je počáteční datum v minulosti. Z 2017-04-10 není zpracování řezu ještě protože hodnota vlastnosti stylu v části dostupnosti je EndOfInterval ve výchozím nastavení. Zpracování nejstarší řezu se nejprve jako výchozí hodnota executionPriorityOrder je OldestFirst. Popis vlastnost stylu naleznete v tématu [datovou sadu dostupnosti](#dataset-availability) části. Popis části executionPriorityOrder najdete v tématu [zásady aktivit](#activity-policies) části. 

Můžete nakonfigurovat, zpět vyplněno datové řezy, které mají být zpracovány současně nastavením **souběžnosti** vlastnost v **zásad** části kódu JSON aktivity. Tato vlastnost určuje počet spuštění paralelní aktivity, které se může stát při jiné řezy. Výchozí hodnota pro vlastnost souběžnosti je 1. Proto jeden zpracování řezu se současně ve výchozím nastavení. Maximální hodnota je 10. Když kanál musí projít velké sady dostupných dat, mají větší hodnotu souběžnosti urychluje zpracování dat. 

## <a name="rerun-a-failed-data-slice"></a>Opětovné spuštění neúspěšné datový řez
Když dojde k chybě při zpracování dat řezu, můžete zjistit proč zpracování řezu se nezdařilo pomocí oken webu Azure portal nebo monitorování a správě aplikace. V tématu [monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md) nebo [monitorování a správu aplikace](data-factory-monitor-manage-app.md) podrobnosti.

Prohlédněte si následující příklad ukazuje dvě aktivity. Aktivity "activity1" a aktivitu 2. Aktivity "activity1" využívá řez Dataset1 a produkuje řez Dataset2, který využívá jako vstup "activity2" k vytvoření řez konečné datové sady.

![Řez se nezdařilo](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram ukazuje, že mimo tři poslední řezů, došlo k chybě, která generovala řez AM 9 10 pro Dataset2. Objekt pro vytváření dat automaticky sleduje závislostí pro datovou sadu časové řady. V důsledku toho se nespustí aktivity při spuštění pro příjem dat řez 9 – 10: 00.

Datový objekt pro vytváření monitorování a nástroje pro správu umožňují rozbalit diagnostické protokoly pro selhání řez snadno najít hlavní příčinu problému a opravte ho. Po opravení problému můžete snadno začít aktivity při spuštění k vytvoření se nezdařilo řez. Další informace o tom, jak znovu spustit a pochopit přechodů mezi stavy pro datové řezy najdete v tématu [monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md) nebo [monitorování a správu aplikace](data-factory-monitor-manage-app.md).

Po spustit řez AM 9 10 pro znovu **Dataset2**, Data Factory spustí spustit pro řez závislé 9 10 AM na poslední datovou sadu.

![Opětovné spuštění neúspěšné řez](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Kanál může obsahovat víc než jednu aktivitu. Pokud máte více aktivit v kanálu a výstup aktivity není vstup jinou aktivitu, může paralelně spustit aktivity, pokud připravení řezy vstupní data pro aktivity.

Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Aktivity může být v kanálu stejné nebo jiné kanály. Druhá aktivita se spustí, pouze pokud první skončí úspěšně.

Zvažte například následující případ, kdy kanálu má dvě aktivity:

1. A1 aktivity, která vyžaduje externí vstupní datové sady D1 a vytvoří výstupní datovou sadu D2.
2. A2 aktivity, který vyžaduje vstup z datové sady D2 a vytvoří výstupní datovou sadu D3.

V tomto scénáři aktivity A1 a A2, byly ve stejné kanálu. Aktivity A1 spustí, když externích dat je k dispozici a je dosaženo frekvence naplánovaná dostupnost. Aktivity A2 spustí naplánované řezy z D2 k dispozici a je dosaženo frekvence naplánovaná dostupnost. Pokud dojde k chybě v jednom z řezy v datové sadě D2, nespustí se pro tento řez A2, dokud nebude k dispozici.

Zobrazení diagramu s obou aktivity v kanálu stejné by vypadat podobně jako v následujícím diagramu:

![Řetězení aktivity v kanálu stejné](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak už bylo zmíněno dříve, aktivity může být v jiné kanály. V takové situaci zobrazení diagramu bude vypadat následující diagram:

![Řetězení aktivity ve dvou kanálů](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Najdete v článku [zkopírujte postupně](#copy-sequentially) část v příloze příklad.

## <a name="model-datasets-with-different-frequencies"></a>Datové sady modelu s jinou frekvence
Frekvence pro vstupní a výstupní datové sady a okno plánu aktivity v ukázky, byly stejné. Některé scénáře vyžadují možnost vytvoření výstupu frekvencí jiný než frekvence jeden nebo více vstupů. Objekt pro vytváření dat podporuje tyto scénáře modelování.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Příklad 1: Vytvoření sestavy denní výstup pro vstupní data, která je k dispozici každou hodinu
Vezměte v úvahu scénář, ve kterém můžete mít vstupní měření data ze senzorů, které jsou k dispozici každou hodinu do úložiště objektů Blob v Azure. Chcete vytvořit sestavu denní agregační s statistické údaje, třeba střední, maximální a minimální den s [Data Factory hive aktivity](data-factory-hive-activity.md).

Zde je, jak můžete model tento scénář se objekt pro vytváření dat:

**Vstupní datové sady**

Hodinové vstupní soubory jsou vyřadit ve složce pro daný den. Dostupnost pro vstup je nastavený na **hodinu** (frekvence: hodiny, interval: 1).

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
**Výstupní datové sady**

Jedna výstupní soubor se vytvoří každý den ve složce den. Dostupnost výstupu je nastavený na **den** (frekvence: den a interval: 1).

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

**Aktivity: aktivitu v kanálu hivu**

Skriptu hive obdrží odpovídající *data a času* informace jako parametry, které používají **WindowStart** proměnné, jak je znázorněno v následujícím fragmentu kódu. Skriptu hive používá tuto proměnnou k načítání dat z správnou složku dne a spusťte agregace generovat výstup.

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

Následující diagram znázorňuje scénář z hlediska data závislostí.

![Data závislostí](./media/data-factory-scheduling-and-execution/data-dependency.png)

Výstupní řez pro každý den, závisí na 24 řezů hodinové ze vstupní datové sady. Tyto závislosti služby Data Factory automaticky vypočítá po zjištění vstupních dat datové řezy, které patří do stejné období jako výstupní řez se vytváří. Pokud některé z 24 vstupní řezy není k dispozici, čeká na objekt pro vytváření dat vstupní řez bude připravená před zahájením denní aktivity při spuštění.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Příklad 2: Zadejte závislostí s výrazy a funkce pro vytváření dat
Pojďme se jiný scénář. Předpokládejme, že máte aktivitu hive, který zpracovává dvě vstupní datové sady. Jeden z nich má nová data denně, ale jeden z nich získá nová data každý týden. Předpokládejme, že chcete provést spojení mezi dvěma vstupy a vytváření výstupu každý den.

Jednoduchý přístup, ve které Data Factory automaticky obrázků na právo vstupní řezy ke zpracování zarovnání na výstup, který datový řez časové období není funkční.

Je nutné zadat, že pro každé aktivity při spuštění, objektu pro vytváření dat mají používat minulého týdne datový řez pro týdenní vstupní datové sady. Při použití Azure Data Factory funkcí jak je znázorněno v následujícím fragmentu kódu k implementaci tohoto chování.

**Input1: Azure blob**

První vstup je Azure blob, denně aktualizují.

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

**Input2: Azure blob**

Input2 je objekt blob systému Azure se aktualizovaný týdně.

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

**Výstup: Azure blob**

Jedna výstupní soubor se vytvoří každý den ve složce dne. Dostupnost výstupu nastavený na **den** (frekvence: den, interval: 1).

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

**Aktivity: aktivitu v kanálu hivu**

Aktivita hive přebírá dva vstupy a produkuje řez výstupních každý den. Zadávat lze každý den výstupní řez závislý na předchozí týden vstupní řez pro týdenní vstup následujícím způsobem.

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

V tématu [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md) seznam funkce a systémové proměnné, které podporuje služby Data Factory.

## <a name="appendix"></a>Příloha

### <a name="example-copy-sequentially"></a>Příklad: kopírování postupně
Je možné spustit více operací kopírování jedna po druhé způsobem sekvenční/řazení. Například můžete mít dvě kopie aktivity v kanálu (CopyActivity1 a CopyActivity2) s následující výstupní datové sady vstupních dat:   

CopyActivity1

Vstup: datové sady. Výstup: Dataset2.

CopyActivity2

Vstup: Dataset2.  Výstup: Dataset3.

CopyActivity2 spustí jenom v případě, že CopyActivity1 proběhla úspěšně a Dataset2 je k dispozici.

Tady je ukázkový kanál JSON:

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

Všimněte si, že v příkladu je výstupní datovou sadu první aktivitu kopírování (Dataset2) zadané jako vstup pro druhý aktivity. Proto druhá aktivita spustí pouze v případě, že výstupní datové sady z první aktivitu je připraven.  

V příkladu CopyActivity2 může mít různé vstupu, například Dataset3, ale zadat Dataset2 jako vstup CopyActivity2, takže aktivity nespustí, dokud nebude dokončeno CopyActivity1. Příklad:

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

Všimněte si, že v příkladu jsou dva vstupní datové sady zadané pro druhý aktivitu kopírování. Jsou-li více vstupů, pouze první vstupní datovou sadu se používá ke kopírování dat, ale jiné datové sady se používají jako závislosti. CopyActivity2 by spustit až po splnění následujících podmínek:

* CopyActivity1 byla úspěšně dokončena a Dataset2 je k dispozici. Tato datová sada se nepoužívá při kopírování dat do Dataset4. Pouze funguje jako plánování závislost pro CopyActivity2.   
* Dataset3 je k dispozici. Tuto datovou sadu představuje data, která se zkopírují do cílového umístění. 
