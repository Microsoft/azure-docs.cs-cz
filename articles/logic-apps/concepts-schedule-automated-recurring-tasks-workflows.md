---
title: Plánování opakovaných úkolů a pracovních postupů v Aplikacích Azure Logic Apps
description: Přehled plánování opakovaných automatizovaných úloh, procesů a pracovních postupů pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270560"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps

Logic Apps pomáhá vytvářet a spouštět automatické opakované úlohy a procesy podle plánu. Vytvořením pracovního postupu aplikace logiky, který začíná předdefinovanou aktivační událostí opakování nebo aktivační událostí posuvného okna, což jsou aktivační události typu Schedule, můžete úlohy spustit okamžitě, později nebo v intervalu opakování. Můžete volat služby uvnitř i vně Azure, jako jsou koncové body HTTP nebo HTTPS, posílat zprávy do služeb Azure, jako je Azure Storage a Azure Service Bus, nebo získat soubory nahrané do sdílené složky. Pomocí aktivační události Opakování můžete také nastavit složité plány a upřesňující opakování pro spouštění úloh. Další informace o předdefinovaných aktivačních událostech a akcích plánu najdete v [tématech Plánování aktivačních událostí](#schedule-triggers) a [Akce plánování](#schedule-actions). 

> [!TIP]
> Můžete naplánovat a spustit opakované úlohy bez vytvoření samostatné aplikace logiky pro každou naplánovanou úlohu a spuštění do [limitu pracovních postupů na oblast a předplatné](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Místo toho můžete použít vzor aplikace logiky, který je vytvořen [šablonou Azure QuickStart: Plánovač úloh Logic Apps](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> Šablona plánovače úloh aplikace Logic Apps vytvoří aplikaci logiky CreateTimerJob, která volá aplikaci logiky TimerJob. Potom můžete volat createtimerjob aplikace logiky jako rozhraní API tím, že požadavek HTTP a předání plánu jako vstup pro požadavek. Každé volání aplikace logiky CreateTimerJob také volá aplikaci logiky TimerJob, která vytvoří novou instanci TimerJob, která se nepřetržitě spouští na základě zadaného plánu nebo dokud nesplní zadaný limit. Tímto způsobem můžete spustit libovolný počet instancí TimerJob bez obav o omezení pracovního postupu, protože instance nejsou definice pracovního postupu jednotlivých aplikací logiky nebo prostředky.

Tento seznam zobrazuje některé ukázkové úlohy, které lze spustit pomocí předdefinovaných aktivačních událostí plánu:

* Získejte interní data, jako je například spustit sql uloženou proceduru každý den.

* Získejte externí data, jako je například vyžádat zprávy o počasí z NOAA každých 15 minut.

* Odešlete data sestavy, například e-mail souhrn pro všechny objednávky větší než určitá částka za poslední týden.

* Zpracovávat data, jako je komprimování dnešních nahraných obrázků každý všední den mimo špičku.

* Vyčistěte data, například odstranit všechny tweety starší než tři měsíce.

* Archivujte data, jako je nabízená faktury službě zálohování každý den v 1:00 po dobu následujících devíti měsíců.

Integrované akce plánu můžete také pozastavit pracovní postup před spuštěním další akce, například:

* Počkejte až do pracovního dne, abyste odeslali aktualizaci stavu prostředpo e-mailem.

* Zpozdit pracovní postup, dokud nebude mít volání HTTP čas na dokončení před obnovením a načtením výsledku.

Tento článek popisuje možnosti pro plán integrované aktivační události a akce.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Spuštění plánu

Pracovní postup aplikace logiky můžete spustit pomocí aktivační události opakování nebo aktivační události posuvného okna, která není přidružena k žádné konkrétní službě nebo systému, například Office 365 Outlook nebo SQL Server. Tyto aktivační události spustí a spustí pracovní postup na základě zadaného opakování, kde vyberete interval a frekvenci, například počet sekund, minut a hodin pro obě aktivační události nebo počet dní, týdnů nebo měsíců pro aktivační událost opakování. Můžete také nastavit počáteční datum a čas a také časové pásmo. Pokaždé, když se spustí aktivační událost, Logic Apps vytvoří a spustí novou instanci pracovního postupu pro vaši aplikaci logiky.

Zde jsou rozdíly mezi těmito aktivačními událostmi:

* **Opakování**: Spustí pracovní postup v pravidelných časových intervalech na základě zadaného plánu. Pokud dojde k vynechání opakování, aktivační událost opakování nezpracuje zmeškané opakování, ale restartuje opakování s dalším plánovaným intervalem. Můžete zadat počáteční datum a čas, stejně jako časové pásmo. Pokud vyberete "Den", můžete zadat hodiny dne a minuty hodiny, například každý den ve 2:30. Pokud vyberete "Týden", můžete také vybrat dny v týdnu, například středa a sobota. Další informace naleznete v [tématu Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí aktivační události Opakování](../connectors/connectors-native-recurrence.md).

* **Posuvné okno:** Spustí pracovní postup v pravidelných časových intervalech, které zpracovávají data v souvislých blocích. Pokud opakování jsou vynechány, posuvné okno aktivační událost přejde zpět a zpracuje zmeškané opakování. Můžete zadat počáteční datum a čas, časové pásmo a dobu trvání, která zpozdí každé opakování pracovního postupu. Tato aktivační událost nemá možnosti určit dny, týdny a měsíce, hodiny dne, minuty hodiny a dny v týdnu. Další informace naleznete v [tématu Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí aktivační události Posuvné okno](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Plánování akcí

Po jakékoli akci v pracovním postupu aplikace logiky můžete pomocí akcí Zpoždění a Zpoždění do, aby váš pracovní postup počkal, než se spustí další akce.

* **Zpoždění**: Počkejte na spuštění další akce pro zadaný počet časových jednotek, například sekund, minut, hodin, dnů, týdnů nebo měsíců. Další informace naleznete [v tématu Delay the next action in workflows](../connectors/connectors-native-delay.md).

* **Zpoždění do**: Počkejte na spuštění další akce až do zadaného data a času. Další informace naleznete [v tématu Delay the next action in workflows](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Vzory počátečního data a času

<a name="start-time"></a>

Zde jsou některé vzory, které ukazují, jak můžete řídit opakování s počátečním datem a časem a jak služba Logic Apps spouští tyto opakování:

| Čas spuštění | Opakování bez plánu | Opakování s plánem (pouze aktivační událost opakování) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Spustí první úlohy okamžitě. <p>Spustí budoucí úlohy na základě času posledního spuštění. | Spustí první úlohy okamžitě. <p>Spustí budoucí úlohy na základě zadaného plánu. |
| Čas zahájení v minulosti | Aktivační událost **opakování:** Vypočítá časy spuštění na základě zadaného počátečního času a zahodí minulé časy spuštění. Spustí první úlohu v příštím budoucím běhu. <p>Spustí budoucí úlohy na základě výpočtů z doby posledního spuštění. <p><p>Aktivační událost **posuvného okna:** Vypočítá časy spuštění na základě zadaného počátečního času a vyhoní minulých časů spuštění. <p>Spustí budoucí úlohy na základě výpočtů od zadaného času zahájení. <p><p>Další vysvětlení naleznete v příkladu následující ho. | Spustí první úlohu *nejdříve* čas zahájení, na základě plánu vypočítaného od počátečního času. <p>Spustí budoucí úlohy na základě zadaného plánu. <p>**Poznámka:** Pokud zadáte opakování s plánem, ale nezadejte hodiny nebo minuty pro plán, budou budoucí časy spuštění vypočteny pomocí hodin nebo minut od prvního běhu. |
| Čas zahájení v současné době nebo v budoucnu | Spustí první úlohu v zadaném čase zahájení. <p>Spustí budoucí úlohy na základě výpočtů z doby posledního spuštění. | Spustí první úlohu *nejdříve* čas zahájení, na základě plánu vypočítaného od počátečního času. <p>Spustí budoucí úlohy na základě zadaného plánu. <p>**Poznámka:** Pokud zadáte opakování s plánem, ale nezadejte hodiny nebo minuty pro plán, budou budoucí časy spuštění vypočteny pomocí hodin nebo minut od prvního běhu. |
||||

> [!IMPORTANT]
> Pokud opakování neurčují možnosti rozšířeného plánování, jsou budoucí opakování založena na čase posledního spuštění.
> Počáteční časy pro tyto opakování může posunzou kvůli faktorům, jako je latence během volání úložiště. Chcete-li se ujistit, že vaše aplikace logiky nezmešká opakování, zejména pokud je frekvence ve dnech nebo delších, použijte jednu z těchto možností:
> 
> * Zadejte čas zahájení opakování.
> 
> * Zadejte hodiny a minuty, kdy chcete spustit opakování pomocí **At these hours** a At these **minutes** vlastnosti.
> 
> * Použijte [aktivační událost posuvné okno](../connectors/connectors-native-sliding-window.md), nikoli aktivační událost opakování.

*Příklad času minulého zahájení a opakování, ale bez plánu*

Předpokládejme, že aktuální datum a čas je 8. Počáteční datum a čas zadáte jako 7.

| Čas spuštění | Aktuální čas | Opakování | Plán |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** v 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** v 13:00) | Každé dva dny | {none} |
|||||

Pro aktivační událost opakování modul Logic Apps vypočítá časy spuštění na základě času spuštění, zahodí časy minulého spuštění, použije další budoucí čas zahájení pro první spuštění a vypočítá budoucí spuštění na základě posledního běhu.

Toto opakování vypadá takto:

| Čas spuštění | Čas prvního spuštění | Budoucí časy běhu |
|------------|----------------|------------------|
| 2017-09-**07** v 14:00 | 2017-09-**09** v 14:00 | 2017-09-**11** v 14:00 </br>2017-09-**13** v 14:00 </br>2017-09-**15** v 14:00 </br>a tak dále... |
||||

Takže bez ohledu na to, jak daleko v minulosti zadáte čas zahájení, například 2017-09-**05** v 14:00 nebo 2017-09-**01** v 14:00, váš první běh vždy používá další budoucí čas zahájení.

Pro aktivační událost posuvné okno modul Logic Apps vypočítá časy spuštění na základě času zahájení, respektuje minulé časy spuštění, používá počáteční čas pro první spuštění a vypočítá budoucí spuštění na základě počátečního času.

Toto opakování vypadá takto:

| Čas spuštění | Čas prvního spuštění | Budoucí časy běhu |
|------------|----------------|------------------|
| 2017-09-**07** v 14:00 | 2017-09-**07** v 14:00 | 2017-09-**09** v 14:00 </br>2017-09-**11** v 14:00 </br>2017-09-**13** v 14:00 </br>2017-09-**15** v 14:00 </br>a tak dále... |
||||

Takže bez ohledu na to, jak daleko v minulosti zadáte čas zahájení, například 2017-09-**05** v 14:00 nebo 2017-09-**01** v 14:00, váš první běh vždy používá zadaný čas zahájení.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Příklad opakování

Zde jsou různé příklady opakování, které můžete nastavit pro aktivační události, které podporují možnosti:

| Trigger | Opakování | Interval | Frequency | Čas spuštění | V tyto dny | V těchto hodinách | V těchto minutách | Poznámka |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Opakování <br>Posuvné okno | Spustit každých 15 minut (bez počátečního data a času) | 15 | Minuta | {none} | {není k dispozici} | {none} | {none} | Tento plán začíná okamžitě, pak vypočítá budoucí opakování na základě času posledního spuštění. |
| Opakování <br>Posuvné okno | Spustit každých 15 minut (s počátečním datem a časem) | 15 | Minuta | *datum spuštění* T*startTime*Z | {není k dispozici} | {none} | {none} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas, pak vypočítá budoucí opakování na základě času posledního spuštění. |
| Opakování <br>Posuvné okno | Spustit každou hodinu, v hodinu (s počátečním datem a časem) | 1 | Hodina | *datum spuštění* Thh:00:00Z | {není k dispozici} | {none} | {none} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas. Budoucí opakování spustit každou hodinu na "00" minutové značky, která se počítá od počátečního času. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán běží pouze jeden den v týdnu nebo jeden den za měsíc. |
| Opakování <br>Posuvné okno | Spustit každou hodinu, každý den (bez počátečního data a času) | 1 | Hodina | {none} | {není k dispozici} | {none} | {none} | Tento plán se spustí okamžitě a vypočítá budoucí opakování na základě času posledního spuštění. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán běží pouze jeden den v týdnu nebo jeden den za měsíc. |
| Opakování <br>Posuvné okno | Spustit každou hodinu, každý den (s počátečním datem a časem) | 1 | Hodina | *datum spuštění* T*startTime*Z | {není k dispozici} | {none} | {none} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas, pak vypočítá budoucí opakování na základě času posledního spuštění. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán běží pouze jeden den v týdnu nebo jeden den za měsíc. |
| Opakování <br>Posuvné okno | Spustit každých 15 minut po hodině, každou hodinu (s počátečním datem a časem) | 1 | Hodina | *datum spuštění* T00:15:00Z | {není k dispozici} | {none} | {none} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas. Budoucí opakování spustit na "15" minutovou značku, která se počítá od počátečního času, takže v 00:15, 1:15 AM, 2:15 AM a tak dále. |
| Opakování | Spustit každých 15 minut po hodině, každou hodinu (bez počátečního data a času) | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Tento plán běží v 00:15, 1:15, 2:15 a tak dále. Tento plán je také ekvivalentní frekvenci "Hodina" a čas zahájení s "15" minut. |
| Opakování | Spouštět každých 15 minut při zadaných minutových značkách (bez počátečního data a času). | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán začíná až do další zadané 15minutové značky. |
| Opakování | Spouštějte denně v 8:00 *plus* minutovou značku od uložení aplikace logiky | 1 | Den | {none} | {není k dispozici} | 8 | {none} | Bez počáteční datum a čas, tento plán spustí na základě času při uložení aplikace logiky (PUT operace). |
| Opakování | Spustit denně v 8:00 (s počátečním datem a časem) | 1 | Den | *datum spuštění* T08:00:00Z | {není k dispozici} | {none} | {none} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas. Budoucí výskyty se spouštějí denně v 8:00. | 
| Opakování | Spustit denně v 8:30 (bez data a času zahájení) | 1 | Den | {none} | {není k dispozici} | 8 | 30 | Tento plán běží každý den v 8:30. |
| Opakování | Běh denně v 8:30 a 16:30 | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30 | |
| Opakování | Běh denně v 8:30, 8:45, 16:30 a 16:45 | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30, 45 | |
| Opakování | Spustit každou sobotu v 17:00 (bez počátečního data a času) | 1 | Týden | {none} | "Sobota" | 17 | 00 | Tento program běží každou sobotu v 17:00. |
| Opakování | Spustit každou sobotu v 17:00 (s počátečním datem a časem) | 1 | Týden | *datum spuštění* T17:00:00Z | "Sobota" | {none} | {none} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas, v tomto případě 9. Budoucí opakování projdou každou sobotu v 17:00. |
| Opakování | Spustit každé úterý, čtvrtek v 17:00 *plus* minutové značky od uložení aplikace logiky| 1 | Týden | {none} | "Úterý", "Čtvrtek" | 17 | {none} | |
| Opakování | Spustit každou hodinu během pracovní doby | 1 | Týden | {none} | Vyberte všechny dny kromě soboty a neděle. | Vyberte požadované hodiny dne. | Vyberte libovolné minuty hodiny, které chcete. | Pokud je například pracovní doba od 8:00 do 17:00, vyberte jako hodiny dne možnost "8, 9, 10, 11, 12, 13, 14, 15, 16, 17". <p>Pokud je pracovní doba od 8:30 do 17:30, vyberte předchozí hodiny dne plus "30" jako minuty hodiny. |
| Opakování | Běhjednou denně o víkendech | 1 | Týden | {none} | "Sobota", "Neděle" | Vyberte požadované hodiny dne. | Podle potřeby vyberte libovolné minuty hodiny. | Tento plán běží každou sobotu a neděli podle zadaného plánu. |
| Opakování | Spustit každých 15 minut dvakrát týdně pouze v pondělí | 2 | Týden | {none} | "Pondělí" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento rozvrh běží každé druhé pondělí na každých 15-minutové ochranné známky. |
| Opakování | Spustit každý měsíc | 1 | Month | *datum spuštění* T*startTime*Z | {není k dispozici} | {není k dispozici} | {není k dispozici} | Tento plán nezačíná *dříve* než zadané počáteční datum a čas a vypočítá budoucí opakování počátečního data a času. Pokud nezadáte počáteční datum a čas, tento plán použije datum a čas vytvoření. |
| Opakování | Spustit každou hodinu po dobu jednoho dne v měsíci | 1 | Month | {viz poznámka} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {viz poznámka} | Pokud nezadáte počáteční datum a čas, tento plán použije datum a čas vytvoření. Chcete-li řídit minuty pro plán opakování, zadejte minuty hodiny, čas zahájení nebo použijte čas vytvoření. Pokud je například čas zahájení nebo čas vytvoření 8:25, tento plán se spustí v 8:25, 9:25, 10:25 a tak dále. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Spustit pouze jednou

Pokud chcete spustit aplikaci logiky pouze najednou v budoucnu, můžete použít **šablonu Plánovač: Spustit jednou úlohy.** Po vytvoření nové aplikace **logiky,** ale před otevřením Návrháře logických aplikací, v části Šablony, v seznamu **Kategorie,** vyberte **plán**a pak vyberte tuto šablonu:

![Vyberte šablonu "Plánovač: Spustit jednou, když jste úlohy"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Nebo pokud můžete spustit aplikaci logiky s **Při přijetí požadavku HTTP - Požadavek** aktivační událost a předat čas zahájení jako parametr pro aktivační událost. Pro první akci použijte **Delay until - Schedule** akce a zadejte čas, kdy se spustí další akce.

## <a name="next-steps"></a>Další kroky

* [Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí aktivační události Opakování](../connectors/connectors-native-recurrence.md)
* [Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí aktivační události Posuvné okno](../connectors/connectors-native-sliding-window.md)
* [Pozastavení pracovních postupů pomocí akcí zpoždění](../connectors/connectors-native-delay.md)
