---
title: Plánování a spouštění pomocí Data Factory
description: Naučte se aspekty plánování a spouštění Azure Data Factory aplikačního modelu.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f613a14f84999fced888ae72de11328bc7c90811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780000"
---
# <a name="data-factory-scheduling-and-execution"></a>Plánování a provádění Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si článek [spuštění kanálu a triggery](../concepts-pipeline-execution-triggers.md) .

Tento článek vysvětluje aspekty plánování a spouštění aplikačního modelu služby Azure Data Factory. V tomto článku se předpokládá, že rozumíte základům Data Factory konceptů aplikačního modelu, včetně aktivit, kanálů, propojených služeb a datových sad. Základní koncepty Azure Data Factory najdete v následujících článcích:

* [Úvodní informace k Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Datové sady](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Počáteční a koncové časy kanálu
Kanál je aktivní jenom mezi časem **spuštění** a časem **ukončení** . Není prováděna před časem spuštění nebo po ukončení. Pokud je kanál pozastaven, neprovede se bez ohledu na jeho počáteční a koncový čas. Pro spuštění kanálu by neměl být pozastaven. Tato nastavení najdete v definici kanálu (spustit, ukončit, pozastaveno): 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Další informace o těchto vlastnostech najdete v článku o [vytvoření kanálů](data-factory-create-pipelines.md) . 


## <a name="specify-schedule-for-an-activity"></a>Zadat plán aktivity
Nejedná se o spuštěný kanál. Aktivity v kanálu se spouštějí v celkovém kontextu kanálu. Můžete zadat plán opakování pro aktivitu pomocí oddílu **Scheduler** aktivity JSON Activity. Například můžete naplánovat, aby aktivita běžela každou hodinu, a to takto:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak je znázorněno v následujícím diagramu, zadáním plánu pro aktivitu se vytvoří řada bubnových oken s časem zahájení a ukončení kanálu. Bubnová okna jsou řada překrývajících se souvislých časových intervalů s pevnou velikostí. Toto logické bubnové okna pro aktivitu se nazývají **okna aktivit**.

![Příklad plánovače aktivit](media/data-factory-scheduling-and-execution/scheduler-example.png)

Vlastnost **Scheduler** pro aktivitu je volitelná. Pokud tuto vlastnost zadáte, musí odpovídat tempo, které zadáte v definici výstupní datové sady pro aktivitu. Výstupní datové sady v současné době řídí plán. Proto je nutné vytvořit výstupní datovou sadu i v případě, že aktivita nevytváří žádný výstup. 

## <a name="specify-schedule-for-a-dataset"></a>Určení plánu pro datovou sadu
Aktivita v kanálu Data Factory může mít nula nebo více vstupních **datových sad** a vytvoří jednu nebo více výstupních datových sad. V případě aktivity můžete zadat tempo, ve kterém jsou k dispozici vstupní data, nebo se výstupní data vytvoří pomocí oddílu **dostupnosti** v definicích datových sad. 

**Frekvence** v části **dostupnost** Určuje časovou jednotku. Povolené hodnoty četnosti jsou: minuty, hodina, den, týden a měsíc. Vlastnost **interval** v oddílu Availability určuje násobitel pro frekvenci. Příklad: Pokud je frekvence nastavená na den a interval je nastaven na hodnotu 1 pro výstupní datovou sadu, budou výstupní data vyprodukována každý den. Pokud zadáte frekvenci jako minutu, doporučujeme nastavit interval na ne méně než 15. 

V následujícím příkladu jsou vstupní data dostupná každou hodinu a výstupní data se vytvářejí každou hodinu ( `"frequency": "Hour", "interval": 1` ). 

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

V současné době **výstupní datová sada zařídí plán**. Jinými slovy, plán zadaný pro výstupní datovou sadu se používá ke spuštění aktivity za běhu. Proto je nutné vytvořit výstupní datovou sadu i v případě, že aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. 

V následující definici kanálu se k určení plánu aktivity používá vlastnost **Scheduleru** . Tato vlastnost je nepovinná. V současné době musí být plán aktivity shodný s plánem zadaným pro výstupní datovou sadu.
 
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

V tomto příkladu se aktivita spouští každou hodinu mezi začátkem a koncovým časem kanálu. Výstupní data se vytvářejí po hodinách po dobu tří hodin (2,8-9 DOP, 9 dop. a od 2:00 do 11 am). 

Každá jednotka dat spotřebovaná nebo vytvořená spuštěním aktivity se nazývá **datový řez**. Následující diagram znázorňuje příklad aktivity s jednou vstupní datovou sadou a jednou výstupní datovou sadou: 

![Plánovač dostupnosti](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagram zobrazuje hodinové datové řezy pro vstupní a výstupní datovou sadu. Diagram znázorňuje tři vstupní řezy, které jsou připravené ke zpracování. Aktivita 10-11 AM probíhá a produkuje výstupní řez 10-11. 

K časovému intervalu přidruženému k aktuálnímu řezu v datové sadě JSON můžete přistupovat pomocí proměnných: [vlastnosti slicestart](data-factory-functions-variables.md#data-factory-system-variables) a [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobně můžete získat přístup k časovému intervalu přidruženému k oknu aktivity pomocí WindowStart a WindowEnd. Plán aktivity musí odpovídat plánu výstupní datové sady pro aktivitu. Hodnoty vlastnosti slicestart a SliceEnd se proto shodují s hodnotami WindowStart a WindowEnd. Další informace o těchto proměnných naleznete v článcích [Data Factory functions a System Variables](data-factory-functions-variables.md#data-factory-system-variables) .  

Tyto proměnné můžete použít pro různé účely ve formátu JSON aktivity. Můžete je například použít k výběru dat ze vstupních a výstupních datových sad představujících data časových řad (například 8 AM až 9 dop.). Tento příklad také používá **WindowStart** a **WindowEnd** k výběru relevantních dat pro spuštění aktivity a jejich zkopírování do objektu BLOB s odpovídajícím **FolderPath**. **FolderPath** je parametrizovaný tak, aby měl samostatné složky pro každou hodinu.  

V předchozím příkladu je plán určený pro vstupní a výstupní datové sady stejný (každou hodinu). Pokud je vstupní datová sada pro aktivitu k dispozici v jiné frekvenci, například každých 15 minut, aktivita vytvářející tuto výstupní datovou sadu se pořád spustí jednou za hodinu jako výstupní datová sada, která řídí plán aktivity. Další informace najdete v tématu [model DataSets s různou frekvencí](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Dostupnost a zásady datové sady
V části dostupnost definice datové sady jste viděli použití vlastností četnost a interval. Existuje několik dalších vlastností, které mají vliv na plánování a provádění aktivity. 

### <a name="dataset-availability"></a>Dostupnost datové sady 
Následující tabulka obsahuje popis vlastností, které můžete použít v části **dostupnost** :

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkci řezu datové sady.<br/><br/><b>Podporovaná frekvence</b>: minuta, hodina, den, týden, měsíc |Yes |NA |
| interval |Určuje násobitel pro frekvenci.<br/><br/>Frekvence x interval určuje, jak často se řez vytvoří.<br/><br/>Pokud potřebujete datovou sadu rozdělit na každou hodinu, nastavte <b>četnost</b> na <b>hodinu</b>a <b>interval</b> na <b>1</b>.<br/><br/><b>Poznámka</b>: Pokud zadáte frekvenci jako minutu, doporučujeme nastavit interval na ne méně než 15. |Yes |NA |
| style |Určuje, zda má být řez vytvořen na začátku nebo konci intervalu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Pokud je frekvence nastavená na month (měsíc) a Style je nastavená na EndOfInterval, řez se vytvoří během posledního dne v měsíci. Pokud je styl nastaven na StartOfInterval, řez se vytvoří první den v měsíci.<br/><br/>Pokud je frekvence nastavená na den a styl je nastavený na EndOfInterval, řez se vytvoří za poslední hodinu dne.<br/><br/>Pokud je frekvence nastavená na Hour (hodina) a Style je nastavená na EndOfInterval, řez se vytvoří na konci hodiny. Například pro řez v období 1 PM – 2 se vytvoří řez na 2 ODP. |No |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase využívané schedulerem k výpočtu hranic řezu datové sady. <br/><br/><b>Poznámka</b>: Pokud AnchorDateTime obsahuje části s daty, které jsou lépe podrobnější než frekvence, budou podrobnější části ignorovány. <br/><br/>Například pokud je <b>interval</b> <b>každou hodinu</b> (četnost: hodina a interval: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b>, budou části <b>minut a sekund</b> AnchorDateTime ignorovány. |No |01/01/0001 |
| posun |Časový interval, podle kterého se posune začátek a konec všech řezů datové sady <br/><br/><b>Poznámka</b>: Pokud jsou zadány oba anchorDateTime i offset, výsledkem je kombinovaný posun. |No |NA |

### <a name="offset-example"></a>Příklad posunutí
Ve výchozím nastavení se pro každý den ( `"frequency": "Day", "interval": 1` ) řezy začnou začínat časem UTC (půlnoc). Pokud chcete, aby byl čas zahájení nastavený na hodnotu 6 času UTC, nastavte posun, jak je znázorněno v následujícím fragmentu kódu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Příklad anchorDateTime
V následujícím příkladu se datová sada vytvoří jednou za 23 hodin. První řez začíná v čase určeném parametrem anchorDateTime, který je nastaven na hodnotu `2017-04-19T08:00:00` (čas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Příklad posunutí/stylu
Následující datová sada je měsíční datová sada a je vytvořena na 3. v každém měsíci v 8:00 `3.08:00:00` . ():

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Zásada datové sady
Datová sada může mít definované zásady ověřování, které určují, jak se dají ověřit data generovaná při spuštění v rámci řezu předtím, než bude připravená na spotřebu. V takových případech se po dokončení řezu stav výstupního řezu změní na **čekání** s podstavem **ověřování**. Po ověření řezů se stav řezu změní na **připraveno**. Pokud byl vytvořen datový řez, ale ověřování neprošlo, spuštění aktivit pro podřízené řezy, které jsou závislé na tomto řezu, se nezpracují. [Monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md) pokrývá různé stavy datových řezů v Data Factory.

Oddíl **Policy** v definici datové sady definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat. Následující tabulka obsahuje popis vlastností, které můžete použít v části **Policy (zásady** ):

| Název zásady | Popis | Použito pro | Vyžadováno | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Ověří, jestli data v **objektu blob Azure** splňují požadavky na minimální velikost (v megabajtech). |Azure Blob |No |NA |
| minimumRows | Ověří, jestli data v **Azure SQL Database** nebo **tabulce Azure** obsahují minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |No |NA |

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

Další informace o těchto vlastnostech a příkladech najdete v článku o [vytvoření datových sad](data-factory-create-datasets.md) . 

## <a name="activity-policies"></a>Zásady aktivit
Zásady ovlivňují chování aktivity za běhu, konkrétně při zpracování řezu tabulky. Podrobnosti jsou uvedeny v následující tabulce.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Description |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet paralelních spuštění aktivit, ke kterým může dojít v různých řezech. Například pokud aktivita potřebuje projít velkou sadou dostupných dat, větší hodnota souběžnosti zrychluje zpracování dat. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datových řezů, které jsou zpracovávány.<br/><br/>Například pokud máte 2 řezy (jedna se děje na 16:00 a druhý na 17:00), a obě jsou vyřízeny. Pokud nastavíte executionPriorityOrder na NewestFirst, řez se nejprve zpracuje v 5 odp. Podobně pokud nastavíte executionPriorityORder na OldestFIrst, bude zpracován řez ve 4 PM. |
| retry |Integer<br/><br/>Maximální hodnota může být 10. |0 |Počet opakovaných pokusů, než se zpracování dat pro řez označí jako selhání. Pokus o spuštění aktivity pro datový řez se opakuje do zadaného počtu opakování. Opakování se provede co nejdříve po selhání. |
| timeout |TimeSpan |00:00:00 |Časový limit aktivity Příklad: 00:10:00 (implikuje časový limit 10 minut)<br/><br/>Pokud hodnota není zadaná nebo je 0, časový limit je nekonečný.<br/><br/>Pokud doba zpracování dat v řezu překročí hodnotu časového limitu, je zrušena a systém se pokusí o opakované zpracování. Počet opakování závisí na vlastnosti opakování. Pokud dojde k vypršení časového limitu, je stav nastavený na vypršel časový limit. |
| způsobené |TimeSpan |00:00:00 |Určete zpoždění před spuštěním zpracování dat řezu.<br/><br/>Spuštění aktivity pro datový řez se spustí, jakmile je zpoždění po očekávanou dobu provádění.<br/><br/>Příklad: 00:10:00 (implikuje zpoždění po 10 minutách) |
| Má |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet dlouhých pokusů o opakování před neúspěšným provedením řezu<br/><br/>Má pokusy jsou rozmístěny pomocí longRetryInterval. Takže pokud potřebujete zadat čas mezi opakovanými pokusy, použijte má. Pokud jsou zadány parametry Retry a má, každý má pokus obsahuje pokusy o opakování a maximální počet pokusů o opakování * má.<br/><br/>Například pokud máme v zásadách aktivity následující nastavení:<br/>Opakovat: 3<br/>Má: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládejme, že je možné provést pouze jeden řez (stav čeká na vyřízení) a spuštění aktivity pokaždé neproběhne úspěšně. Zpočátku by se mělo 3 po sobě jdoucích pokusů o spuštění. Po každém pokusu se stav řezu opakuje. Po prvním 3 pokusech se bude stav řezu má.<br/><br/>Po hodinu (tj. hodnotě longRetryInteval) by existovala další sada 3 po sobě jdoucích pokusů o spuštění. Po této operaci se stav řezu nezdařil a nebude proveden pokus o další opakování. Proto byly provedeny celkem 6 pokusů.<br/><br/>Pokud je jakékoli spuštění úspěšné, bude stav řezu připravený a nebude proveden pokus o další opakované pokusy.<br/><br/>Má se můžou použít v situacích, kdy se závislá data dostanou v nedeterministických časech, nebo v celkovém prostředí je vločka, pod kterou se zpracování dat děje. V takových případech s tím, že se opakuje jeden po jiné, to nemusí pomáhat a provádět po uplynutí časového intervalu požadovaného výstupu.<br/><br/>Upozornění: nenastavujte vysoké hodnoty pro má nebo longRetryInterval. Vyšší hodnoty obvykle implikují jiné systémové problémy. |
| longRetryInterval |TimeSpan |00:00:00 |Prodleva mezi dlouhými pokusy o opakování |

Další informace najdete v článku [kanály](data-factory-create-pipelines.md) . 

## <a name="parallel-processing-of-data-slices"></a>Paralelní zpracování datových řezů
Můžete nastavit počáteční datum pro kanál v minulosti. Když to uděláte, Data Factory automaticky vypočítá (vrátí zpět) všechny datové řezy v minulosti a začne je zpracovávat. Příklad: Pokud vytvoříte kanál s počátečním datem 2017-04-01 a aktuální datum je 2017-04-10. Pokud je tempo výstupní datové sady denně, Data Factory spustí zpracování všech řezů od 2017-04-01 do 2017-04-09, protože počáteční datum je v minulosti. Řez z 2017-04-10 se ještě nezpracovává, protože hodnota vlastnosti Style v oddílu Availability je EndOfInterval ve výchozím nastavení. Nejdříve se zpracuje nejstarší řez, protože výchozí hodnota executionPriorityOrder je OldestFirst. Popis vlastnosti Style najdete v části věnované [dostupnosti datové sady](#dataset-availability) . Popis části executionPriorityOrder najdete v části věnované [zásadám aktivit](#activity-policies) . 

Můžete nakonfigurovat zpětně naplněné datové řezy, které mají být zpracovány paralelně, nastavením vlastnosti **souběžnosti** v části **zásady** JSON aktivity. Tato vlastnost určuje počet spuštění paralelní aktivity, ke kterým může dojít v různých řezech. Výchozí hodnota pro vlastnost Concurrency je 1. Proto ve výchozím nastavení je jeden řez zpracováván současně. Maximální hodnota je 10. Když kanál potřebuje projít velkou sadou dostupných dat, větší hodnota souběžnosti zrychluje zpracování dat. 

## <a name="rerun-a-failed-data-slice"></a>Opětovné spuštění neúspěšného datového řezu
Když při zpracování datového řezu dojde k chybě, můžete zjistit, proč se zpracování řezu nepovedlo pomocí Azure Portalových oken nebo monitorovat a spravovat aplikace. Podrobnosti najdete v tématu [monitorování a Správa kanálů pomocí Azure Portalových](data-factory-monitor-manage-pipelines.md) oken nebo [aplikací pro monitorování a správu](data-factory-monitor-manage-app.md) .

Vezměte v úvahu následující příklad, který ukazuje dvě aktivity. Activity1 a aktivita 2. Activity1 spotřebovává řez z DataSet1. a vytvoří řez Dataset2, který se spotřebuje jako vstup pomocí "Activity2" pro vytvoření řezu finální datové sady.

![Neúspěšný řez](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram ukazuje, že se třemi posledními řezy nedošlo k selhání při vytváření výseče 9-10 pro Dataset2. Data Factory automaticky sleduje závislost pro datovou sadu časových řad. V důsledku toho nespustí spuštění aktivit pro navazující průřez 9-10.

Data Factory nástroje pro monitorování a správu umožňují přejít k podrobnostem o diagnostických protokolech pro neúspěšnou výseč, abyste mohli snadno najít hlavní příčinu problému a opravit ho. Po vyřešení problému můžete snadno spustit běh aktivity a vytvořit tak neúspěšný řez. Další informace o tom, jak znovu spustit a pochopit přechody stavu pro datové řezy, najdete v tématu [monitorování a Správa kanálů pomocí Azure Portalch](data-factory-monitor-manage-pipelines.md) oken nebo [aplikací pro monitorování a správu](data-factory-monitor-manage-app.md).

Po opětovném spuštění průřezu 9-10 pro **Dataset2** spustí Data Factory pro výslednou datovou sadu 9-10 závislý řez.

![Opětovné spuštění řezu, který selhal](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Kanál může obsahovat víc než jednu aktivitu. Pokud máte v kanálu více aktivit a výstupem aktivity není vstup jiné aktivity, aktivity mohou běžet paralelně, pokud jsou vstupní datové řezy pro aktivity připravené.

Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Aktivity můžou být ve stejném kanálu nebo v různých kanálech. Druhá aktivita se spustí jenom v případě, že se první úspěšně dokončí.

Zvažte například následující případ, kde má kanál dvě aktivity:

1. Aktivita a1, která vyžaduje externí vstupní datovou sadu D1 a vytváří výstupní datovou sadu D2.
2. Aktivita a2, která vyžaduje vstup ze sady dat D2 a vytváří výstupní datovou sadu D3.

V tomto scénáři jsou aktivity a1 a a2 ve stejném kanálu. Aktivita a1 se spustí, když jsou k dispozici externí data a dosáhnou plánované četnosti dostupnosti. Aktivita a2 se spustí, když budou k dispozici naplánované řezy z D2 a dojde k dosažení plánované četnosti dostupnosti. Pokud dojde k chybě v jednom z řezů v datové sadě D2, a2 se pro tento řez nespustí, dokud nebude k dispozici.

Zobrazení diagramu s oběma aktivitami ve stejném kanálu by vypadalo jako v následujícím diagramu:

![Řetězení aktivit ve stejném kanálu](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak bylo zmíněno dříve, aktivity mohou být v různých kanálech. V takovém scénáři by zobrazení diagramu vypadalo podobně jako v následujícím diagramu:

![Řetězení aktivit ve dvou kanálech](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Příklad najdete v části kopírování sekvenčně v dodatku.

## <a name="model-datasets-with-different-frequencies"></a>Modelování datových sad s různou frekvencí
V ukázkách byly frekvence pro vstupní a výstupní datové sady a okno plánu aktivit stejné. Některé scénáře vyžadují možnost vytvářet výstup s různou frekvencí, než je frekvence jednoho nebo více vstupů. Data Factory podporuje modelování těchto scénářů.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Ukázka 1: vytvoření denní výstupní sestavy pro vstupní data, která jsou k dispozici každou hodinu
Vezměte v úvahu scénář, ve kterém máte během každé hodiny ve službě Azure Blob Storage k dispozici vstupní měření dat od senzorů. Chcete vytvořit každodenní souhrnnou sestavu se statistikami, jako je například střední, maximální a minimální hodnota pro den s [Data Factory aktivitou podregistru](data-factory-hive-activity.md).

Tady je postup, jak můžete modelovat tento scénář pomocí Data Factory:

**Vstupní datová sada**

Hodinové vstupní soubory jsou ve složce pro daný den vyřazeny. Dostupnost pro vstup je nastavená na **hodinu** (frekvence: hodina, interval: 1).

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

Jeden výstupní soubor se vytvoří každý den ve složce dne. Dostupnost výstupu je nastavená na **den** (frekvence: den a interval: 1).

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

**Aktivita: aktivita podregistru v kanálu**

Skript podregistru obdrží příslušné informace o *typu DateTime* jako parametry, které používají proměnnou **WindowStart** , jak je znázorněno v následujícím fragmentu kódu. Skript podregistru používá tuto proměnnou k načtení dat ze správné složky pro daný den a spuštění agregace pro vygenerování výstupu.

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

Následující diagram znázorňuje scénář z hlediska závislosti dat.

![Závislost dat](./media/data-factory-scheduling-and-execution/data-dependency.png)

Výstupní řez každého dne závisí na 24 hodinových řezech ze vstupní datové sady. Data Factory tyto závislosti automaticky vypočte pomocí zjištění vstupních datových řezů, které spadají do stejného časového období jako výstupní řez, který se má vyrobit. Pokud některý z 24 vstupních řezů není k dispozici, Data Factory čeká, než se vstupní řez před spuštěním každodenního spuštění aktivity vypustí.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Ukázka 2: určení závislosti s výrazy a Data Factory funkcemi
Pojďme zvážit jiný scénář. Předpokládejme, že máte aktivitu podregistru, která zpracovává dvě vstupní datové sady. Jedna z nich má data denně, ale jeden z nich získává nová data každý týden. Předpokládejme, že jste chtěli spojit mezi dvěma vstupy a vytvořit výstup každý den.

Jednoduchý přístup, ve kterém Data Factory automaticky vyhodnotit správné vstupní řezy ke zpracování pomocí zarovnání na časové období výstupního datového řezu nefunguje.

Je nutné určit, že pro každý běh aktivity má Data Factory pro týdenní vstupní datovou sadu použít datový řez poslední týden. K implementaci tohoto chování použijte Azure Data Factory funkce, jak je znázorněno v následujícím fragmentu kódu.

**Input1: Azure Blob**

Prvním vstupem je denní aktualizace objektu blob Azure.

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

**Input2: Azure Blob**

Input2 je týdenní aktualizace objektu blob Azure.

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

**Výstup: Azure Blob**

Jeden výstupní soubor se vytvoří každý den ve složce pro daný den. Dostupnost výstupu je nastavená na **den** (frekvence: den, interval: 1).

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

**Aktivita: aktivita podregistru v kanálu**

Aktivita podregistru přijímá dva vstupy a vytváří výstupní řez každý den. Můžete zadat každý den výstupní řez, který bude záviset na vstupním řezu předchozího týdne pro týdenní vstup následujícím způsobem.

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

Seznam funkcí a systémových proměnných, které Data Factory podporuje, najdete v tématu [Data Factory functions a systémových proměnných](data-factory-functions-variables.md) .

## <a name="appendix"></a>Přílohy

### <a name="example-copy-sequentially"></a>Příklad: kopírování sekvenčně
Je možné spustit více operací kopírování jeden po druhém sekvenčním a seřazeným způsobem. Například můžete mít dvě aktivity kopírování v kanálu (soubor copyactivity1 a CopyActivity2) s následujícími vstupními datovými sadami výstupních dat:   

Soubor copyactivity1

Input: DataSet. Výstup: Dataset2.

CopyActivity2

Vstup: Dataset2.  Výstup: Dataset3.

CopyActivity2 se spustí jenom v případě, že soubor copyactivity1 běžel úspěšně a Dataset2 je k dispozici.

Tady je ukázkový formát JSON kanálu:

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

Všimněte si, že v příkladu je výstupní datová sada první aktivity kopírování (Dataset2) zadána jako vstup pro druhou aktivitu. Proto se druhá aktivita spustí pouze v případě, že je výstupní datová sada z první aktivity připravena.  

V tomto příkladu může mít CopyActivity2 jiný vstup, jako je například Dataset3, ale zadáte Dataset2 jako vstup do CopyActivity2, takže se aktivita nespustí až do dokončení soubor copyactivity1. Například:

Soubor copyactivity1

Vstup: DataSet1.. Výstup: Dataset2.

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

Všimněte si, že v tomto příkladu jsou pro druhou aktivitu kopírování zadány dvě vstupní datové sady. Pokud je zadáno více vstupů, pro kopírování dat se používá pouze první vstupní datová sada, ale jiné datové sady se používají jako závislosti. CopyActivity2 se spustí až po splnění následujících podmínek:

* Soubor copyactivity1 se úspěšně dokončil a k dispozici je Dataset2. Tato datová sada se nepoužívá při kopírování dat do Dataset4. Funguje pouze jako závislost plánování pro CopyActivity2.   
* Dataset3 je k dispozici. Tato datová sada představuje data, která se zkopírují do cíle. 
