---
title: Plánování opakujících se úloh a pracovních postupů v Azure Logic Apps
description: Přehled o plánování opakujících se automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 27763536b859b7bc3e9aa0a7c490cb510c0fda41
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588450"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps

Logic Apps vám pomůže vytvářet a spouštět automatizované opakované úlohy a procesy podle plánu. Vytvořením pracovního postupu aplikace logiky, který začíná vestavěným triggerem opakování nebo aktivační událostí posuvných oken, což jsou triggery typu plánování, můžete spouštět úlohy hned později nebo v pravidelných intervalech. Můžete volat služby uvnitř i mimo Azure, jako jsou koncové body HTTP nebo HTTPS, odesílat zprávy do služeb Azure, například Azure Storage a Azure Service Bus, nebo získávat soubory odeslané do sdílené složky. Pomocí triggeru opakování můžete také nastavit komplexní plány a pokročilé opakování pro spuštěné úlohy. Další informace o integrovaných triggerech a akcích plánování najdete v tématu [plánování aktivačních událostí](#schedule-triggers) a [plánování akcí](#schedule-actions). 

> [!TIP]
> Můžete naplánovat a spustit opakující se úlohy bez vytváření samostatné aplikace logiky pro každou naplánovanou úlohu a spuštění do [limitu pro pracovní postupy na oblast a předplatné](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Místo toho můžete použít model aplikace logiky, který je vytvořený [šablonou rychlého startu Azure: Logic Apps Plánovač úloh](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> Šablona plánovače úloh Logic Apps vytvoří aplikaci logiky CreateTimerJob, která volá aplikaci logiky TimerJob. Pak můžete zavolat aplikaci logiky CreateTimerJob jako rozhraní API tak, že provedete požadavek HTTP a jako vstup zadáte plán pro požadavek. Každé volání aplikace logiky CreateTimerJob také volá aplikaci logiky TimerJob, která vytvoří novou instanci TimerJob, která se průběžně spouští na základě zadaného plánu nebo až do splnění zadaného limitu. Tímto způsobem můžete spustit tolik instancí TimerJob, kolik chcete, aniž byste se museli starat o omezení pracovního postupu, protože instance nejsou jednotlivými definicemi pracovních postupů aplikace logiky nebo prostředky.

Tento seznam obsahuje několik ukázkových úloh, které můžete spustit s integrovanými aktivačními událostmi plánu:

* Získejte interní data, jako je například spuštění uložené procedury SQL každý den.

* Získejte externí data, jako jsou například zprávy o počasí z NOAA každých 15 minut.

* Odešlete data sestavy, jako je například Shrnutí e-mailu pro všechny objednávky větší než konkrétní částka za minulý týden.

* Zpracování dat, jako je například komprese dnešních nahraných obrázků v době mimo špičku.

* Vyčištění dat, například odstranění všech tweety starších než tři měsíce.

* Archivace dat, jako jsou nabízené faktury, do služby zálohování v 1:00. každý den v příštích devět měsících.

Můžete také použít předdefinované akce plánu k pozastavení pracovního postupu před spuštěním další akce, například:

* Počkejte, až pracovní den pošle aktualizaci stavu přes e-mail.

* Odložit pracovní postup do doby, než bude dokončeno volání protokolu HTTP, než bude pokračovat a bude načítán výsledek.

Tento článek popisuje možnosti pro předdefinované aktivační události a akce pro plán.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Aktivační události plánu

Pracovní postup aplikace logiky můžete spustit pomocí triggeru opakování nebo aktivační procedury posuvných oken, která není přidružena k žádné konkrétní službě ani systému. Tyto aktivační události spustí a spustí pracovní postup na základě zadaného opakování, kde můžete vybrat interval a frekvenci, například počet sekund, minut, hodin, dnů, týdnů nebo měsíců. Můžete také nastavit datum a čas zahájení i časové pásmo. Pokaždé, když se Trigger aktivuje, Logic Apps vytvoří a spustí novou instanci pracovního postupu pro vaši aplikaci logiky.

Tady jsou rozdíly mezi těmito triggery:

* **Opakování**: pracovní postup se spouští v pravidelných časových intervalech na základě zadaného plánu. Pokud se opakování nedaří, například kvůli přerušení nebo zakázaným pracovním postupům, aktivační událost opakování nezpracovává zmeškané opakování, ale restartuje opakování s dalším naplánovaným intervalem. Můžete zadat datum a čas zahájení i časové pásmo. Pokud vyberete "Day", můžete zadat hodiny dne a minuty hodiny, například každý den v 2:30. Pokud vyberete "Week", můžete také vybrat dny v týdnu, například středa a sobotu. Další informace najdete v tématu [vytváření, plánování a spouštění opakovaných úloh a pracovních postupů s triggerem opakování](../connectors/connectors-native-recurrence.md).

* **Posuvné okno**: spouští pracovní postup v pravidelných časových intervalech, které zpracovávají data v souvislých blocích. Pokud se opakování neobjevují, například kvůli přerušení nebo zakázaným pracovním postupům, přepne se posuvná aktivační událost okna zpátky a zpracuje zmeškané opakování. Můžete zadat počáteční datum a čas, časové pásmo a dobu trvání pro zpoždění každého opakování ve vašem pracovním postupu. Tato aktivační událost nepodporuje Pokročilá plánování, například konkrétní hodiny dne, minuty hodiny a dny v týdnu. Další informace najdete v tématu [vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí posuvných triggerů okna](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Plánované akce

Po jakékoli akci v pracovním postupu aplikace logiky můžete použít prodlevu a prodlevu, dokud akce nečekají, než bude pracovní postup čekat, než se spustí další akce.

* **Delay**: Počkejte, než se spustí další akce pro zadaný počet časových jednotek, například sekundy, minuty, hodiny, dny, týdny nebo měsíce. Další informace najdete v tématu [zpoždění další akce v pracovních postupech](../connectors/connectors-native-delay.md).

* **Zpoždění do**: Počkejte na spuštění další akce až do zadaného data a času. Další informace najdete v tématu [zpoždění další akce v pracovních postupech](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Vzory pro počáteční datum a čas

<a name="start-time"></a>

Tady je několik vzorů, které ukazují, jak můžete řídit opakování s počátečním datem a časem a jak služba Logic Apps spouští Tato opakování:

| Čas spuštění | Opakování bez plánu | Opakování s plánem (jenom Trigger opakování) |
|------------|-----------------------------|----------------------------------------------------|
| nTato | Okamžitě spustí první úlohu. <p>Spustí budoucí úlohy na základě času posledního spuštění. | Okamžitě spustí první úlohu. <p>Spustí budoucí úlohy na základě zadaného plánu. |
| Čas začátku v minulosti | Trigger **opakování** : vypočítá dobu běhu založenou na zadaném čase zahájení a zahodí minulé časy spuštění. Spustí první úlohu v příštím budoucím čase spuštění. <p>Spustí budoucí úlohy na základě výpočtů z posledního času spuštění. <p><p>Aktivační událost **posuvných oken** : vypočítá časy spuštění na základě zadaného počátečního času a respektuje minulé časy spuštění. <p>Spustí budoucí úlohy na základě výpočtů z určeného počátečního času. <p><p>Další vysvětlení najdete v příkladu následujícím v této tabulce. | Spustí první úlohu, která *není dřív* než čas spuštění, podle plánu vypočítaného z času spuštění. <p>Spustí budoucí úlohy na základě zadaného plánu. <p>**Poznámka:** Pokud zadáte opakování s plánem, ale nezadáte hodiny nebo minuty pro daný plán, pak se budoucí časy spuštění počítají pomocí hodin nebo minut v první době spuštění. |
| Čas spuštění v současnosti nebo v budoucnosti | Spustí první úlohu v zadaném počátečním čase. <p>Spustí budoucí úlohy na základě výpočtů z posledního času spuštění. | Spustí první úlohu, která *není dřív* než čas spuštění, podle plánu vypočítaného z času spuštění. <p>Spustí budoucí úlohy na základě zadaného plánu. <p>**Poznámka:** Pokud zadáte opakování s plánem, ale nezadáte hodiny nebo minuty pro daný plán, pak se budoucí časy spuštění počítají pomocí hodin nebo minut v první době spuštění. |
||||

> [!IMPORTANT]
> Pokud opakování nespecifikují pokročilé možnosti plánování, budoucí opakování vycházejí z času posledního spuštění.
> Časy zahájení těchto opakování se můžou zpomalit kvůli faktorům, jako je latence během volání úložiště. Abyste se ujistili, že vaše aplikace logiky nezpůsobí opakování, zejména pokud je frekvence ve dnech nebo delší, použijte jednu z těchto možností:
> 
> * Zadejte čas zahájení opakování.
> 
> * Zadejte hodiny a minuty pro spuštění opakování pomocí v **těchto hodinách** a **ve vlastnostech těchto minut** .
> 
> * Použijte [aktivační událost posuvných oken](../connectors/connectors-native-sliding-window.md)místo triggeru opakování.

*Příklad pro čas zahájení a opakování, ale žádný plán*

Předpokládejme, že aktuální datum a čas jsou 8. září 2017 na 1:00 odp. Zadejte počáteční datum a čas od 7. září 2017 na 2:00 PM, který je v minulosti, a opakování, které se spouští každé dva dny.

| Čas spuštění | Aktuální čas | Opakování | Plán |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(2017-09 –**07** v 2:00 odp.) | 2017-09 –**08** T13:00:00Z <br>(2017-09 –**08** v 1:00 odp.) | Každé dva dny | nTato |
|||||

U triggeru opakování vypočítá Logic Apps modul časy spuštění na základě času spuštění, zahodí minulé časy spuštění, používá další budoucí čas spuštění pro první spuštění a počítá budoucí běhy na základě posledního času spuštění.

Tady je postup, jak vypadá toto opakování:

| Čas spuštění | Čas prvního spuštění | Budoucí časy spuštění |
|------------|----------------|------------------|
| 2017-09 –**07** v 2:00 odp. | 2017-09 –**09** v 2:00 odp. | 2017-09 –**11** v 2:00 odp. </br>2017-09 –**13** v 2:00 odp. </br>2017-09 –**15** v 2:00 odp. </br>a tak dále... |
||||

Bez ohledu na to, jak daleko v minulosti zadáte čas zahájení, například 2017-09-**05** v 2:00 odp. nebo 2017-09-**01** v 2:00 odp. první spuštění vždy používá další budoucí čas spuštění.

V případě aktivační události posuvných oken modul Logic Apps vypočítá dobu běhu založenou na čase spuštění, respektuje dobu běhu, používá počáteční čas prvního spuštění a vypočítá budoucí běhy na základě času spuštění.

Tady je postup, jak vypadá toto opakování:

| Čas spuštění | Čas prvního spuštění | Budoucí časy spuštění |
|------------|----------------|------------------|
| 2017-09 –**07** v 2:00 odp. | 2017-09 –**08** v 1:00 odp. (aktuální čas) | 2017-09 –**09** v 2:00 odp. </br>2017-09 –**11** v 2:00 odp. </br>2017-09 –**13** v 2:00 odp. </br>2017-09 –**15** v 2:00 odp. </br>a tak dále... |
||||

Bez ohledu na to, jak daleko v minulosti zadáte čas zahájení, například 2017-09-**05** v 2:00 odp. nebo 2017-09-**01** v 2:00 odp. první spuštění vždy používá určený čas spuštění.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Příklady opakování

Tady jsou různé příklady opakování, které můžete nastavit pro aktivační události, které podporují tyto možnosti:

| Trigger | Opakování | Interval | Frekvence | Čas spuštění | V tyto dny | V těchto hodinách | V těchto minutách | Poznámka |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Vzorec <br>Posuvné okno | Spustit každých 15 minut (žádné počáteční datum a čas) | 15 | Minuta | nTato | znemožnit | nTato | nTato | Tento plán se spustí okamžitě a pak vypočítá budoucí opakování na základě posledního času spuštění. |
| Vzorec <br>Posuvné okno | Spustit každých 15 minut (s počátečním datem a časem) | 15 | Minuta | *StartDate* T *čas_spuštění* Z | znemožnit | nTato | nTato | Tento plán se nespustí *dříve* , než je zadané počáteční datum a čas, a pak vypočítá budoucí opakování na základě posledního času spuštění. |
| Vzorec <br>Posuvné okno | Spustit každou hodinu, ve hodinu (s počátečním datem a časem) | 1 | Hodina | *StartDate* THH: 00:00Z | znemožnit | nTato | nTato | Tento plán se nespustí *dříve* , než je zadané počáteční datum a čas. Budoucí opakování se spouští každou hodinu při označení "00" min. počítá se od počátečního času. <p>Pokud je frekvence "Week" (týden) nebo "Month", tento plán se spustí jenom na jeden den v týdnu nebo jeden den za měsíc. |
| Vzorec <br>Posuvné okno | Spustit každou hodinu, každý den (žádné počáteční datum a čas) | 1 | Hodina | nTato | znemožnit | nTato | nTato | Tento plán se spustí okamžitě a vypočítá budoucí opakování na základě posledního času spuštění. <p>Pokud je frekvence "Week" (týden) nebo "Month", tento plán se spustí jenom na jeden den v týdnu nebo jeden den za měsíc. |
| Vzorec <br>Posuvné okno | Spustit každou hodinu, každý den (s počátečním datem a časem) | 1 | Hodina | *StartDate* T *čas_spuštění* Z | znemožnit | nTato | nTato | Tento plán se nespustí *dříve* , než je zadané počáteční datum a čas, a pak vypočítá budoucí opakování na základě posledního času spuštění. <p>Pokud je frekvence "Week" (týden) nebo "Month", tento plán se spustí jenom na jeden den v týdnu nebo jeden den za měsíc. |
| Vzorec <br>Posuvné okno | Spouští se každých 15 minut po celé hodině (s počátečním datem a časem). | 1 | Hodina | *StartDate* T00:15:00Z | znemožnit | nTato | nTato | Tento plán se nespustí *dříve* , než je zadané počáteční datum a čas. Budoucí opakování se spouštějí při "15" minutové značce, která se počítá od počátečního času, takže v 00:15 dop. 1:15 AM, 2:15 a tak dále. |
| Opakování | Spustit každých 15 minut po celé hodině (bez počátečního data a času) | 1 | Den | nTato | znemožnit | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Tento plán běží v 00:15 dop. 1:15 AM, 2:15 a tak dále. Tento plán je také stejný jako frekvence "hodina" a čas spuštění s "15" minutami. |
| Opakování | Spustí se každých 15 minut v zadaném počtu minut (žádné počáteční datum a čas). | 1 | Den | nTato | znemožnit | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se nespustí až do další zadané 15 minutové značky. |
| Opakování | Spustit každý den v 8 dop. *plus* po uložení aplikace logiky se zobrazí minutová značka | 1 | Den | nTato | znemožnit | 8 | nTato | Bez počátečního data a času se tento plán spustí na základě času uložení aplikace logiky (operace PUT). |
| Opakování | Spustit každý den v 8:00. (s počátečním datem a časem) | 1 | Den | *StartDate* T08:00:00Z | znemožnit | nTato | nTato | Tento plán se nespustí *dříve* , než je zadané počáteční datum a čas. Budoucí výskyty běží denně v 8:00. | 
| Opakování | Spustit každý den v 8:00. (žádné počáteční datum a čas) | 1 | Den | nTato | znemožnit | 8 | 00 | Tento plán se spustí každý den v 8:00. |
| Opakování | Spustit každý den v 8:00 a 4:00 odp. | 1 | Den | nTato | znemožnit | 8, 16 | 0 | |
| Opakování | Každodenní spuštění v 8:30 AM, 8:45 dop., 4:30 ODP. a 4:45 odp. | 1 | Den | nTato | znemožnit | 8, 16 | 30.45 | |
| Opakování | Spustit každou sobotu v 5:00 PM (žádné počáteční datum a čas) | 1 | Týden | nTato | Sobota | 17 | 0 | Tento plán se spustí každou sobotu v 5:00./odp.. |
| Opakování | Spustit každou sobotu v 5:00. odp. (s počátečním datem a časem) | 1 | Týden | *StartDate* T17:00:00Z | Sobota | nTato | nTato | Tento plán se nespustí *dříve* , než je zadané počáteční datum a čas, v tomto případě 9. září 2017 na 5:00 odp. Budoucí opakování se spouštějí každou sobotu v 5:00./odp.. |
| Opakování | Spustit každé úterý, čtvrtek na 5 odp. *plus* minuty od při uložení aplikace logiky| 1 | Týden | nTato | "Úterý", "čtvrtek" | 17 | nTato | |
| Opakování | Spustí se každou hodinu během pracovní doby. | 1 | Týden | nTato | Vyberte všechny dny s výjimkou sobotu a neděle. | Vyberte hodiny v požadovaném dni. | Vyberte libovolné minuty hodiny, kterou chcete. | Pokud například máte pracovní dobu 8:00 až 5:00 odp., vyberte "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako hodiny dne *plus* "0" jako minuty hodiny. |
| Opakování | Spouštět každý den každý den na víkendech | 1 | Týden | nTato | "Sobota", "neděle" | Vyberte hodiny v požadovaném dni. | V případě potřeby vyberte libovolné minuty hodiny. | Tento plán se spustí každou sobotu a neděli podle zadaného plánu. |
| Opakování | Spouští se každých 15 minut dva týdny jenom v pondělí. | 2 | Týden | nTato | Pondělí | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se spouští každé druhé pondělí při každé 15 minutové značce. |
| Opakování | Spustit každý měsíc | 1 | Month (Měsíc) | *StartDate* T *čas_spuštění* Z | znemožnit | znemožnit | znemožnit | Tento plán se nespustí *dřív* než zadané počáteční datum a čas a vypočte budoucí opakování v počátečním datu a času. Pokud nezadáte počáteční datum a čas, použije tento plán datum a čas vytvoření. |
| Opakování | Spustit každou hodinu po dobu jednoho dne měsíčně | 1 | Month (Měsíc) | {Viz Poznámka} | znemožnit | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Viz Poznámka} | Pokud nezadáte počáteční datum a čas, použije tento plán datum a čas vytvoření. Chcete-li řídit minuty pro plán opakování, zadejte minuty hodiny, čas spuštění nebo použijte čas vytvoření. Pokud je například čas spuštění nebo čas vytvoření 8:25 dop., bude tento plán spuštěn v 8:25 dop., 9:25, 10:25 a tak dále. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Spustit pouze jednorázově

Pokud chcete aplikaci logiky spustit pouze v jednom okamžiku v budoucnu, můžete použít šablonu **Scheduler: Run jedenkrát Jobs** . Po vytvoření nové aplikace logiky, ale před otevřením návrháře Logic Apps v části **šablony** vyberte v seznamu **kategorie** možnost **plán** a pak vyberte tuto šablonu:

![Vyberte šablonu Plánovač: spustit po úlohách.](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Nebo, pokud můžete aplikaci logiky spustit, **když se přijme požadavek HTTP – Trigger žádosti** a předejte čas spuštění jako parametr triggeru. Pro první akci použijte akci **zpoždění do-plánovaného** a zadejte čas, kdy se má spustit další akce.

## <a name="next-steps"></a>Další kroky

* [Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí triggeru opakování](../connectors/connectors-native-recurrence.md)
* [Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí posuvné aktivační události okna](../connectors/connectors-native-sliding-window.md)
* [Pozastavit pracovní postupy s akcemi zpoždění](../connectors/connectors-native-delay.md)
