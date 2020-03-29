---
title: Vytváření pokročilých plánů úloh a opakování
description: Zjistěte, jak vytvořit pokročilé plány a opakování pro úlohy v Plánovači Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: b85932bf0d4fd080afadef2bc28d6a218b2d627a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898586"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Vytváření pokročilých plánů a opakování úloh v Plánovači Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

V rámci úlohy [Plánovač Azure](../scheduler/scheduler-intro.md) je plán jádrem, které určuje, kdy a jak služba Plánovač úlohu spouští. Pomocí plánovače můžete nastavit více jednorázových a opakovaných plánů pro úlohu. Jednorázové plány se spouštějí pouze jednou v určený čas a jsou v podstatě opakované plány, které jsou spuštěny pouze jednou. Opakované plány se spouštějí na zadané frekvenci. S touto flexibilitou můžete plánovač použít pro různé obchodní scénáře, například:

* **Pravidelně čistěte data**: Vytvořte denní úlohu, která odstraní všechny tweety starší než tři měsíce.

* **Archivní data**: Vytvořte měsíční úlohu, která odešle historii faktur do zálohovací služby.

* **Požádat o externí data**: Vytvořte úlohu, která běží každých 15 minut a vytáhne novou zprávu o počasí z NOAA.

* **Zpracovat obrázky**: Vytvořte úlohu ve všední den, která běží mimo špičku a používá cloud computing pro kompresi obrázků nahraných během dne.

Tento článek popisuje ukázkové úlohy, které můžete vytvořit pomocí Plánovače a [rozhraní REST API plánovače Azure](/rest/api/scheduler)a obsahuje definici zápisu objektu JavaScript (JSON) pro každý plán. 

## <a name="supported-scenarios"></a>Podporované scénáře

Tyto příklady ukazují rozsah scénářů, které podporuje Plánovač Azure a jak vytvořit plány pro různé vzorce chování, například:

* Spustit jednou v určitém datu a čase.
* Spusťte a opakujte určitý počet opakování.
* Okamžitě spusťte a opakujte.
* Spouštět a opakovat *každých n* minut, hodin, dnů, týdnů nebo měsíců počínaje určitým časem.
* Spouštět a opakovat každý týden nebo měsíčně, ale pouze v určité dny v týdnu nebo v konkrétní dny v měsíci.
* Spusťte a opakujte více než jednou za určité období. Například každý měsíc poslední pátek a pondělí nebo denně v 5:15 a v 17:15.

Tento článek později popisuje tyto scénáře podrobněji.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Vytvořit plán pomocí rozhraní REST API

Pokud chcete vytvořit základní plán s [rozhraním REST api Plánovače Azure](/rest/api/scheduler), postupujte takto:

1. Zaregistrujte své předplatné Azure u poskytovatele prostředků pomocí [operace Registrovat – rozhraní REST API správce prostředků](https://docs.microsoft.com/rest/api/resources/providers). Název zprostředkovatele služby Azure Scheduler je **Microsoft.Scheduler**. 

1. Vytvořte kolekci úloh pomocí [operace Vytvořit nebo aktualizovat pro kolekce úloh](https://docs.microsoft.com/rest/api/scheduler/jobcollections) v rozhraní REST ROZHRANÍ API plánovače. 

1. Vytvořte úlohu pomocí [operace Vytvořit nebo aktualizovat pro úlohy](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Prvky schématu úlohy

Tato tabulka obsahuje přehled na vysoké úrovni pro hlavní prvky JSON, které můžete použít při nastavování opakování a plánů pro úlohy. 

| Element | Požaduje se | Popis | 
|---------|----------|-------------|
| **startTime** | Ne | Hodnota řetězce DateTime ve [formátu ISO 8601,](https://en.wikipedia.org/wiki/ISO_8601) která určuje, kdy se úloha poprvé spustí v základním plánu. <p>U složitých plánů úloha začíná nejdříve **startTime**. | 
| **recurrence** | Ne | Pravidla opakování při spuštění úlohy. Objekt **opakování** podporuje tyto prvky: **frekvence**, **interval**, **plán**, **počet**a **endTime**. <p>Pokud použijete prvek **opakování,** musíte také použít prvek **frekvence,** zatímco jiné prvky **opakování** jsou volitelné. |
| **Frekvence** | Ano, při použití **opakování** | Časová jednotka mezi výskyty a podporuje tyto hodnoty: "Minuta", "Hodina", "Den", "Týden", "Měsíc" a "Rok" | 
| **interval** | Ne | Kladné celé číslo, které určuje počet časových jednotek mezi výskyty na základě **frekvence**. <p>Pokud je například **interval** 10 a **frekvence** je "Týden", úloha se opakuje každých 10 týdnů. <p>Zde je největší počet intervalů pro každou frekvenci: <p>- 18 měsíců <br>- 78 týdnů <br>- 548 dní <br>- Pro hodiny a minuty je rozsah 1 <= <*interval*> <= 1000. | 
| **Plán** | Ne | Definuje změny opakování na základě zadaných minutových značek, hodinových značek, dnů v týdnu a dnů v měsíci. | 
| **count** | Ne | Kladné celé číslo, které určuje počet spuštění úlohy před dokončením. <p>Například pokud má denní úloha **počet** nastavený na 7 a počáteční datum je pondělí, úloha skončí v neděli. Pokud počáteční datum již uplynulo, první spuštění se vypočítá z času vytvoření. <p>Bez **endTime** nebo **count**, úloha běží nekonečně. Nelze použít **počet** a **endTime** ve stejné úloze, ale pravidlo, které dokončí jako první je dodržena. | 
| **endTime** | Ne | Hodnota řetězce Date nebo DateTime ve [formátu ISO 8601,](https://en.wikipedia.org/wiki/ISO_8601) která určuje, kdy úloha přestane běžet. Můžete nastavit hodnotu pro **endTime,** která je v minulosti. <p>Bez **endTime** nebo **count**, úloha běží nekonečně. Nelze použít **počet** a **endTime** ve stejné úloze, ale pravidlo, které dokončí jako první je dodržena. |
|||| 

Toto schéma JSON například popisuje základní plán a opakování úlohy: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Data a hodnoty DateTime*

* Data v úlohách plánovače zahrnují pouze datum a postupujte podle [specifikace ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Datum-časy v úlohách Plánovač zahrnují datum a čas, postupujte podle [specifikace ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)a jsou považovány za Čas UTC, pokud není zadán žádný posun UTC. 

Další informace naleznete v [tématu Koncepty, terminologie a entity](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Podrobnosti: startTime

Tato tabulka popisuje, jak ovládací prvky **startTime** řídí způsob spuštění úlohy:

| startTime | Žádné opakování | Opakování, žádný plán | Opakování s plánem |
|-----------|---------------|-------------------------|--------------------------|
| **Žádný čas zahájení** | Okamžitě utíkej. | Okamžitě utíkej. Spustit pozdější spuštění vypočítané z doby posledního spuštění. | Okamžitě utíkej. Spusťte pozdější spuštění na základě plánu opakování. | 
| **Čas zahájení v minulosti** | Okamžitě utíkej. | Vypočítejte první budoucí čas spuštění po počátečním čase a spusťte v tomto okamžiku. <p>Spustit pozdější spuštění vypočítané z doby posledního spuštění. <p>Viz příklad za touto tabulkou. | Zahájit *úlohu nejdříve* zadaný čas zahájení. První výskyt vychází z plánu vypočítaného z času začátku. <p>Spusťte pozdější spuštění na základě plánu opakování. | 
| **Počáteční čas v budoucnosti nebo aktuální čas** | Spustit jednou v zadaném čase zahájení. | Spustit jednou v zadaném čase zahájení. <p>Spustit pozdější spuštění vypočítané z doby posledního spuštění. | Zahájit *úlohu nejdříve* zadaný čas zahájení. První výskyt vychází z plánu a je vypočítaný z času začátku. <p>Spusťte pozdější spuštění na základě plánu opakování. |
||||| 

Předpokládejme, že tento příklad s těmito podmínkami: čas zahájení v minulosti s opakováním, ale bez plánu.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Aktuální datum a čas je duben 08, 2015 v 1:00 PM.

* Počáteční datum a čas je duben 07, 2015 v 14:00, což je před aktuálním datem a časem.

* Opakování je každé dva dny.

1. Za těchto podmínek je první poprava v dubnu 09, 2015 v 14:00. 

   Plánovač vypočítá výskyty spuštění na základě počátečního času, zahodí všechny instance v minulosti a použije další instanci v budoucnu. 
   V tomto případě **startTime** je v dubnu 07, 2015 v 14:00, takže další instance je dva dny od této doby, což je duben 09, 2015 v 14:00.

   První spuštění je stejné, zda **startTime** je 2015-04-05 14:00 nebo 2015-04-01 14:00. Po prvním spuštění pozdější spuštění jsou vypočteny na základě plánu. 
   
1. Popravy pak následovat v tomto pořadí: 
   
   1. 2015-04-11 ve 14:00
   1. 2015-04-13 ve 14:00 
   1. 2015-04-15 ve 14:00
   1. A tak dále...

1. Nakonec, pokud má úloha plán, ale žádné zadané hodiny a minuty, tyto hodnoty jsou ve výchozím nastavení na hodiny a minuty v prvním spuštění.

<a name="schedule"></a>

## <a name="details-schedule"></a>Podrobnosti: plán

Plán můžete **použít** k *omezení* počtu spuštění úloh. Například pokud úloha s **frekvencí** "měsíc" má plán, který běží pouze na den 31, úloha spustí pouze v měsících, které mají 31.

Plán můžete také **použít** k *rozšíření* počtu spuštění úloh. Například pokud úloha s **frekvencí** "měsíc" má plán, který běží na měsíc dny 1 a 2, úloha běží na první a druhý den v měsíci namísto pouze jednou za měsíc.

Pokud zadáte více než jeden prvek plánu, pořadí hodnocení je od největší ho nejmenší: číslo týdne, měsíc den, den v týdnu, hodina a minuta.

Následující tabulka obsahuje podrobný popis elementů plánu:

| Název JSON | Popis | Platné hodnoty |
|:--- |:--- |:--- |
| **minutes** |Minuty hodiny, kdy je úloha spuštěna. |Pole celá čísla. |
| **Hodin** |Hodiny dne, kdy je úloha spuštěna. |Pole celá čísla. |
| **weekDays** |Dny v týdnu, kdy se úloha spustí. Lze zadat pouze s týdenní frekvencí. |Pole některé z následujících hodnot (maximální velikost pole je 7):<br />- "Pondělí"<br />- "Úterý"<br />- "Středa"<br />- "Čtvrtek"<br />- "Pátek"<br />- "Sobota"<br />- "Neděle"<br /><br />Nerozlišují malá a velká písmena. |
| **monthlyOccurrences** |Určuje, které dny v měsíci se úloha spustí. Lze zadat pouze s měsíční frekvencí. |Pole objektů **monthlyOccurrences:**<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **den** je den v týdnu, kdy se úloha spustí. Například *{Neděle}* je každou neděli v měsíci. Povinná hodnota.<br /><br />**je** výskyt dne během měsíce. Například *{Neděle, -1}* je poslední neděle v měsíci. Nepovinný parametr. |
| **monthDays** |Den v měsíci, kdy se úloha spustí. Lze zadat pouze s měsíční frekvencí. |Pole následujících hodnot:<br />– Libovolná hodnota <= −1 a >= −31<br />– Libovolná hodnota >= 1 a <= 31|

## <a name="examples-recurrence-schedules"></a>Příklady: Plány opakování

Následující příklady ukazují různé plány opakování. Příklady se zaměřují na objekt plánu a jeho dílčí prvky.

Tyto plány předpokládají, že **interval** je nastaven na 1\. Příklady také předpokládají správné hodnoty **frekvence** pro hodnoty v **plánu**. Například nelze použít **frekvenci** "den" a mají **monthDays** změny v **plánu**. Tato omezení popisujeme dříve v článku.

| Příklad | Popis |
|:--- |:--- |
| `{"hours":[5]}` |Běh v 5 hodin ráno každý den.<br /><br />Plánovač odpovídá každou hodnotu v "hodiny" s každou hodnotu v "minuty", jeden po druhém, chcete-li vytvořit seznam všech časů, kdy je úloha spuštěna. |
| `{"minutes":[15], "hours":[5]}` |Spuštění každý den v 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Spuštění každý den v 5:15 a 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Spuštění každý den v 5:15, 5:45 a 17:15 a 17:45. |
| `{"minutes":[0,15,30,45]}` |Spuštění každých 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Spuštění každou hodinu.<br /><br />Tato úloha běží každou hodinu. Minuta je řízena hodnotou pro **startTime**, pokud je zadána. Pokud není zadána žádná hodnota **startTime,** je minuta řízena časem vytvoření. Například pokud čas zahájení nebo čas vytvoření (podle toho, co platí) je 12:25 PM, úloha běží v 00:25, 01:25, 02:25, ..., 23:25.<br /><br />Plán je stejný jako úloha s **frekvencí** "hodina", **interval** 1 a žádná hodnota **plánu.** Rozdíl je v tom, že tento plán můžete použít s různými hodnotami **frekvence** a **intervalu** k vytvoření jiných úloh. Například pokud **je frekvence** "měsíc", plán se spustí pouze jednou za měsíc namísto každý den (pokud je **frekvence** "den"). |
| `{minutes:[0]}` |Spuštění v každou celou hodinu.<br /><br />Tato úloha se také spouští každou hodinu, ale v hodinu (12:00, 1:00, 2:00 a tak dále). Tento plán je stejný jako úloha s **frekvencí** "hodina", hodnota **startTime** nula minut a žádný **plán**, pokud je frekvence "den". Pokud je však **frekvence** "týden" nebo "měsíc", plán provede pouze jeden den v týdnu nebo jeden den v měsíci. |
| `{"minutes":[15]}` |Běh at 15 minut po hodině každou hodinu.<br /><br />Běží každou hodinu, počínaje 00:15, 1:15, 2:15 a tak dále. Končí ve 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Spustit v 17:00 v sobotu každý týden. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spustit v 17 hodin v pondělí, středu a pátek každý týden. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění každý týden v pondělí, středu a pátek v 17:15 a 17:45. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spustit v 5:00 a 17:00 v pondělí, středu a pátek každý týden. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spustit v 5:15, 5:45 AM, 5:15 PM, a 5:45 PM v pondělí, středu a pátek každý týden. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spuštění ve všední dny každých 15 minut. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Běh každých 15 minut ve všední dny, od 9:00 do 16:45. |
| `{"weekDays":["sunday"]}` |Běhvá v neděli v čase startu. |
| `{"weekDays":["tuesday", "thursday"]}` |Spustit v úterý a ve čtvrtek v době zahájení. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Spustit v 6 hodin ráno na 28 den každého měsíce (za **předpokladu,** že frekvence "měsíc"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Spustit v 6 hodin poslední den v měsíci.<br /><br />Pokud chcete spustit úlohu poslední den v měsíci, použijte -1 namísto dne 28, 29, 30 nebo 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Spustit v 6 hodin ráno na první a poslední den každého měsíce. |
| `{monthDays":[1,-1]}` |Spustit na první a poslední den každého měsíce v době zahájení. |
| `{monthDays":[1,14]}` |Spustit na první a čtrnáctý den každého měsíce v době zahájení. |
| `{monthDays":[2]}` |Spustit druhý den v měsíci v době zahájení. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spustit na první pátek v měsíci v 5 hodin ráno. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spustit na první pátek v měsíci v době zahájení. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Spustit na třetí pátek od konce měsíce, každý měsíc, v době zahájení. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spuštění v každý první a poslední pátek v měsíci v 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spustit na první a poslední pátek v měsíci v době zahájení. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Běžet pátý pátek v měsíci v době zahájení.<br /><br />Pokud není pátý pátek v měsíci, úloha neběží. Můžete zvážit použití -1 místo 5 pro výskyt, pokud chcete spustit úlohu na poslední vyskytující se pátek v měsíci. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Spuštění v poslední pátek v měsíci každých 15 minut. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Spuštění každou třetí středu v měsíci v 5:15, 5:45, 17:15 a 17:45. |

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)