---
title: Sestavení komplexních plánů a pokročilé opakování ve službě Azure Scheduler
description: Informace o sestavení komplexních plánů a pokročilé opakování ve službě Azure Scheduler.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692330"
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Sestavení komplexních plánů a pokročilé opakování ve službě Azure Scheduler

Základní úlohu služby Azure Scheduler je plán. Plán Určuje, kdy a jak Scheduler provede úlohu. 

Chcete-li nastavit více jednorázové nebo opakované plány pro úlohu můžete použít Plánovač. Jednorázové plány fire jednou v zadanou dobu. Jednorázové plány jsou efektivně opakující se plány které provést pouze jednou. Opakující se plány fire na předem určený frekvenci.

S tuto flexibilitu potřebují můžete použít Plánovač pro celou řadu obchodních scénářů:

* **Vyčištění periodických dat**. Každý den, například odstraňte všechny tweetů, které jsou starší než tři měsíce.
* **Archivace**. Například každý měsíc, nabízenou faktury historie ke službě zálohování.
* **Požadavky na externí data**. Například každých 15 minut, načítat nové sestavy počasí identifikátor klíče subjektu z NOAA.
* **Zpracování obrázků**. Každý den v týdnu, hodiny mimo špičku, například použijte cloud computing komprimovat bitové kopie, které byly odeslány daný den.

V tomto článku jsme provede Příklad úloh, které můžete vytvořit pomocí plánovače. Poskytujeme data JSON, který popisuje každý plán. Pokud použijete [REST API Scheduleru](https://msdn.microsoft.com/library/mt629143.aspx), můžete použít tento stejný formát JSON na [vytváření úlohy plánovače](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Podporované scénáře
V příkladech v tomto článku znázorňují šířky scénáře, které podporuje plánovače. Příklady široce ukazují, jak vytvořit plány pro mnoha způsoby chování, včetně:

* Spusťte jednou v určité datum a čas.
* Spusťte a opakovat konkrétní počet opakování.
* Spustit okamžitě a opakovat.
* Spuštění a opakovat každých  *n*  minuty, hodiny, dny, týdny nebo měsíce, spouštění v určitém čase.
* Spusťte a opakovat týdenní nebo měsíční četností, ale jenom na konkrétní dny v týdnu nebo na konkrétní dny v měsíci.
* Spusťte a opakovat vícekrát v časovém období. Například, poslední pátek a poslední pondělí v každém měsíci, nebo v 5:15:00 a 17:15:00 každý den.

## <a name="date-and-date-time"></a>Datum a datum a čas
Datum odkazů v Plánovači úloh postupujte podle kroků [specifikace formátu ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)a zahrnovat pouze data.

Postupujte podle data a času odkazů v Plánovači úloh [specifikace formátu ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)a obsahovat datum a čas. Datum a čas, který není zadat posun UTC se považuje za čas UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Vytvoření plánu použít JSON a rozhraní REST API
Vytvoření základního plánu pomocí [REST API Scheduleru](https://msdn.microsoft.com/library/mt629143), první [zaregistrovat předplatné zprostředkovatele prostředků](https://msdn.microsoft.com/library/azure/dn790548.aspx). Název zprostředkovatele pro Scheduler je **Microsoft.Scheduler**. Potom [vytvořit kolekci úloh](https://msdn.microsoft.com/library/mt629159.aspx). Nakonec [vytvořit úlohu](https://msdn.microsoft.com/library/mt629145.aspx). 

Když vytvoříte úlohu, můžete určit pomocí formát JSON, jako je třeba v této výňatek ze plánování a opakování:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Základní informace o schématu úlohy
Následující tabulka obsahuje přehled hlavních prvků, které se používá k nastavení opakování a plánování v rámci úlohy:

| Název JSON | Popis |
|:--- |:--- |
| **startTime** |Hodnota, datum a čas. Pro základní plány **startTime** je první výskyt. Pro komplexní plány úlohy spustí dřív než **startTime**. |
| **recurrence** |Určuje pravidla opakování pro úlohy a opakování, kdy má být úloha spuštěna. Objekt opakování podporuje elementy **frekvence**, **interval**, **endTime**, **počet**, a **plán**. Pokud **opakování** je definován **frekvence** je vyžadován. Další **opakování** prvky jsou volitelné. |
| **frequency** |Řetězec, který představuje jednotku frekvence, kdy úloha opakovat. Podporované hodnoty jsou "minutu", "hodina", "dne", "týden" a "měsíc". |
| **interval** |Kladné celé číslo. **interval** označuje interval **frekvence** hodnota, která určuje, jak často má být úloha spuštěna. Například pokud **interval** 3 a **frekvence** je "týden" úloha opakuje každé tři týdny.<br /><br />Scheduler podporuje maximálně **interval** 18 pro měsíční frekvenci, 78 frekvence týdenní a 548 pro denní četnost. Pro hodinu a minutu četnost podporovaný rozsah je 1 < = **interval** < = 1 000. |
| **endTime** |Řetězec, který určuje datum a čas, jejichž překročení úlohy nefunguje. Můžete nastavit hodnotu pro **endTime** , je v minulosti. Pokud **endTime** a **počet** nejsou zadané, úloha běží nekonečnou. Nemůže současně obsahovat **endTime** a **počet** ve stejné úloze. |
| **Počet** |Kladné celé (větší než nula) určuje, kolikrát má být úloha spuštěna před dokončením.<br /><br />**počet** představuje počet, kolikrát má být úloha spuštěna dříve, než určuje dokončit. Například pro úlohu, která se spouští každý den **počet** 5 a počáteční datum pondělí, dokončení úlohy po spuštění v pátek. Pokud počáteční datum v minulosti, první spuštění se počítá z čas vytvoření.<br /><br />Pokud žádné **endTime** nebo **počet** není zadaný, úloha běží nekonečnou. Nemůže současně obsahovat **endTime** a **počet** ve stejné úloze. |
| **schedule** |Úlohy v zadaném intervalu mění jeho opakování podle plánu opakování. A **plán** hodnota obsahuje změny na základě minuty, hodiny, dny v týdnu, dny v měsíci a číslo týdne. |

## <a name="job-schema-defaults-limits-and-examples"></a>Výchozí schéma úlohy, omezení a příklady
Dále v tomto článku probereme každý z těchto prvků podrobně:

| Název JSON | Typ hodnoty | Povinné? | Výchozí hodnota | Platné hodnoty | Příklad: |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |řetězec |Ne |Žádné |ISO 8601 datum časy |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |Objekt |Ne |Žádné |Objekt opakování |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |řetězec |Ano |Žádný |"minutu", "hodina", "dne", "týden", "měsíc" |`"frequency" : "hour"` |
| **interval** |číslo |Ano |Žádné |1 až 1000 |`"interval":10` |
| **endTime** |řetězec |Ne |Žádné |Hodnota data a času, který představuje čas v budoucnosti |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Počet** |číslo |Ne |Žádné |>= 1 |`"count": 5` |
| **schedule** |Objekt |Ne |Žádný |Objekt plánu |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Podrobné informace: položka startTime
Následující tabulka popisuje, jak **startTime** řídí způsob, který spouští úlohy:

| Hodnota startTime | Bez opakování | Opakování, žádný plán | Opakování s plánem |
|:--- |:--- |:--- |:--- |
| **Žádný čas spuštění** |Spusťte jednou okamžitě. |Spusťte jednou okamžitě. Spuštění dalších spuštěních vypočítaných z čas posledního spuštění. |Spusťte jednou okamžitě.<br /><br />Spusťte dalších spuštěních podle plánu opakování. |
| **Počáteční čas v minulosti.** |Spusťte jednou okamžitě. |Vypočítat budoucí prvním spuštění po spuštění a v daném čase spustit.<br /><br />Spuštění dalších spuštěních vypočítaných z čas posledního spuštění. <br /><br />Další informace podívejte se na příklad, pod touto tabulkou. |Úloha spustí *ne sooner než* zadaným časem spuštění. První výskyt vychází z plánu vypočítaného z času začátku.<br /><br />Spusťte dalších spuštěních podle plánu opakování. |
| **Počáteční čas v budoucnosti nebo aktuální čas** |Spusťte jednou v určený čas spuštění. |Spusťte jednou v určený čas spuštění.<br /><br />Spuštění dalších spuštěních vypočítaných z čas posledního spuštění.|Úloha spustí *ne sooner než* zadaným časem spuštění. První výskyt je podle plánu, vypočítaných z čas spuštění.<br /><br />Spusťte dalších spuštěních podle plánu opakování. |

Podívejme se na příklad co se stane, když **startTime** je v minulosti s opakování, ale žádný plán.  Předpokládejme, že je aktuální čas 2015-04-08 13:00, **startTime** je 2015-04-07 14:00, a **opakování** je dvou dní (definované s **frekvence**: den a **interval**: 2.) Všimněte si, že **startTime** je v minulosti a dojde k před aktuálním časem.

Za těchto podmínek bude první spuštění na 2015-04-09 v 14:00\. Modul plánovače vypočítá výskyty spuštění na základě času začátku. Všechny instance v minulosti se zahodí. Modul použije další instanci, která nastane v budoucnosti. V takovém případě **startTime** je 2015-04-07 na 2:00 PM, takže další instance je o dva dny od té doby je 2015-04-09 na 2:00 PM.

Upozorňujeme, že první provádění by být stejné zda **startTime** je 2015-04-05 14:00 nebo 2015-04-01 14:00\. Po prvním spuštění se další spuštění vypočítají na základě plánu. Bude mít na 2015-04-11 na 2:00 PM, pak 2015-04-13 na 2:00 PM pak 2015-04-15 ve 2:00 a tak dále.

Nakonec, když má plánu, nastaveného hodinách a minutách nejsou v plánu, výchozí hodnoty hodin a minut první spuštění, v uvedeném pořadí.

## <a name="deep-dive-schedule"></a>Podrobné informace: plán
Můžete použít **plán** k *limit* počet spuštění úlohy. Například, pokud úlohy s **frekvence** "měsíci" obsahuje plán, který funguje pouze na den 31, úloha běží jenom v měsících, které mají třicet první den.

Můžete také použít **plán** k *rozbalte* počet spuštění úlohy. Například, pokud úlohy s **frekvence** "měsíci" obsahuje plán, který běží na dny v měsíci 1 a 2, úloha běží na prvním a druhém dny v měsíci místo pouze jednou za měsíc.

Pokud zadáte více elementů plán, pořadí vyhodnocení je od největších po nejmenší: číslo týden, měsíc, den, den v týdnu, hodin a minut.

Následující tabulka obsahuje podrobný popis elementů plánu:

| Název JSON | Popis | Platné hodnoty |
|:--- |:--- |:--- |
| **minutes** |Počet minut za hodinu, ve kterém má být úloha spuštěna. |Pole celých čísel. |
| **hours** |Hodin dne, kdy má být úloha spuštěna. |Pole celých čísel. |
| **weekDays** |Dny v týdnu, úloha běží. Lze zadat pouze s frekvencí týdně. |Pole kterékoli z následujících hodnot (pole maximální velikost je 7):<br />-"Pondělí"<br />-"Úterý"<br />-"Středa"<br />-"Čtvrtek"<br />-"Pátek"<br />-"Sobota"<br />-"Neděle"<br /><br />Není malá a velká písmena. |
| **monthlyOccurrences** |Určuje, které dny v měsíci úloha běží. Lze zadat pouze s měsíční četností. |Pole **monthlyOccurrences** objekty:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **den** den v týdnu, úloha běží. Například *{neděle}* je každou neděli v měsíci. Povinná hodnota.<br /><br />**výskyt** je výskyt den v měsíci. Například *{neděle, -1}* je poslední neděle v měsíci. Volitelné. |
| **monthDays** |Den v měsíci, který má být úloha spuštěna. Lze zadat pouze s měsíční četností. |Pole z následujících hodnot:<br />-Všechny hodnoty < = -1 a > =-31<br />-Všechny hodnoty > = 1 a < = 31|

## <a name="examples-recurrence-schedules"></a>Příklady: Opakování plány
Následující příklady ukazují různé plány opakování. V příkladech se zaměřit na objekt plánu a jeho dílčí prvky.

Tyto plány předpokládat, že **interval** je nastaven na hodnotu 1\. Příklady také předpokládají správný **frekvence** hodnoty hodnotami v **plán**. Například nemůžete použít **frekvence** "denní" a mají **Prescribed** úpravy v **plán**. Jsme popisují tato omezení dříve v článku.

| Příklad: | Popis |
|:--- |:--- |
| `{"hours":[5]}` |Spusťte na 5: 00 každý den.<br /><br />Scheduler odpovídá až každé hodnoty v "hours" s každou hodnotu ve "minutes", po jednom, můžete vytvořit seznam z všechny časy, kdy má být úloha spuštěna. |
| `{"minutes":[15], "hours":[5]}` |Spuštění každý den v 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Spuštění každý den v 5:15 a 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Spuštění každý den v 5:15, 5:45 a 17:15 a 17:45. |
| `{"minutes":[0,15,30,45]}` |Spuštění každých 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Spuštění každou hodinu.<br /><br />Tato úloha spouští každou hodinu. Minutu řídí hodnota **startTime**, pokud je zadán. Pokud žádné **startTime** je zadána hodnota, řídí minutu čas vytvoření. Například pokud je čas spuštění nebo čas vytvoření (podle toho, co platí) 12:25 hodin, úlohy, které se spouští v 00:25, 01:25 02:25,..., 23:25.<br /><br />Plán je ekvivalentní úlohu s **frekvence** "hodina" **interval** 1, a ne **plán** hodnotu. Rozdíl je, které můžete použít tento plán jiné **frekvence** a **interval** hodnotami pro vytvoření jiné úlohy. Například pokud **frekvence** je "měsíc", je plán spuštěn pouze jednou za měsíc místo každý den (Pokud **frekvence** je "den"). |
| `{minutes:[0]}` |Spuštění v každou celou hodinu.<br /><br />Tato úloha také spouští každou hodinu, ale na hodinu (12 AM, 1 AM, 2 AM a tak dále). Jde o ekvivalent úlohu s **frekvence** "hodina", **startTime** hodnotu nula minut a ne **plán**, pokud je četnost "den". Ale pokud **frekvence** je "týden" nebo "měsíc", plán provede pouze jeden den, týden nebo jednoho dne a měsíce, v uvedeném pořadí. |
| `{"minutes":[15]}` |Spusťte 15 minut po hodině každou hodinu.<br /><br />Spouští každou hodinu od 00:15 AM, 1:15:00, 2:15:00, a tak dále. Ho ukončí ve 23:15:00. |
| `{"hours":[17], "weekDays":["saturday"]}` |Spusťte v 17: 00 na sobotu každý týden. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spusťte v 17: 00 v pondělí, středu a pátek každý týden. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Spuštění každý týden v pondělí, středu a pátek v 17:15 a 17:45. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spusťte na 5: 00 a 17: 00 v pondělí, středu a pátek každý týden. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Spustit na 5:15:00, 5:45 AM, 17:15:00 a 17:45:00 v pondělí, středu a pátek každý týden. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spuštění ve všední dny každých 15 minut. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Spusťte každých 15 minut ve všední dny od 9: 00 do 4:45. |
| `{"weekDays":["sunday"]}` |Spusťte v neděli při spuštění. |
| `{"weekDays":["tuesday", "thursday"]}` |Spusťte každé úterý a čtvrtek v čas spuštění. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Spustit v 6: 00 na dvacet osmého každý měsíc (za předpokladu, že **frekvence** "měsíci"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Spusťte v 6: 00 poslední den v měsíci.<br /><br />Pokud chcete spustit úlohu v poslední den v měsíci, použijte místo den 28, 29, 30 a 31 -1. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Spusťte v 6: 00 na první a poslední den v měsíci. |
| `{monthDays":[1,-1]}` |Spusťte na první a poslední den v měsíci v čase zahájení. |
| `{monthDays":[1,14]}` |Spusťte na první a čtrnáctá den v měsíci v čase zahájení. |
| `{monthDays":[2]}` |Spusťte na druhý den v měsíci v čase zahájení. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spusťte na prvním pátek v každém měsíci v 5: 00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Spusťte na prvním pátek v každém měsíci v čase zahájení. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Spusťte na třetí pátek od konce měsíce, každý měsíc, při spuštění. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spuštění v každý první a poslední pátek v měsíci v 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Spusťte na první a poslední pátek v každém měsíci v čas spuštění. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Spusťte na páté pátek v každém měsíci v čase zahájení.<br /><br />Pokud neexistuje žádné páté pátek v měsíci, nebude spuštění úlohy. Zvažte použití -1 namísto 5 pro výskyt, pokud chcete spustit úlohu v posledního výskytu pátek v měsíci. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Spuštění v poslední pátek v měsíci každých 15 minut. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Spuštění každou třetí středu v měsíci v 5:15, 5:45, 17:15 a 17:45. |

## <a name="see-also"></a>Další informace najdete v tématech

- [Co je Scheduler?](scheduler-intro.md)
- [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)
- [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)
- [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)
- [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)
- [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
- [Azure Scheduler vysokou dostupnost a spolehlivost](scheduler-high-availability-reliability.md)
- [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)
- [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

