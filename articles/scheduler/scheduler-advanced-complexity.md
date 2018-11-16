---
title: Vytvářet plány pokročilé úlohy a opakování – Azure Scheduleru
description: Zjistěte, jak vytvořit upřesnění plánování a opakování pro úlohy v Azure Scheduleru
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: be3f8ddaf9788eb9023ffc2caf2e0d6aeb49bdba
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712054"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Vytvářejte upřesnění plánování a opakování pro úlohy v Azure Scheduleru

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje službu Azure Scheduler, která se vyřazuje z provozu. K plánování úloh [místo ní zkuste použít Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

V rámci [Azure Scheduleru](../scheduler/scheduler-intro.md) úlohy, plán je core, která určuje, kdy a jak službu Plánovač spustí úlohu. Můžete nastavit více jednorázové nebo opakované plány pro úlohu s plánovačem. Jednorázové plány spustit pouze jednou v zadaný čas a jsou v podstatě opakující se plány, které se spustí pouze jednou. V zadaných intervalech spustit opakovaných plánů. Tento flexibilní Plánovač lze použít pro různé obchodní scénáře, například:

* **Vyčištění dat pravidelně**: vytvoření každodenní úlohu, která odstraní všechny tweety, které jsou starší než tři měsíce.

* **Archivace dat**: vytvořit úlohu spouštěnou měsíčně, který nabízených oznámení v ceně fakturační historie ke službě zálohování.

* **Externí data žádosti**: vytvoření úlohy, která se spouští každých 15 minut a z NOAA si vyžádá novou sestavu o počasí.

* **Zpracování obrázků**: Vytvořte úlohu den v týdnu, který spustí během hodin mimo špičku a použije cloud computingu pro kompresi Image se odeslaly během dne.

Tento článek popisuje Příklad úloh můžete vytvořit pomocí plánovače a [REST API služby Azure Scheduler](/rest/api/scheduler)a obsahuje definici zápis JSON (JavaScript Object) pro každý plán. 

## <a name="supported-scenarios"></a>Podporované scénáře

Tyto příklady ukazují řadu scénářů, které podporuje Azure Scheduleru a jak vytvořit plány pro různé vzorce chování, například:

* Spusťte jednou v určité datum a čas.
* Spuštění a opakovat s konkrétním počtem opakování.
* Okamžité spuštění a opakovat.
* Spuštění a opakovat každých *n* minuty, hodiny, dny, týdny nebo měsíců, počínaje v určitém čase.
* Spuštění a opakovat týdně nebo měsíčně, ale jenom v určité dny v týdnu nebo v určité dny v měsíci.
* Spuštění a opakovat maximálně jednou za určité období. Například každý měsíc na poslední pátek a pondělí, nebo každý den v 5:15:00 a v 17:15:00.

Tento článek popisuje později tyto scénáře podrobněji.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Vytvoření plánu pomocí rozhraní REST API

Chcete-li vytvořit základní plán se [REST API služby Azure Scheduler](/rest/api/scheduler), postupujte podle těchto kroků:

1. Zaregistrovat vaše předplatné u poskytovatele prostředků pomocí [zaregistrovat operace – REST API Resource Manageru](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register). Název zprostředkovatele pro službu Azure Scheduler je **Microsoft.Scheduler**. 

1. Vytvořit kolekci úloh s použitím [operace vytvoření nebo aktualizace kolekcí úloh](https://docs.microsoft.com/rest/api/scheduler/jobcollections#JobCollections_CreateOrUpdate) v rozhraní REST API Scheduleru. 

1. Vytvoření úlohy s použitím [operaci vytváření nebo aktualizace pro úlohy](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Prvky schématu úlohy

Tato tabulka obsahuje podrobný přehled hlavních elementů JSON, který můžete použít při nastavování opakování a plány pro úlohy. 

| Element | Požaduje se | Popis | 
|---------|----------|-------------|
| **startTime** | Ne | Hodnotu řetězce data a času v [formátu ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) , která určuje, kdy úloha nejprve spustí v plánu basic. <p>U složitějších plánů úloha spustí dřív než **startTime**. | 
| **recurrence** | Ne | Opakování pravidla po spuštění úlohy. **Opakování** objekt podporuje tyto elementy: **frekvence**, **interval**, **plán**, **počet**, a **endTime**. <p>Pokud používáte **opakování** elementu, je třeba použít také **frekvence** elementu, zatímco jiné **opakování** prvky jsou volitelné. |
| **frequency** | Ano, pokud použijete **opakování** | Časová jednotka mezi výskyty a podporuje tyto hodnoty: "Minute", "Hour", "Day", "Týden", "Měsíc" a "Rok" | 
| **interval** | Ne | Celé kladné číslo určující počet časových jednotek mezi opakování na základě **frekvence**. <p>Například pokud **interval** je 10 a **frekvence** hodnotu "Týden", úloha se opakuje každé 10 týdny. <p>Tady je největší počet intervalů pro každý frekvence: <p>-18 měsíců <br>-78 týdnů <br>-548 dnů <br>-Pro hodiny a minuty, rozsah je 1 < = <*interval*>< = 1 000. | 
| **schedule** | Ne | Definuje změny opakování podle zadané minuty – značky, hodinu, dny v týdnu a dny v měsíci | 
| **count** | Ne | Kladné celé číslo, která určuje počet případů, kdy se úloha spustí před dokončením. <p>Například, pokud má každodenní úlohu **počet** nastavená na 7 a počáteční datum je od pondělí, úlohu dokončí spuštěné v neděli. Pokud už uplynul počáteční datum, při prvním spuštění se počítá od času vytvoření. <p>Bez **endTime** nebo **počet**, neomezeně spuštění úlohy. Nelze použít obě **počet** a **endTime** ve stejné úloze, ale pravidlo, že dokončení nejprve zachovaný. | 
| **endTime** | Ne | Řetězcovou hodnotu datum nebo datum a čas v [formátu ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) , která určuje, kdy se zastaví úlohu spuštění. Můžete nastavit hodnotu pro **endTime** , který je v minulosti. <p>Bez **endTime** nebo **počet**, neomezeně spuštění úlohy. Nelze použít obě **počet** a **endTime** ve stejné úloze, ale pravidlo, že dokončení nejprve zachovaný. |
|||| 

Například toto schéma JSON popisuje základní plánu a opakování pro úlohu: 

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

*Data a hodnoty data a času*

* Data v Plánovači úloh zahrnují pouze datum a postupujte podle [specifikace formátu ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

* Data a časy v Plánovači úloh uveďte datum a čas, postupujte [specifikace formátu ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)a budou považovat za čas UTC-li zadána žádná posun UTC. 

Další informace najdete v tématu [koncepty, terminologie a entity](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Podrobnosti: položka startTime

Tato tabulka popisuje, jak **startTime** řídí způsob, jakým úloha běží:

| startTime | Žádná opakování | Opakování bez plánu | Opakování s plánem |
|-----------|---------------|-------------------------|--------------------------|
| **Žádné počáteční čas** | Spusťte jednou okamžitě. | Spusťte jednou okamžitě. Spuštění vyšší výpočtu z posledního času spuštění. | Spusťte jednou okamžitě. Spuštění později na základě plánu opakování. | 
| **Počáteční čas v minulosti** | Spusťte jednou okamžitě. | Výpočet budoucí prvního spuštění po čase začátku a v daném čase spustit. <p>Spuštění vyšší výpočtu z posledního času spuštění. <p>Podívejte se na příklad za touto tabulkou. | Spuštění úlohy *až po* zadaný čas začátku. První výskyt vychází z plánu vypočítaného z času začátku. <p>Spuštění později na základě plánu opakování. | 
| **Počáteční čas v budoucnosti nebo aktuální čas** | Spusťte jednou v zadaný čas začátku. | Spusťte jednou v zadaný čas začátku. <p>Spuštění vyšší výpočtu z posledního času spuštění. | Spuštění úlohy *až po* zadaný čas začátku. První výskyt vychází z plánu a je vypočítaný z času začátku. <p>Spuštění později na základě plánu opakování. |
||||| 

Předpokládejme v tomto příkladu s těmito podmínkami: počáteční čas v minulosti se počet opakování, ale žádný plán.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Aktuální datum a čas je v 13:00 08. dubna 2015.

* Počáteční datum a čas je na 2:00 PM, což je před aktuálním datem a časem 07. dubna 2015.

* Opakování je každé dva dny.

1. Za těchto podmínek dojde k prvnímu spuštění je 09. dubna 2015 ve 14:00. 

   Plánovače vypočítá výskyty spuštění na základě času začátku zahodí všechny instance v minulosti a použije další instanci v budoucnu. 
   V takovém případě **startTime** je 07. dubna 2015 ve 14:00:00, takže další instance nastane o dva dny později, tedy 09. dubna 2015 ve 14:00.

   Dojde k prvnímu spuštění je stejný, zda **startTime** je 2015-04-05 14:00 nebo 2015-04-01 14:00. Po prvním spuštění vyšší spuštění se počítají na základě plánu. 
   
1. Provedení postupujte v tomto pořadí: 
   
   1. 2015-04-11 ve 14:00
   1. 2015-04-13 ve 14:00 
   1. 2015-04-15 ve 14:00
   1. a tak dále.

1. A konečně, když má plánu, ale žádné zadané hodiny a minuty, tyto výchozí hodnoty na hodiny a minuty v prvním spuštění se v uvedeném pořadí.

<a name="schedule"></a>

## <a name="details-schedule"></a>Podrobnosti: plán

Můžete použít **plán** k *limit* počet spuštění úlohy. Například, pokud úlohy s **frekvence** má "měsíc" plán, který se spustí jenom v 31. den, úloha se spustí jenom v měsících, které mají 31 dní.

Můžete také použít **plán** k *rozbalte* počet spuštění úlohy. Například, pokud úlohy s **frekvence** má "měsíc" plán, který se spouští v 1 a 2, úloha běží na první a druhý den v měsíci namísto pouze jednou za měsíc.

Pokud chcete zadat více než jeden element plán, pořadí jejich vyhodnocování postupuje od největšího po nejmenší: čísla týdne, měsíc a den, den v týdnu, hodiny a minuty.

Následující tabulka obsahuje podrobný popis elementů plánu:

| Název JSON | Popis | Platné hodnoty |
|:--- |:--- |:--- |
| **minutes** |Minuty v hodině, kdy úloha běží. |Pole celých čísel. |
| **hours** |Hodin dne, kdy úloha běží. |Pole celých čísel. |
| **weekDays** |Dny v týdnu, úloha běží. Je možné zadat jenom při týdenní frekvenci. |Pole některý z následujících hodnot (maximální velikost pole je 7):<br />-"Pondělí"<br />-"Úterý"<br />-"Středa"<br />-"Čtvrtek"<br />-"Pátek"<br />-"Sobota"<br />-"Neděle"<br /><br />Není malá a velká písmena. |
| **monthlyOccurrences** |Určuje, které dny v měsíci se úloha spustí. Je možné zadat jenom při měsíční frekvenci. |Pole **monthlyOccurrences** objekty:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **den** představuje den v týdnu, spouští úlohy. Například *{neděle}* určuje každou neděli v měsíci. Povinná hodnota.<br /><br />**výskyt** je výskyt dne v měsíci. Například *{neděle, -1}* je poslední neděle v měsíci. Volitelné. |
| **monthDays** |Den v měsíci, kdy úloha běží. Je možné zadat jenom při měsíční frekvenci. |Pole následujících hodnot:<br />– Libovolná hodnota <= −1 a >= −31<br />– Libovolná hodnota >= 1 a <= 31|

## <a name="examples-recurrence-schedules"></a>Příklady: Plány opakování

Následující příklady ukazují různé plány opakování. V příkladech se zaměřit na objekt plánu a jeho dílčí elementy.

Tyto plány se předpokládá, že **interval** je nastavená na 1\. Příklady také Předpokládejme správnou **frekvence** hodnot pro hodnoty v **plán**. Například nelze použít **frekvence** "Day" a mít **hodnoty** změny v **plán**. Popisujeme tato omezení dříve v tomto článku.

| Příklad: | Popis |
|:--- |:--- |
| `{"hours":[5]}` |Spusťte v 5: 00 každý den.<br /><br />Plánovač odpovídá každé hodnoty v "hours" s každou hodnotu v "minutes", jeden po druhém, chcete-li vytvořit seznam všech případů, kdy úloha běží. |
| `{"minutes":[15], "hours":[5]}` |Spuštění každý den v 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Spuštění každý den v 5:15 a 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Spuštění každý den v 5:15, 5:45 a 17:15 a 17:45. |
| `{"minutes":[0,15,30,45]}` |Spuštění každých 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Spuštění každou hodinu.<br /><br />Tato úloha se spouští každou hodinu. Minutu řídí hodnota **startTime**, pokud je zadán. Pokud ne **startTime** není zadána hodnota, minuty se řídí časem vytvoření. Například, pokud čas začátku nebo čas vytvoření (podle situace) je 12:25 hodin, úloha se spustí v 00:25, 01:25, 02:25,..., 23:25.<br /><br />Plán je stejný jako úloha s **frekvence** "hodina", **interval** 1 a žádným **plán** hodnotu. Rozdíl je, že můžete použít tento plán s různými **frekvence** a **interval** hodnoty pro vytvoření jiných úloh. Například pokud **frekvence** je "měsíc", plán se spustí pouze jednou za měsíc, ne každý den (Pokud **frekvence** je "day"). |
| `{minutes:[0]}` |Spuštění v každou celou hodinu.<br /><br />Tato úloha také spouští každou hodinu, ale v celou hodinu (12 AM, 1 AM, 2 AM a tak dále). Tento plán je stejný jako úloha s **frekvence** "hodina", **startTime** hodnotou nula minut a žádným **plán**, pokud je frekvence "den". Ale pokud **frekvence** je "týden" nebo "měsíc" plán se spustí pouze jeden den v týdnu nebo jeden den v měsíci, v uvedeném pořadí. |
| `{"minutes":[15]}` |Spuštění 15 minut po hodině, každou hodinu.<br /><br />Spouští každou hodinu od 00:15 AM, 1:15:00, 2:15:00, a tak dále. Končí 23:15:00. |
| `{"hours":[17], "weekDays":["saturday"]}` |Spuštění každý týden v 17: 00 v sobotu. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění každý týden v 17: 00 v pondělí, středu a pátek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění každý týden v pondělí, středu a pátek v 17:15 a 17:45. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění každý týden v 5: 00 a 17: 00 v pondělí, středu a pátek. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění v 5:15, 5:45:00, 17:15 a 17:45:00 v pondělí, středu a pátek každý týden. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spuštění ve všední dny každých 15 minut. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spuštění každých 15 minut ve všední dny mezi 9: 00 a 16:45:00. |
| `{"weekDays":["sunday"]}` |Spouštět v neděli v době spuštění. |
| `{"weekDays":["tuesday", "thursday"]}` |Spuštění každé úterý a čtvrtek v době spuštění. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Spustit v 6: 00 28. dne v měsíci (za předpokladu, že **frekvence** "měsíc"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Spusťte v 6: 00, poslední den v měsíci.<br /><br />Pokud chcete spustit úlohu na poslední den v měsíci, použijte místo 28, 29, 30 nebo 31 hodnotu -1. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Spusťte v 6: 00 na první a poslední den v měsíci. |
| `{monthDays":[1,-1]}` |Spusťte na první a poslední den v měsíci v době spuštění. |
| `{monthDays":[1,14]}` |Spusťte na první a 14. den v měsíci v době spuštění. |
| `{monthDays":[2]}` |Spusťte druhý den v měsíci v době spuštění. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spuštění v každý první pátek v měsíci v 5: 00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spuštění v každý první pátek v měsíci v době spuštění. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Spuštění v každý třetí pátek od konce měsíce, každého měsíce v zadaný čas začátku. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spuštění v každý první a poslední pátek v měsíci v 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spusťte při spuštění na první a poslední pátek v měsíci. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Spuštění v každý pátý pátek v měsíci v počáteční čas.<br /><br />Pokud není nemá pátý pátek v měsíci, úloha se nespustí. Můžete zvážit použití -1 místo hodnoty 5 výskyt, pokud chcete spustit úlohu posledního výskytu pátek v měsíci. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Spuštění v poslední pátek v měsíci každých 15 minut. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Spuštění každou třetí středu v měsíci v 5:15, 5:45, 17:15 a 17:45. |

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)