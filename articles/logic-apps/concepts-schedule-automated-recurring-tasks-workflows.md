---
title: Plánování opakovaných úloh a pracovních postupů v Azure Logic Apps
description: Přehled o plánování opakované automatizované úlohy, procesy a pracovní postupy s Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356049"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Naplánování a spuštění opakované automatizované úlohy, procesy a pracovní postupy s Azure Logic Apps

Logic Apps umožňuje vytvářet a spouštět automatizované opakované úlohy a procesy podle plánu. Tím, že vytvoříte aplikaci logiky, která se spouští triggerem integrované opakování nebo posuvné okno, které jsou triggery typu plánu, můžete spouštět úlohy okamžitě, později, nebo v opakovaných intervalech. Můžete volat služby uvnitř i mimo Azure, jako jsou koncové body HTTP nebo HTTPS, odesílat zprávy do služby Azure, například Azure Storage a Azure Service Bus nebo získat soubory nahraje do sdílené složky. Pomocí triggeru opakování můžete také nastavit komplexních plánů a pokročilé opakování pro spouštění úloh. Další informace o předdefinovaných aktivačních událostech plánovače a akcích najdete v části [plán a opakované automatizované spuštění, úloh a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Tady jsou některé příklady, které druhy úloh, které můžete spustit:

* Získejte interní data, jako je spuštění uložené procedury SQL každý den.

* Načíst externí data, jako je například o přijetí změn počasí z NOAA každých 15 minut.

* Odeslat data sestavy, například e-mailu souhrnné informace pro všechny objednávky větší než konkrétní částku v minulém týdnu.

* Zpracování dat, jako je komprese dnes má nahraných obrázků každý den v týdnu, hodiny mimo špičku.

* Vyčištění dat, jako je například odstranit všechny tweety, které jsou starší než tři měsíce.

* Archivace dat, jako je například nabízená faktury ke službě zálohování v 1:00 hodin každý den dalších devět měsíců.

Také vám pomůže naplánovat integrované akce pozastavení pracovního postupu před spuštěním další akce, například:

* Počkejte, dokud jeden den v týdnu pro odeslání e-mailu aktualizace stavu.

* Zpoždění pracovní postup, dokud volání protokolu HTTP má čas dokončení a obnovení a načítání výsledku.

Tento článek popisuje možnosti pro integrované aktivačních událostech plánovače a akce.

## <a name="schedule-triggers"></a>Aktivačních událostech plánovače

Můžete spustit pracovního postupu aplikace logiky pomocí triggeru opakování nebo aktivační událost posuvné okno, které nejsou přidružené žádné konkrétní služby nebo systému, například Office 365 Outlook nebo SQL Server. Tyto triggery spuštění a spuštění pracovního postupu podle zadané opakování, kde můžete vybrat interval a frekvenci, jako je počet sekund, minut a hodiny pro obě aktivační události nebo počet dní, týdny nebo měsíce pro trigger opakování. Můžete také nastavit počáteční datum a čas, jakož i časové pásmo. Pokaždé, když trigger aktivuje, Logic Apps vytvoří a spustí novou instanci pracovního postupu pro aplikaci logiky.

Zde jsou rozdíly mezi těmito aktivační události:

* **Opakování**: Spuštění vašeho pracovního postupu v pravidelných intervalech podle zadaného plánu. Pokud jsou opakování, trigger opakování nezpracovává zmeškaných opakování, ale restartuje opakování se do dalšího naplánovaného intervalu. Můžete zadat počáteční datum a čas, jakož i časové pásmo. Pokud vyberete "Day", můžete zadat denní dobu a minuty v hodině, například každý den ve 2:30. Pokud vyberete "Týden", můžete také vybrat dny v týdnu, jako je například středa až Saturday. Další informace najdete v tématu [vytvořit, podle plánu a spouštět opakované úlohy a pracovních postupů pomocí triggeru opakování](../connectors/connectors-native-recurrence.md).

* **Posuvné okno**: Spuštění vašeho pracovního postupu v pravidelných intervalech, které zpracovávají data v průběžné bloků. Pokud jsou opakování, aktivační událost posuvné okno přejde zpět a zpracuje zmeškaných opakování. Můžete zadat počáteční datum a čas, časové pásmo a trvání zpoždění opakování ve svém pracovním postupu. Tato aktivační událost nemá možností, které určují dnů, týdnů a měsíců, hodin dne, minuty, hodiny a dny v týdnu. Další informace najdete v tématu [vytvořit, podle plánu a spouštět opakované úlohy a pracovní postupy s triggerem posuvné okno](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Naplánování akcí

Po žádnou akci pracovního postupu aplikace logiky můžete použít akce zpoždění a zpoždění dokud váš pracovní postup čekat, než spustí další akce.

* **zpoždění**: Čekání na spuštění další akci pro zadaný počet časových jednotek, jako je například několika sekund, minuty, hodiny, dny, týdny nebo měsíce. Další informace najdete v tématu [zpoždění další akce v pracovních postupech](../connectors/connectors-native-delay.md).

* **Zpoždění do**: Čekání na další akce až do zadaného data a času spuštění. Další informace najdete v tématu [zpoždění další akce v pracovních postupech](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Vzory pro počáteční datum a čas

<a name="start-time"></a>

Tady jsou některé vzory, které ukazují, jak můžete řídit opakování s počáteční datum a čas a vykonávání těchto opakování služby Logic Apps:

| Čas spuštění | Opakování bez plánu | Opakování s plánem (pouze trigger opakování) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | První úloha se spustí okamžitě. <p>Spustí budoucí úlohy založené na čas posledního spuštění. | První úloha se spustí okamžitě. <p>Umožňuje spouštět budoucí úlohy podle zadaného plánu. |
| Počáteční čas v minulosti | **Opakování** aktivační události: Vypočítá doba spuštění na základě zadaný čas začátku a zahození za posledních spustit časy. Spuštění první úlohy v další budoucí čas spuštění. <p>Spustí budoucí úlohy založené na výpočty z posledního času spuštění. <p><p>**Posuvné okno** aktivační události: Vypočítá doba spuštění na základě zadaný čas začátku a časy respektuje poslední spuštění. <p>Spustí budoucí úlohy založené na výpočty v zadaný čas začátku. <p><p>Podrobnější vysvětlení najdete v příkladu za touto tabulkou. | Spuštění první úlohy *až po* než čas spuštění podle plánu vypočítaného z času začátku. <p>Umožňuje spouštět budoucí úlohy podle zadaného plánu. <p>**Poznámka:** Je-li zadat počet opakování s plánem, ale nezadávejte hodin nebo minut, než plán, pak budoucích časů spuštění vypočítají na základě hodin nebo minut, v uvedeném pořadí, od prvního spuštění. |
| Čas spuštění v současné době nebo v budoucnu | Spuštění první úlohy v zadaný čas začátku. <p>Spustí budoucí úlohy založené na výpočty z posledního času spuštění. | Spuštění první úlohy *až po* než čas spuštění podle plánu vypočítaného z času začátku. <p>Umožňuje spouštět budoucí úlohy podle zadaného plánu. <p>**Poznámka:** Je-li zadat počet opakování s plánem, ale nezadávejte hodin nebo minut, než plán, pak budoucích časů spuštění vypočítají na základě hodin nebo minut, v uvedeném pořadí, od prvního spuštění. |
||||

*Příklad pro poslední čas spuštění a opakování, ale žádný plán*

Předpokládejme, že aktuální datum a čas je 8. září 2017 v 13:00. Můžete zadat počáteční datum a čas jako 7. září 2017 ve 2:00 PM, což je v minulosti a je nastaveno opakování, který se spustí každé dva dny.

| Čas spuštění | Aktuální čas | Opakování | Plán |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** ve 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** v 13:00) | Každé 2 dny | {none} |
|||||

Pro trigger opakování Logic Apps vypočítá modul runtime podle času spuštění zahodí minulé doby spuštění, používá další budoucí čas zahájení pro první spuštění a vypočítá budoucí běhy založené na čas posledního spuštění.

Zde je, jak bude vypadat toto opakování:

| Čas spuštění | První čas spuštění | Budoucí čas spuštění |
|------------|----------------|------------------|
| 2017-09 -**07** ve 14:00 | 2017-09 -**09** ve 14:00 | 2017-09 -**11** ve 14:00 </br>2017-09 -**13** ve 14:00 </br>2017-09 -**15** ve 14:00 </br>a tak dále. |
||||

Ano, bez ohledu na to jak daleko v minulosti, je třeba zadat čas zahájení, 2017-09 -**05** ve 14:00 nebo 2017-09 -**01** na 2:00 PM, první spusťte vždy používá další budoucí čas spuštění.

Pro trigger posuvné okno, Logic Apps vypočítá modul runtime podle času spuštění respektuje posledních časy spuštění, využívá čas zahájení pro první spuštění a vypočítá budoucí spouštění podle času spuštění.

Zde je, jak bude vypadat toto opakování:

| Čas spuštění | První čas spuštění | Budoucí čas spuštění |
|------------|----------------|------------------|
| 2017-09 -**07** ve 14:00 | 2017-09 -**07** ve 14:00 | 2017-09 -**09** ve 14:00 </br>2017-09 -**11** ve 14:00 </br>2017-09 -**13** ve 14:00 </br>2017-09 -**15** ve 14:00 </br>a tak dále. |
||||

Ano, bez ohledu na to jak daleko v minulosti, je třeba zadat čas zahájení, 2017-09 -**05** ve 14:00 nebo 2017-09 -**01** na 2:00 PM, první spusťte vždy používá zadaný počáteční čas.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Příklad opakování

Tady jsou různé příklad opakování, které můžete nastavit pro aktivační události, které podporují možnosti:

| Trigger | Opakování | Interval | Frekvence | Čas spuštění | V tyto dny | V těchto hodinách | V těchto minutách | Poznámka |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Opakování, <br>Posuvné okno | Spuštění každých 15 minut (žádná počáteční datum a čas) | 15 | Minuta | {none} | {není k dispozici} | {none} | {none} | Tento plán okamžitě spustí a pak vypočítá budoucí opakování podle času posledního spuštění. |
| Opakování, <br>Posuvné okno | Spuštění každých 15 minut (s počáteční datum a čas) | 15 | Minuta | *startDate*T*startTime*Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadané datum a čas, pak vypočítá budoucí opakování podle času posledního spuštění. |
| Opakování, <br>Posuvné okno | Spuštění každou hodinu v celou hodinu (pomocí počáteční datum a čas) | 1 | Hodina | *startDate*Thh:00:00Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaný počáteční datum a čas. Budoucí opakování spouštět každou hodinu v "00" minuta značku, která bude vypočítána z času začátku. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán se spustí v uvedeném pořadí pouze jeden den v týdnu nebo jeden den za měsíc. |
| Opakování, <br>Posuvné okno | Spuštění každou hodinu, každý den (žádná počáteční datum a čas) | 1 | Hodina | {none} | {není k dispozici} | {none} | {none} | Tento plán okamžitě spustí a vypočítá budoucí opakování podle času posledního spuštění. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán se spustí v uvedeném pořadí pouze jeden den v týdnu nebo jeden den za měsíc. |
| Opakování, <br>Posuvné okno | Spuštění každou hodinu, každý den (s počáteční datum a čas) | 1 | Hodina | *startDate*T*startTime*Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadané datum a čas, pak vypočítá budoucí opakování podle času posledního spuštění. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán se spustí v uvedeném pořadí pouze jeden den v týdnu nebo jeden den za měsíc. |
| Opakování, <br>Posuvné okno | Spustit každých 15 minut po hodině, každou hodinu (s počáteční datum a čas) | 1 | Hodina | *startDate*T00:15:00Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaný počáteční datum a čas. Budoucí opakování probíhat "15" minute značka, která se počítá od samého začátku čas, tak v 00:15, 1:15:00, 2:15:00 a tak dále. |
| Opakování | Spuštění každých 15 minut po hodině, každou hodinu (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Tento plán se spustí v 00:15, 1:15:00, 2:15:00, a tak dále. Tento plán je také ekvivalentní frekvencí "Hodina" a "15" minut čas spuštění. |
| Opakování | Spuštění každých 15 minut v zadané minuty značky (žádná počáteční datum a čas). | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se nespustí, dokud další zadané značky 15 minut. |
| Opakování | Spuštění v 8:00 každý den (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 8 | {none} | Tento plán se spustí v 8:00 hodin každý den podle zadaného plánu. |
| Opakování | Spuštění v 8:00 každý den (s počáteční datum a čas) | 1 | Den | *startDate*T08:00:00Z | {není k dispozici} | {none} | {none} | Tento plán se spustí 8:00 hodin každý den podle zadaný čas začátku. | 
| Opakování | Spustit v 8:30:00 každý den (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 8 | 30 | Tento plán se spustí v 8:30:00 každý den podle zadaného plánu. |
| Opakování | Spustit v 8:30:00 každý den (s počáteční datum a čas) | 1 | Den | *startDate*T08:30:00Z | {není k dispozici} | {none} | {none} | Tento plán se spustí v zadaný počáteční datum v 8:30:00. |
| Opakování | Spuštění v 8:30:00 a 16:30:00 každý den | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30 | |
| Opakování | Spustit v 8:30:00, 8:45:00, 16:30:00 a 16:45:00 každý den | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30, 45 | |
| Opakování | Spusťte každou sobotu v 17: 00 (žádná počáteční datum a čas) | 1 | Týden | {none} | "Sobota" | 17 | 00 | Tento plán se spustí každou sobotu v 17:00:00. |
| Opakování | Spusťte každou sobotu v 17: 00 (s počáteční datum a čas) | 1 | Týden | *startDate*T17:00:00Z | "Sobota" | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaný počáteční datum a čas, v tomto případě 9. září 2017 v 17:00:00. Budoucí opakování spusťte každou sobotu v 17:00:00. |
| Opakování | Spuštění každé úterý, čtvrtek v 17: 00 | 1 | Týden | {none} | "Úterý", "Čtvrtek" | 17 | {none} | Tento plán se spustí každé úterý a čtvrtek v 17:00:00. |
| Opakování | Spuštění každou hodinu během pracovní doby | 1 | Týden | {none} | Vyberte všechny dny, s výjimkou sobotu a neděli. | Vyberte hodiny dne, který chcete. | Vyberte všechny minuty v hodině, který chcete. | Například pokud 8:00:00 do 17:00 hodin pracovní doby, zvolte "8, 9, 10, 11, 12, 13, 14, 15, 16 a 17" jako hodin dne. <p>Pokud pracovní doby 8:30:00 do 5:30 PM, vyberte předchozí hodiny dne plus "30" jako minuty v hodině. |
| Opakování | Spustit jednou pro každý den o víkendech | 1 | Týden | {none} | "Sobota", "Neděle" | Vyberte hodiny dne, který chcete. | Vyberte všechny minuty v hodině, podle potřeby. | Tento plán se spustí podle nastaveného plánu každou sobotu a neděli. |
| Opakování | Spuštění každých 15 minut jednou za dva týdny v pondělí pouze | 2 | Týden | {none} | "Pondělí" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se spustí každý druhý pondělí na značce každých 15 minut. |
| Opakování | Spusťte každý měsíc | 1 | Měsíc | *startDate*T*startTime*Z | {není k dispozici} | {není k dispozici} | {není k dispozici} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas a vypočítá budoucí opakování na počáteční datum a čas. Pokud nechcete zadat počáteční datum a čas, používá tento plán datum a čas vytvoření. |
| Opakování | Spuštění každou hodinu za jeden den za měsíc | 1 | Měsíc | {Viz poznámka} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Viz poznámka} | Pokud nechcete zadat počáteční datum a čas, používá tento plán datum a čas vytvoření. Pokud chcete řídit minut u plánu opakování, zadejte minuty v hodině, čas zahájení, nebo použijte čas vytvoření. Například pokud časem spuštění a čas vytvoření 8:25:00, tento plán se spouští v 8:25:00, 9:25 AM, 10:25 AM, a tak dále. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Spustit pouze jednou

Pokud chcete aplikaci logiky spustit pouze v jednom okamžiku v budoucnu, můžete použít **plánovače: Spustit jednou úlohy** šablony. Po vytvoření nové aplikace logiky, ale před otevřením návrháře pro Logic Apps, v části **šablony** části z **kategorie** seznamu vyberte **plán**a pak vyberte Tuto šablonu:

![Vyberte "plánovače: Spustit jednou úlohy"šablony](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Nebo, pokud je možné spustit aplikaci logiky s **přijetí požadavku HTTP při - požadavek** aktivovat a čas zahájení předat jako parametr pro aktivační událost. Pro první akci, použijte **zpoždění do – naplánovat** akce a zadejte čas při spuštění další akci.

## <a name="next-steps"></a>Další postup

* [Vytvářet, plánovat a spouštět opakované úlohy a pracovní postupy pomocí triggeru opakování](../connectors/connectors-native-recurrence.md)
* [Vytvářet, plánovat a spouštět opakované úlohy a pracovní postupy s triggerem posuvné okno](../connectors/connectors-native-sliding-window.md)
* [Pozastavit pracovních postupů s akcemi zpoždění](../connectors/connectors-native-delay.md)