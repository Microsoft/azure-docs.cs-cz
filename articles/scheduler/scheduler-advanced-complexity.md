---
title: Sestavení pokročilých plánů a opakování úloh
description: Naučte se vytvářet pokročilé plány a opakování pro úlohy v Azure Scheduleru.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368175"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Sestavování pokročilých plánů a opakování úloh v Azure Scheduleru

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

V rámci úlohy [Azure Scheduleru](../scheduler/scheduler-intro.md) je plán jádrem, který určuje, kdy a jak služba plánovače úlohu spouští. Pro úlohu s plánovačem můžete nastavit několik jednorázových a opakovaných plánů. Jednorázové plány se spouští jenom jednou v zadaném čase a jsou v podstatě opakující se plány, které se spouštějí jenom jednou. Opakující se plány běží na zadané frekvenci. Díky této flexibilitě můžete použít Scheduler pro různé obchodní scénáře, například:

* **Pravidelně čistit data**: Vytvořte každodenní úlohu, která odstraní všechny tweety starší než tři měsíce.

* **Archivovaná data**: Vytvořte měsíční úlohu, která vloží historii faktury do zálohovací služby.

* **Požadovat externí data**: vytvořte úlohu, která se spouští každých 15 minut, a vyžádá si novou zprávu o počasí z NOAA.

* **Image procesů**: můžete vytvořit úlohu v týdnu, která běží v době mimo špičku, a používá Cloud Computing pro komprimaci imagí odeslaných během dne.

Tento článek popisuje příklady úloh, které můžete vytvořit pomocí plánovače a [služby Azure scheduler REST API](/rest/api/scheduler), a obsahuje definici JavaScript Object Notation (JSON) pro každý plán. 

## <a name="supported-scenarios"></a>Podporované scénáře

Tyto příklady znázorňují rozsah scénářů, které Azure Scheduler podporuje, a postup pro vytváření plánů pro různé vzory chování, například:

* Spustí se jednou v konkrétní datum a čas.
* Spuštění a opakování určitého počtu opakování.
* Spusťte okamžitě a znovu se opakuje.
* Spuštění a opakování každých *n* minut, hodin, dnů, týdnů nebo měsíců, a to od určitého času.
* Spustit a opakovat každý týden nebo každý měsíc, ale jenom v konkrétní dny v týdnu nebo v konkrétní dny v měsíci.
* Spuštění a opakování více než jednou pro konkrétní období. Například každý měsíc v posledním pátek a pondělí nebo každý den v 5:15. a v 5:15 ODP.

Tento článek později popisuje tyto scénáře podrobněji.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Vytvořit plán s REST API

K vytvoření plánu Basic pomocí [REST API Azure Scheduleru](/rest/api/scheduler)použijte následující postup:

1. Zaregistrujte své předplatné Azure u poskytovatele prostředků pomocí [REST API operace Register-správce prostředků](/rest/api/resources/providers). Název poskytovatele služby Azure Scheduler je **Microsoft. Scheduler**. 

1. Vytvořte kolekci úloh pomocí [operace vytvořit nebo aktualizovat pro kolekce úloh](/rest/api/scheduler/jobcollections) v plánovači REST API. 

1. Vytvořte úlohu pomocí [operace vytvořit nebo aktualizovat pro úlohy](/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Prvky schématu úlohy

Tato tabulka poskytuje podrobný přehled hlavních elementů JSON, které můžete použít při nastavování opakování a plánů pro úlohy. 

| Prvek | Povinné | Popis | 
|---------|----------|-------------|
| **Spuštění** | No | Hodnota řetězce DateTime ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , která určuje, kdy se úloha poprvé spustí podle plánu Basic. <p>U složitých plánů úloha začíná dřív než **StartTime**. | 
| **vzorec** | No | Pravidla opakování při spuštění úlohy. Objekt **opakování** podporuje tyto prvky: **četnost**, **interval**, **plán**, **počet** a **čas** ukončení. <p>Použijete-li element **opakování** , je nutné také použít prvek **frekvence** , zatímco jiné prvky **opakování** jsou volitelné. |
| **opakování** | Ano, při použití **opakování** | Časová jednotka mezi výskyty a podporuje tyto hodnoty: "minuta", "hodina", "den", "týden", "Month" a "Year" | 
| **doba** | No | Celé kladné číslo určující počet časových jednotek mezi výskyty na základě **frekvence**. <p>Pokud má například **interval** hodnotu 10 a **frekvence** "týden", úloha se opakuje každých 10 týdnů. <p>Toto je nejvyšší počet intervalů pro každou frekvenci: <p>– 18 měsíců <br>– 78 týdnů <br>– 548 dní <br>– Pro hodiny a minuty je rozsah 1 <= <*intervalu*> <= 1000. | 
| **CXL** | No | Definuje změny opakování na základě zadaných minutových značek, hodinových značek, dnů v týdnu a dnů v měsíci. | 
| **count** | No | Kladné celé číslo, které určuje počet pokusů o spuštění úlohy před dokončením. <p>Pokud je například denní úloha v poli **počet** nastavená na hodnotu 7 a počáteční datum je pondělí, úloha skončí běžet v neděli. Pokud počáteční datum již prošlo, je první spuštění vypočítáno z času vytvoření. <p>Aniž byste měli **čas** ukončení ani **počet**, úloha se spustí nekonečně. V rámci stejné úlohy nemůžete použít současně jak **Count** , tak i **čas** , ale pravidlo, které se dokončí jako první, se respektuje. | 
| **endTime** | No | Hodnota řetězce Date nebo DateTime ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , která určuje, kdy se úloha přestane spouštět. Můžete nastavit hodnotu pro **čas** ukončení, který je v minulosti. <p>Aniž byste měli **čas** ukončení ani **počet**, úloha se spustí nekonečně. V rámci stejné úlohy nemůžete použít současně jak **Count** , tak i **čas** , ale pravidlo, které se dokončí jako první, se respektuje. |
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

* Data v úlohách Scheduleru obsahují pouze datum a postupují podle [Specifikace ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Datum a čas v úlohách Scheduleru zahrnují datum i čas, dodržuje [specifikaci ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)a předpokládá se, že se jedná o čas UTC, pokud není zadaný žádný posun UTC. 

Další informace najdete v tématu [Koncepty, terminologie a entity](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Podrobnosti: Čas_spuštění

Tato tabulka popisuje, jak **StartTime** řídí způsob spuštění úlohy:

| startTime | Bez opakování | Opakování, žádný plán | Opakování s plánem |
|-----------|---------------|-------------------------|--------------------------|
| **Žádný počáteční čas** | Spustit ihned. | Spustit ihned. Spustit pozdější spuštění vypočítané z posledního času spuštění. | Spustit ihned. Spusťte pozdější spuštění na základě plánu opakování. | 
| **Čas začátku v minulosti** | Spustit ihned. | Vypočítá první čas spuštění v budoucnosti po počátečním čase a spustí se v daném čase. <p>Spustit pozdější spuštění vypočítané z posledního času spuštění. <p>Podívejte se na příklad za touto tabulkou. | Spustit úlohu *ne dřív než* zadaný počáteční čas. První výskyt vychází z plánu vypočítaného z času začátku. <p>Spusťte pozdější spuštění na základě plánu opakování. | 
| **Čas spuštění v budoucnosti nebo aktuální čas** | Spustí se jednou v zadaném počátečním čase. | Spustí se jednou v zadaném počátečním čase. <p>Spustit pozdější spuštění vypočítané z posledního času spuštění. | Spustit úlohu *ne dřív než* zadaný počáteční čas. První výskyt vychází z plánu a je vypočítaný z času začátku. <p>Spusťte pozdější spuštění na základě plánu opakování. |
||||| 

Předpokládejme, že tento příklad s těmito podmínkami: čas zahájení v minulosti s opakováním, ale žádný plán.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Aktuální datum a čas je 8. dubna 2015 na 1:00 odp.

* Počáteční datum a čas jsou 07. dubna 2015 na 2:00 PM, což předchází aktuálnímu datu a času.

* Opakování je každé dva dny.

1. Za těchto podmínek se první spuštění vyplatí od 09 do 9. dubna 2015 na 2:00 odp. 

   Scheduler vypočítá výskyty spuštění založené na čase spuštění, zahodí všechny instance v minulosti a v budoucnu používá další instanci. 
   V tomto případě je **čas StartTime** 15. dubna 2015 na 2:00 PM, takže další instance je od té doby dvě dny, což je 9. dubna 2015 na 2:00 odp.

   První spuštění je stejné, bez ohledu na to, jestli je **čas_spuštění** 2015-04-05 14:00 nebo 2015-04-01 14:00. Po prvním spuštění se pozdější spuštění vypočítají na základě plánu. 
   
1. Spuštění pak bude následovat v tomto pořadí: 
   
   1. 2015-04-11 na 2:00 odp.
   1. 2015-04-13 na 2:00 odp. 
   1. 2015-04-15 na 2:00 odp.
   1. A tak dále...

1. Nakonec platí, že pokud má úloha plán, ale ne zadané hodiny a minuty, tyto hodnoty se ve výchozím nastavení vyhodnotí jako hodiny a minuty v prvním spuštění, v uvedeném pořadí.

<a name="schedule"></a>

## <a name="details-schedule"></a>Podrobnosti: plán

Pomocí **plánu** můžete *omezit* počet spuštění úloh. Pokud má například úloha s **frekvencí** "Month" plán, který se spouští jenom na 31. den, úloha se spustí jenom v měsících, které mají 31 den.

Pomocí **plánu** můžete také *rozšířit* počet spuštění úloh. Pokud má například úloha s **frekvencí** "Month" plán, který se spouští v měsících 1 a 2, úloha se spustí první a druhý den v měsíci, nikoli jenom jednou za měsíc.

Pokud zadáte více než jeden prvek plánování, pořadí vyhodnocení bude od největšího po nejmenší: číslo týdne, měsíc, den v týdnu, hodina a minuta.

Následující tabulka obsahuje podrobný popis elementů plánu:

| Název JSON | Description | Platné hodnoty |
|:--- |:--- |:--- |
| **minuty** |Minuty hodiny, kdy se úloha spouští. |Pole celých čísel. |
| **hodin** |Hodiny dne, kdy se úloha spouští. |Pole celých čísel. |
| **weekDays** |Dny v týdnu, kdy úloha běží. Dá se zadat jenom s týdenní frekvencí. |Pole některé z následujících hodnot (maximální velikost pole je 7):<br />– "Pondělí"<br />– "Úterý"<br />– "Středa"<br />-"Čtvrtek"<br />-"Pátek"<br />-"Sobota"<br />-"Neděle"<br /><br />Nerozlišuje velká a malá písmena. |
| **monthlyOccurrences** |Určuje, které dny v měsíci je úloha spuštěna. Dá se zadat jenom s měsíční frekvencí. |Pole objektů **monthlyOccurrences** :<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **Day** je den v týdnu, kdy se úloha spouští. Například *{neděle}* je každou neděli v měsíci. Povinná hodnota.<br /><br />**výskyt** je výskyt dne v měsíci. Například  *{neděle,-1}* je poslední neděle v měsíci. Nepovinný parametr. |
| **monthDays** |Den v měsíci, kdy se úloha spouští. Dá se zadat jenom s měsíční frekvencí. |Pole s následujícími hodnotami:<br />– Libovolná hodnota <= −1 a >= −31<br />– Libovolná hodnota >= 1 a <= 31|

## <a name="examples-recurrence-schedules"></a>Příklady: plány opakování

Následující příklady znázorňují různé plány opakování. Příklady jsou zaměřeny na objekt Schedule a jeho dílčí prvky.

V těchto plánech se předpokládá, že je tento **interval** nastavený na hodnotu 1.\. Příklady také předpokládají správné hodnoty **frekvence** pro hodnoty v **plánu**. Například nemůžete použít **frekvenci** "Day" (den) a **monthDays** změnu v **plánu**. Tato omezení jsme popsali výše v článku.

| Příklad | Description |
|:--- |:--- |
| `{"hours":[5]}` |Spusťte každý den v intervalu 5 dop.<br /><br />Scheduler porovnává každou hodnotu v řádu "hours" s každou hodnotou v hodnotě "minuty" a vytvoří seznam všech časů spuštění úlohy. |
| `{"minutes":[15], "hours":[5]}` |Spuštění každý den v 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Spuštění každý den v 5:15 a 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Spuštění každý den v 5:15, 5:45 a 17:15 a 17:45. |
| `{"minutes":[0,15,30,45]}` |Spuštění každých 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Spuštění každou hodinu.<br /><br />Tato úloha se spouští každou hodinu. Minuta se řídí hodnotou pro **StartTime**, pokud je zadaná. Pokud není zadána žádná hodnota **StartTime** , je minuta řízena časem vytvoření. Pokud je například čas spuštění nebo čas vytvoření (případná částka) 12:25 ODP., úloha se spustí v 00:25, 01:25, 02:25,..., 23:25.<br /><br />Plán je stejný jako úloha s **frekvencí** "hodina", **intervalem** 1 a hodnotou **plánu** . Rozdílem je, že k vytvoření dalších úloh můžete použít tento plán s různými hodnotami **frekvence** a **intervalů** . Například pokud je **počet_plateb** "Month", plán se spustí pouze jednou měsíčně místo každého dne (Pokud je **frekvence** "Day"). |
| `{minutes:[0]}` |Spuštění v každou celou hodinu.<br /><br />Tato úloha se také spouští každou hodinu, ale v hodině (12 DOP, 1 dop. 2 dop atd.). Tento plán je stejný jako úloha s **frekvencí** "hodina", hodnotou **StartTime** 0 minut a bez **plánu**, pokud je frekvence "Day". Pokud je však **frekvence** "Week" nebo "Month", plán se spustí pouze jeden den v týdnu nebo jeden den v měsíci. |
| `{"minutes":[15]}` |Spustí se 15 minut po každé hodině.<br /><br />Spustí se každou hodinu, od 00:15 do 1:15 dopoledne, 2:15 a tak dále. Končí na 11:15 PM. |
| `{"hours":[17], "weekDays":["saturday"]}` |Každý týden spusťte v sobotu za 5 hodin. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Každý týden spouštějte v pondělí, středu a pátek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění každý týden v pondělí, středu a pátek v 17:15 a 17:45. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spouštějte každý týden v pondělí, středu a pátek v 8:00 a 5 odp. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spusťte každý týden v pondělí, středu a pátek v 5:15 dop. 5:45 AM, 5:15 ODP. a 5:45 odp. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spuštění ve všední dny každých 15 minut. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spouští se každých 15 minut v pracovních dnech od 9:00 do 4:45./odp.. |
| `{"weekDays":["sunday"]}` |Spustí se v neděli v čase spuštění. |
| `{"weekDays":["tuesday", "thursday"]}` |V počátečním čase spouštějte v úterý a v čtvrtek. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Spustí se v rozmezí od 6 do 28 dne v měsíci (za předpokladu, že je k dispozici **frekvence** "měsíc"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Běžet v rozmezí od 6. až do poslední den v měsíci.<br /><br />Pokud chcete úlohu spustit poslední den v měsíci, použijte hodnotu-1 namísto dne 28, 29, 30 nebo 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Spustí se v 6. první a poslední den v každém měsíci. |
| `{monthDays":[1,-1]}` |Spustí se první a poslední den v měsíci v počátečním čase. |
| `{monthDays":[1,14]}` |Spustí se v prvním a 14 dni v měsíci v počátečním čase. |
| `{monthDays":[2]}` |Spustí se v druhém dni v měsíci v počátečním čase. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spustí se první pátek v měsíci v intervalu 5 dop. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spustí se první pátek v měsíci v počátečním čase. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Spustí se třetí pátek od konce měsíce v počátečním čase. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spuštění v každý první a poslední pátek v měsíci v 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spustí se v prvním a posledním pátek v měsíci v počátečním čase. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Spustí se pátý pátek v měsíci v počátečním čase.<br /><br />Pokud v měsíci není pátý pátek, úloha se nespustí. Pokud chcete úlohu spustit v poslední pátek v měsíci, zvažte použití parametru-1 namísto 5. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Spuštění v poslední pátek v měsíci každých 15 minut. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Spuštění každou třetí středu v měsíci v 5:15, 5:45, 17:15 a 17:45. |

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)