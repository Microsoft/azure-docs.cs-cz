---
title: Plánování a provádění pomocí datové továrny
description: Naučte se aspekty plánování a provádění aplikačního modelu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281064"
---
# <a name="data-factory-scheduling-and-execution"></a>Plánování a provádění datové továrny
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si článek [spuštění kanálu a aktivační události.](../concepts-pipeline-execution-triggers.md)

Tento článek vysvětluje aspekty plánování a spouštění aplikačního modelu služby Azure Data Factory. Tento článek předpokládá, že rozumíte základům konceptů modelu aplikace Data Factory, včetně aktivity, kanálů, propojených služeb a datových sad. Základní koncepty Azure Data Factory najdete v následujících článcích:

* [Úvodní informace k Data Factory](data-factory-introduction.md)
* [Kanály](data-factory-create-pipelines.md)
* [Soubory](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Počáteční a koncový čas potrubí
Kanál je aktivní pouze mezi jeho **čas zahájení** a **čas ukončení.** Není proveden před časem zahájení nebo po koncovém čase. Pokud je kanál pozastaven, není proveden bez ohledu na jeho počáteční a koncový čas. Pro spuštění kanálu by neměl být pozastaven. Tato nastavení (začátek, konec, pozastaveno) najdete v definici kanálu: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Další informace tyto vlastnosti, naleznete [v tématu vytvoření článku potrubí.](data-factory-create-pipelines.md) 


## <a name="specify-schedule-for-an-activity"></a>Určení plánu aktivity
Není kanál, který je spuštěn. Jsou aktivity v kanálu, které jsou prováděny v celkovém kontextu kanálu. Můžete určit opakovaný plán aktivity pomocí **plánovače** části aktivity JSON. Můžete například naplánovat, aby se aktivita spouštěla každou hodinu, a to následujícím způsobem:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak je znázorněno v následujícím diagramu, zadání plánu pro aktivitu vytvoří řadu omílání oken s v počáteční a koncové časy kanálu. Omílání oken jsou řada pevných velikostí nepřekrývajících se souvislých časových intervalů. Tato logická okna pro omílání pro aktivitu se nazývají **okna aktivity**.

![Příklad plánovače aktivit](media/data-factory-scheduling-and-execution/scheduler-example.png)

Vlastnost **plánovače** pro aktivitu je volitelná. Pokud zadáte tuto vlastnost, musí odpovídat kadenci, kterou zadáte v definici výstupní datové sady pro aktivitu. Výstupní datové sady v současné době řídí plán. Proto je nutné vytvořit výstupní datovou sadu i v případě, že aktivita nevytváří žádný výstup. 

## <a name="specify-schedule-for-a-dataset"></a>Určení plánu pro datovou sadu
Aktivita v kanálu data factory může trvat nula nebo více **vstupních datových sad** a vytvářet jednu nebo více výstupních datových sad. Pro aktivitu můžete určit kadenci, při které jsou vstupní data k dispozici, nebo pomocí části **dostupnosti** v definicích datové sady. 

**Frekvence** v části **dostupnosti** určuje časovou jednotku. Povolené hodnoty pro frekvenci jsou: Minuta, Hodina, Den, Týden a Měsíc. Vlastnost **interval** v části dostupnosti určuje násobitel pro frekvenci. Například: Pokud je frekvence nastavena na Den a interval je nastaven a 1 pro výstupní datové sady, výstupní data jsou vytvářena denně. Pokud zadáte frekvenci jako minutu, doporučujeme nastavit interval na nejméně 15. 

V následujícím příkladu jsou vstupní data k dispozici každou hodinu`"frequency": "Hour", "interval": 1`a výstupní data jsou vytvářena každou hodinu ( . 

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

V současné době **výstup datové sady řídí plán**. Jinými slovy, plán zadaný pro výstupní datovou sadu se používá ke spuštění aktivity za běhu. Proto je nutné vytvořit výstupní datovou sadu i v případě, že aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. 

V následující definici kanálu se vlastnost **plánovače** používá k určení plánu aktivity. Tato vlastnost je nepovinná. V současné době musí plán aktivity odpovídat plánu určenému pro výstupní datovou sadu.
 
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

V tomto příkladu aktivita běží každou hodinu mezi počáteční a koncový čas kanálu. Výstupní data jsou vytvářena každou hodinu pro tříhodinová okna (8:00 - 9:00, 9:00 - 10:00 a 10:00 - 11:00). 

Každá jednotka dat spotřebovaných nebo vytvořených spuštěním aktivity se nazývá **řez dat**. Následující diagram znázorňuje příklad aktivity s jednou vstupní datovou sadou a jednou výstupní datovou sadou: 

![Plánovač dostupnosti](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagram znázorňuje hodinové datové řezy pro vstupní a výstupní datovou sadu. Diagram znázorňuje tři vstupní řezy, které jsou připraveny ke zpracování. Aktivita 10-11 AM probíhá a vytváří výstupní řez 10-11 AM. 

K časovému intervalu přidruženému k aktuálnímu řezu v datové sadě JSON můžete přistupovat pomocí proměnných: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) a [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobně můžete přistupovat k časovému intervalu přidruženému k oknu aktivity pomocí WindowStart a WindowEnd. Plán aktivity musí odpovídat plánu výstupní datové sady pro aktivitu. Proto sliceStart a SliceEnd hodnoty jsou stejné jako WindowStart a WindowEnd hodnoty v uvedeném pořadí. Další informace o těchto proměnných naleznete v [tématu Funkce datové továrny a](data-factory-functions-variables.md#data-factory-system-variables) systémové proměnné články.  

Tyto proměnné můžete použít pro různé účely ve vaší aktivitě JSON. Můžete je například použít k výběru dat ze vstupních a výstupních datových sad představujících data časových řad (například: 8:00 až 9:00). Tento příklad také používá **WindowStart** a **WindowEnd** vybrat příslušná data pro spuštění aktivity a zkopírovat do objektu blob s příslušnou **folderPath**. **FolderPath** je parametrizován mít samostatnou složku pro každou hodinu.  

V předchozím příkladu je plán zadaný pro vstupní a výstupní datové sady stejný (každou hodinu). Pokud vstupní datová sada pro aktivitu je k dispozici na jinou frekvenci, řekněme každých 15 minut, aktivita, která vytváří tuto výstupní datovou sadu stále běží jednou za hodinu jako výstupní datová sada je to, co řídí plán aktivity. Další informace naleznete v [tématu Model datové sady s různými frekvencemi](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Dostupnost datové sady a zásady
Zobrazení využití vlastností frekvence a intervalu v části dostupnosti definice datové sady. Existuje několik dalších vlastností, které ovlivňují plánování a provádění aktivity. 

### <a name="dataset-availability"></a>Dostupnost datové sady 
Následující tabulka popisuje vlastnosti, které můžete použít v části **dostupnosti:**

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro výrobu řezů datové sady.<br/><br/><b>Podporovaná frekvence</b>: Minuta, Hodina, Den, Týden, Měsíc |Ano |Není k dispozici |
| interval |Určuje násobitel pro frekvenci.<br/><br/>"Interval frekvence x" určuje, jak často se řez vyrábí.<br/><br/>Pokud potřebujete, aby byla datová sada rozdělena na základě hodin, nastavte <b>frekvenci</b> na <b>hodinu</b>a <b>interval</b> na <b>1</b>.<br/><br/><b>Poznámka:</b>Pokud zadáte Frekvenci jako minutu, doporučujeme nastavit interval na nejméně 15 |Ano |Není k dispozici |
|  – styl |Určuje, zda má být řez vytvořen na začátku/na konci intervalu.<ul><li>Začátekintervalu</li><li>EndOfInterval</li></ul><br/><br/>Pokud frekvence je nastavena na Měsíc a styl je nastaven na EndOfInterval, řez se vyrábí na poslední den v měsíci. Pokud je styl nastaven na StartOfInterval, řez se vytváří první den v měsíci.<br/><br/>Pokud frekvence je nastavena na Den a styl je nastaven a EndOfInterval, řez se vyrábí v poslední hodině dne.<br/><br/>Pokud Frekvence je nastavena na hodinu a styl je nastaven na EndOfInterval, řez se vytváří na konci hodiny. Například pro řez pro 1 PM – 14 hodin období, řez se vyrábí v 14:00. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní časovou polohu používanou plánovačem k výpočtu hranic datových sad. <br/><br/><b>Poznámka:</b>Pokud AnchorDateTime má data části, které jsou podrobnější než frekvence pak podrobnější části jsou ignorovány. <br/><br/>Například pokud <b>interval</b> je <b>hodinová</b> (frekvence: hodina a interval: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b>, pak <b>minuty a sekundy</b> části AnchorDateTime jsou ignorovány. |Ne |01/01/0001 |
| posun |Časový posun, o které jsou posunuty počáteční a koncové všechny řezy datové sady. <br/><br/><b>Poznámka:</b>Pokud jsou zadány anchorDateTime i offset, výsledkem je kombinovaný posun. |Ne |Není k dispozici |

### <a name="offset-example"></a>příklad odsazení
Ve výchozím nastavení`"frequency": "Day", "interval": 1`začínají denní řezy ( ) v čase 12 AM UTC (půlnoc). Pokud chcete, aby čas zahájení byl čas 6 AM UTC místo, nastavte posun, jak je znázorněno v následujícím fragmentu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime příklad
V následujícím příkladu je datová sada vytvářena jednou za 23 hodin. První řez začíná v čase určeném anchorDateTime, `2017-04-19T08:00:00` který je nastaven na (Čas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>posun/styl Příklad
Následující datová sada je měsíční datovou sadou a je vytvářena třetí den`3.08:00:00`každého měsíce v 8:00 ( ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Zásady datové sady
Datová sada může mít definovanou zásadu ověření, která určuje, jak mohou být data generovaná spuštěním řezu ověřena před tím, než je připravena ke spotřebě. V takových případech po dokončení spuštění řezu se stav výstupní ho řezu změní na **Čekání** s dílčím stavem **ověření**. Po ověření řezů se stav řezu změní na **Připraveno**. Pokud byla vytvořena část dat, ale neprošla ověřením, aktivita se spustí pro podřízené řezy, které jsou závislé na tomto řezu, nebudou zpracovány. [Monitorování a správa kanálů](data-factory-monitor-manage-pipelines.md) pokrývá různé stavy řezů dat v datové továrně.

Oddíl **zásad** v definici datové sady definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat. Následující tabulka popisuje vlastnosti, které můžete použít v části **zásady:**

| Název zásady | Popis | Použito | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Ověří, že data v **objektu blob Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob |Ne |Není k dispozici |
| minimumRows | Ověří, zda data v **databázi Azure SQL** nebo **v tabulce Azure** obsahují minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

#### <a name="examples"></a>Příklady
**minimumVelikostMB:**

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

Další informace o těchto vlastnostech a příkladech naleznete v tématu Vytvoření článku [datových sad.](data-factory-create-datasets.md) 

## <a name="activity-policies"></a>Zásady aktivit
Zásady ovlivňují chování aktivity za běhu, konkrétně při zpracování řezu tabulky. V následující tabulce jsou uvedeny podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet paralelních spuštění aktivit, ke kterým může dojít na různých řezech. Například pokud aktivita potřebuje projít velkou sadu dostupných dat, s větší hodnota souběžnosti urychluje zpracování dat. |
| executionPriorityOrder |NejnovějšíPrvní<br/><br/>NejstaršíPrvní |NejstaršíPrvní |Určuje pořadí řezů dat, které jsou zpracovávány.<br/><br/>Například pokud máte 2 řezy (jeden se děje v 16:00 a další v 17:00) a oba čekají na spuštění. Pokud nastavíte spuštěníPriorityOrder být NewestFirst, řez v 17:00 je zpracována jako první. Podobně pokud nastavíte spuštěníPriorityORder být OldestFIrst, pak řez na 4 pm je zpracována. |
| retry |Integer<br/><br/>Maximální hodnota může být 10 |0 |Počet opakování před zpracováním dat pro řez je označen jako selhání. Spuštění aktivity pro řez dat je opakován až do zadaného počtu opakování. Opakování se provádí co nejdříve po selhání. |
| timeout |TimeSpan |00:00:00 |Časový čas aktivity. Příklad: 00:10:00 (znamená časový výtažek 10 min)<br/><br/>Pokud hodnota není zadána nebo je 0, časový čas je nekonečný.<br/><br/>Pokud doba zpracování dat na řezu překročí hodnotu časového limitu, je zrušena a systém se pokusí zopakovat zpracování. Počet opakování závisí na vlastnosti opakování. Když dojde k časovému výpadku, stav je nastaven na TimedOut. |
| Zpoždění |TimeSpan |00:00:00 |Určete zpoždění před zahájením zpracování dat řezu.<br/><br/>Provádění aktivity pro řez dat je spuštěn a zpoždění je po očekávané době provádění.<br/><br/>Příklad: 00:10:00 (znamená zpoždění 10 minut) |
| longRetry |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet pokusů o dlouhou opakování před spuštěním řezu se nezdařilo.<br/><br/>LongRetry pokusy jsou rozmístěny longRetryInterval. Takže pokud potřebujete zadat čas mezi pokusy o opakování, použijte longRetry. Pokud jsou zadány Retry a longRetry jsou zadány, každý pokus longRetry zahrnuje pokusy opakovat a maximální počet pokusů je Opakovat * longRetry.<br/><br/>Například pokud máme následující nastavení v zásadách aktivity:<br/>Opakování: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládejme, že je pouze jeden řez ke spuštění (stav je Čekání) a provádění aktivity selže pokaždé. Zpočátku by došlo ke 3 po sobě jdoucím pokusům o provedení. Po každém pokusu by byl stav řezu Opakovat. Po prvních 3 pokusy jsou u konce, stav řezu by LongRetry.<br/><br/>Po hodině (to znamená longRetryInteval hodnota), by další sadu 3 po sobě jdoucích pokusů o spuštění. Poté by se stav řezu nezdařil a nepokoušeli by se o žádné další pokusy. Proto bylo provedeno celkem 6 pokusů.<br/><br/>Pokud je jakékoli spuštění úspěšné, stav řezu by byl Připraven a žádné další pokusy o opakování.<br/><br/>longRetry lze použít v situacích, kdy závislá data dorazí v nedeterministické době nebo celkové prostředí je šupinatá, za kterých dochází ke zpracování dat. V takových případech opakování jeden po druhém nemusí pomoci a to po časovém intervalu má za následek požadovaný výstup.<br/><br/>Upozornění: nenastavujte vysoké hodnoty pro longRetry nebo longRetryInterval. Vyšší hodnoty obvykle znamenají další systémové problémy. |
| longRetryInterval |TimeSpan |00:00:00 |Prodleva mezi dlouhými pokusy o opakování |

Další informace naleznete v článku [Potrubí.](data-factory-create-pipelines.md) 

## <a name="parallel-processing-of-data-slices"></a>Paralelní zpracování datových řezů
Můžete nastavit počáteční datum kanálu v minulosti. Pokud tak učiníte, Data Factory automaticky vypočítá (zpět výplně) všechny řezy dat v minulosti a začne jejich zpracování. Například: Pokud vytvoříte kanál s počátečním datem 2017-04-01 a aktuální datum je 2017-04-10. Pokud je kadence výstupní datové sady denně, potom Data Factory začne zpracovávat všechny řezy od 2017-04-01 do 2017-04-09 okamžitě, protože počáteční datum je v minulosti. Řez 2017-04-10 ještě není zpracován, protože hodnota vlastnosti style v části dostupnosti je EndOfInterval ve výchozím nastavení. Nejstarší řez je zpracován jako první jako výchozí hodnota executionPriorityOrder je OldestFirst. Popis vlastnosti stylu naleznete v části [dostupnosti datové sady.](#dataset-availability) Popis části executionPriorityOrder naleznete v části [Zásady aktivity.](#activity-policies) 

Můžete nakonfigurovat zpětvyplněná data řezy, které mají být zpracovány paralelně nastavením **vlastnosti souběžnosti** v části **zásad** aktivity JSON. Tato vlastnost určuje počet paralelní chod aktivit, ke kterým může dojít na různých řezech. Výchozí hodnota vlastnosti souběžnosti je 1. Proto jeden řez je zpracována současně ve výchozím nastavení. Maximální hodnota je 10. Když potrubí potřebuje projít velkou sadu dostupných dat, s větší hodnota souběžnosti urychluje zpracování dat. 

## <a name="rerun-a-failed-data-slice"></a>Opětovné spuštění neúspěšného řezu dat
Když dojde k chybě při zpracování řezu dat, můžete zjistit, proč se zpracování řezu nezdaří pomocí listů portálu Azure nebo monitorování a správa aplikace. Podrobnosti najdete [v tématu Monitorování a správa kanálů pomocí blades portálu Azure](data-factory-monitor-manage-pipelines.md) nebo [aplikace Monitorování a správa.](data-factory-monitor-manage-app.md)

Vezměme si následující příklad, který zobrazuje dvě aktivity. Aktivita1 a Aktivita 2. Activity1 spotřebovává řez Dataset1 a vytvoří řez Dataset2, který je spotřebován jako vstup Activity2 k vytvoření řezu konečné datové sady.

![Neúspěšný řez](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram ukazuje, že ze tří posledních řezů došlo k selhání, které vytvořilo řez 9-10 AM pro Dataset2. Data Factory automaticky sleduje závislost pro datovou sadu časových řad. V důsledku toho nespustí aktivitu spustit pro řez po proudu 9-10.

Nástroje pro monitorování a správu data factory umožňují přejít k podrobnostem do diagnostických protokolů pro neúspěšný řez, abyste snadno našli hlavní příčinu problému a opravili ho. Po opevnění problému můžete snadno spustit spuštění aktivity a vytvořit neúspěšný řez. Další informace o tom, jak znovu spustit a pochopit přechody stavu pro řezy dat, najdete [v tématu monitorování a správa kanálů pomocí blades portálu Azure](data-factory-monitor-manage-pipelines.md) nebo [monitorování a správa aplikace](data-factory-monitor-manage-app.md).

Po opětovném spuštění řezu 9-10 AM pro **Dataset2**spustí Data Factory spuštění pro řez závislý na 9-10 am na konečné datové sady.

![Opětovné spuštění řezu se nezdařilo](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Kanál může obsahovat víc než jednu aktivitu. Pokud máte více aktivit v kanálu a výstup aktivity není vstup emitovat jinou aktivitu, aktivity může běžet paralelně, pokud jsou připraveny řezy vstupních dat pro aktivity.

Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Aktivity mohou být ve stejném kanálu nebo v různých kanálech. Druhá aktivita se spustí pouze v případě, že první úspěšně dokončí.

Zvažte například následující případ, kdy má kanál dvě aktivity:

1. Aktivita A1, která vyžaduje externí vstupní datovou sadu D1 a vytváří výstupní datovou sadu D2.
2. Aktivita A2, která vyžaduje vstup z datové sady D2 a vytváří výstupní datovou sadu D3.

V tomto scénáři aktivity A1 a A2 jsou ve stejném kanálu. Aktivita A1 se spustí, když jsou k dispozici externí data a je dosaženo naplánované frekvence dostupnosti. Aktivita A2 se spustí, když jsou k dispozici naplánované řezy z D2 a je dosažena naplánovaná frekvence dostupnosti. Pokud je chyba v jednom z řezů v datové sadě D2, A2 nespustí pro tento řez, dokud nebude k dispozici.

Zobrazení diagramu s oběma aktivitami ve stejném kanálu bude vypadat jako následující diagram:

![Řetězení aktivit ve stejném potrubí](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak již bylo zmíněno dříve, činnosti by mohly být v různých potrubích. V takovém scénáři by zobrazení diagramu vypadat jako následující diagram:

![Řetězení ve dvou potrubích](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Příklad naleznete v části kopie v dodatku.

## <a name="model-datasets-with-different-frequencies"></a>Modelové datové sady s různými frekvencemi
Ve vzorcích byly frekvence vstupních a výstupních datových sad a okna plánu aktivit stejné. Některé scénáře vyžadují schopnost vytvářet výstup s frekvencí jinou než frekvence jednoho nebo více vstupů. Data Factory podporuje modelování těchto scénářů.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Ukázka 1: Vytvoření denní výstupní sestavy pro vstupní data, která jsou k dispozici každou hodinu
Zvažte scénář, ve kterém máte vstupní naměřená data ze senzorů k dispozici každou hodinu v úložišti objektů Blob Azure. Chcete vytvořit denní agregační sestavu se statistikami, jako je průměr, maximum a minimum pro den s [aktivitou podregistru datové továrny](data-factory-hive-activity.md).

Zde je postup, jak můžete modelovat tento scénář s Factory dat:

**Vstupní datová sada**

Hodinové vstupní soubory jsou vynechány ve složce pro daný den. Dostupnost pro vstup je nastavena na **hodinu** (frekvence: hodina, interval: 1).

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

Jeden výstupní soubor je vytvořen každý den ve složce dne. Dostupnost výstupu je nastavena na **den** (frekvence: Den a interval: 1).

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

**Aktivita: aktivita úlu v potrubí**

Skript podregistru obdrží příslušné informace *DateTime* jako parametry, které používají proměnnou **WindowStart,** jak je znázorněno v následujícím fragmentu. Skript podregistru používá tuto proměnnou k načtení dat ze správné složky pro den a spuštění agregace pro generování výstupu.

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

Následující diagram znázorňuje scénář z hlediska závislosti na datech.

![Závislost dat](./media/data-factory-scheduling-and-execution/data-dependency.png)

Výstupní řez pro každý den závisí na 24 hodinových řezech ze vstupní datové sady. Data Factory vypočítá tyto závislosti automaticky zjišťuje, že vstupní data řezy, které spadají do stejného časového období jako výstupní řez, který má být vytvořen. Pokud některý z 24 vstupních řezů není k dispozici, Data Factory čeká na vstupní řez, který má být připraven před zahájením denní aktivity spustit.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Ukázka 2: Určení závislosti pomocí výrazů a funkcí datové továrny
Zvažme jiný scénář. Předpokládejme, že máte aktivitu podregistru, která zpracovává dvě vstupní datové sady. Jeden z nich má nová data denně, ale jeden z nich dostává nová data každý týden. Předpokládejme, že jste chtěli provést spojení mezi dvěma vstupy a vytvořit výstup každý den.

Jednoduchý přístup, ve kterém Data Factory automaticky zjidní správné vstupní řezy ke zpracování zarovnáním časové období výstupní ho dat nefunguje.

Je nutné zadat, že pro každé spuštění aktivity data factory by měl použít minulý týden datový řez pro týdenní vstupní datové sady. K implementaci tohoto chování se používají funkce Azure Data Factory, jak je znázorněno v následujícím fragmentu.

**Vstup1: Objekt blob Azure**

První vstup je objekt blob Azure, který se aktualizuje denně.

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

**Vstup2: Objekt blob Azure**

Input2 je objekt blob Azure, který se aktualizuje každý týden.

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

**Výstup: Objekt blob Azure**

Jeden výstupní soubor je vytvořen každý den ve složce pro den. Dostupnost výstupu je nastavena na **denní (frekvence:** Den, interval: 1).

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

**Aktivita: aktivita úlu v potrubí**

Aktivita podregistru přebírá dva vstupy a vytváří výstupní řez každý den. Můžete určit výstupní řez každého dne, který bude záviset na vstupním řezu předchozího týdne pro týdenní vstup následujícím způsobem.

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

Seznam funkcí a systémových proměnných, které data factory podporuje, najdete v tématu [Funkce datové továrny a systémové proměnné.](data-factory-functions-variables.md)

## <a name="appendix"></a>Příloha

### <a name="example-copy-sequentially"></a>Příklad: kopírování postupně
Je možné spustit více operací kopírování jeden po druhém sekvenčním/seřazeným způsobem. Můžete mít například dvě aktivity kopírování v kanálu (CopyActivity1 a CopyActivity2) s následujícími výstupními datovými sadami vstupních dat:   

CopyActivity1

Vstup: Datová sada. Výstup: Dataset2.

CopyActivity2

Vstup: Dataset2.  Výstup: Dataset3.

CopyActivity2 by spustit pouze v případě, že CopyActivity1 úspěšně spuštěna dataset2 je k dispozici.

Zde je ukázkový kanál JSON:

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

Všimněte si, že v příkladu je výstupní datová sada první aktivity kopírování (Dataset2) zadána jako vstup pro druhou aktivitu. Proto druhá aktivita spustí pouze v případě, že výstupní datová sada z první aktivity je připraven.  

V příkladu CopyActivity2 může mít jiný vstup, například Dataset3, ale zadáte Dataset2 jako vstup copyactivity2, takže aktivita nespustí, dokud CopyActivity1 nedokončí. Například:

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

Všimněte si, že v příkladu jsou určeny dvě vstupní datové sady pro druhou aktivitu kopírování. Pokud je zadáno více vstupů, slouží ke kopírování dat pouze první vstupní datová sada, ale jako závislosti se používají jiné datové sady. CopyActivity2 by spustit až po splnění následujících podmínek:

* CopyActivity1 byl úspěšně dokončen a Dataset2 je k dispozici. Tato datová sada se nepoužívá při kopírování dat do Dataset4. Slouží pouze jako plánování závislost pro CopyActivity2.   
* Dataset3 je k dispozici. Tato datová sada představuje data, která jsou zkopírována do cíle. 
