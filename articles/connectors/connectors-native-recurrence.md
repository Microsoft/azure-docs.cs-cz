---
title: Naplánování a spuštění automatizovaných úloh a pracovních postupů s Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace naplánovaných a opakované úkoly s konektorem opakování v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
tags: connectors
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: 369bdba063f8582b8343682dcbbc990d2f63e21a
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078061"
---
# <a name="create-and-run-recurring-tasks-and-workflows-with-azure-logic-apps"></a>Vytváření a spouštění opakovaných úloh a pracovních postupů s Azure Logic Apps

Naplánování akcí, úlohy a procesy, které pravidelně spouštět vytvořit pracovní postup aplikace logiky, která začíná **plán – opakování** [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts). Můžete nastavit datum a čas pro spuštění pracovního postupu a plán opakování pro provádění úloh, jako jsou tyto příklady a další:

* Získáte interní data: [Spuštění uložené procedury SQL](../connectors/connectors-create-api-sqlazure.md) každý den.
* Načíst externí data: Načítat zprávy o počasí NOAA každých 15 minut.
* Data sestavy: E-mailu souhrnné informace pro všechny objednávky větší než konkrétní částku v minulém týdnu.
* Zpracování dat: Komprese nahraných obrázků s dnešní každý den v týdnu, hodiny mimo špičku.
* Vyčištění dat: Odstraňte všechny tweety, které jsou starší než tři měsíce.
* Archivace dat: Push faktury ke službě zálohování každý měsíc.

Tato aktivační událost podporuje mnoho vzory, třeba:

* Okamžité spuštění a opakovat každých *n* počet sekund, minuty, hodiny, dny, týdny nebo měsíce.
* Začínají na určitý čas, pak spustit a opakovat každých *n* počet sekund, minuty, hodiny, dny, týdny nebo měsíce.
* Spuštění a se opakovaly s jednou nebo několikrát denně, například v 8:00:00 a 17:00:00.
* Spuštění a opakovat každý týden, ale jenom pro konkrétní dny, jako je například sobotu a neděli.
* Spuštění a opakujte každý týden, ale jenom pro konkrétní dny a časy, jako je od pondělí do pátku v 8:00:00 a 17:00:00.

Když se trigger opakování aktivuje pokaždé, když, Logic Apps vytvoří a spustí novou instanci pracovního postupu aplikace logiky. 

Okamžitě aktivovat svou aplikaci logiky a spusťte jednou bez opakovaného najdete v tématu [spuštění úlohy jednou](#run-once) dále v tomto tématu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Jinak si můžete [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Přidat trigger opakování pro aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky, nebo si přečtěte [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Až se návrhář pro Logic Apps se zobrazí pod vyhledávacím polem zvolte **všechny**. Do vyhledávacího pole zadejte jako filtr "opakování". Ze seznamu triggerů vyberte tento trigger: **Opakování – plán** 

   ![Vyberte trigger "Plán – opakování"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Tato aktivační událost je teď na první krok ve své aplikaci logiky.

3. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte vlastnosti tak, aby každý týden spuštění pracovního postupu. 

   ![Nastavit interval a frekvence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Další možnosti plánování, zvolte **zobrazit pokročilé možnosti**. 

   ![Další možnosti](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Nyní můžete nastavit tyto možnosti: 

   * Nastavte počáteční datum a čas pro aktivaci triggeru. 
   Pokud chcete zadat počáteční datum a čas, můžete také použít časové pásmo. 

   * Pokud vyberete "Day" nebo "Týden" frekvence, můžete vybrat konkrétní časy pro opakování. 

   * Pokud vyberete "Týden", můžete vybrat konkrétní dny v týdnu příliš.
   
   ![Rozšířené možnosti plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Předpokládejme například, že dnes je od pondělí 4. září 2017. 
   Následující trigger opakování neaktivuje *všechny dřív* než počáteční datum a čas, který je od pondělí 18. září 2017 v 8:00 RÁNO PST. 
   Plán opakování je ale nastavená pro 10:30:00, 12:30 odp. a 2:30 PM v pondělí pouze. To je poprvé, aktivuje se a vytvoří instanci pracovního postupu aplikace logiky je v 10:30:00. 
   Další informace o jak časy zahájení práce, informace najdete v těchto [spustit příklady čas](#start-time).
   Budoucí spouštění následkem toho došlo na 12:30 odp. a 14:30:00 stejného dne. 
   Každé opakování vytvoří vlastní instance pracovního postupu. Poté opakuje celý plán vše přes znovu příští pondělí. 
   [*Jaké jsou některé další výskyty příklad?*](#example-recurrences)

   ![Rozšířený příklad plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Aktivační událost se zobrazí náhled pro zadané opakování jenom v případě, že vyberete jako frekvence "Den" nebo "Týden".
   
6. Nyní můžete vytvořit zbývající pracovního postupu akce nebo tok řídicí příkazy. Další akce, které můžete přidat, naleznete v tématu [konektory](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Podrobnosti o triggeru

Můžete nakonfigurovat tyto vlastnosti pro trigger opakování.

| Název | Požaduje se | Název vlastnosti | Typ | Popis | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frekvence** | Ano | frequency | Řetězec | Jednotka času pro opakování: **Druhý**, **minutu**, **hodinu**, **den**, **týden**, nebo **měsíc** | 
| **Interval** | Ano | interval | Integer | Kladné celé číslo, který popisuje, jak často pracovní postup spouští na základě frekvence. <p>Výchozí interval je 1. Toto jsou minimální a maximální intervaly: <p>-Měsíc: 1 – 16 měsíců </br>-Den: 1 – 500 dnů </br>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud je interval 6 a je frekvence "Měsíc", pak opakování je každých 6 měsíců. | 
| **Časové pásmo** | Ne | timeZone | Řetězec | Platí, pouze pokud zadáte čas spuštění protože nepřijme tento trigger [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. | 
| **Čas spuštění** | Ne | startTime | Řetězec | Zadejte čas spuštění v tomto formátu: <p>RRRR-MM-ddTHH Pokud vyberte časové pásmo <p>-nebo- <p>RRRR-MM-: ssZ Pokud nevyberete časové pásmo <p>Tak například, pokud chcete 18. září 2017 ve 14:00, zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, jako je například tichomořského času. Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas musí následovat [specifikace formátu ISO 8601 datum čas](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, je nutné přidat písmeno "Z" na konci bez mezer. Tato "Z" odkazuje na ekvivalentní [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>U jednoduchých plánů, počáteční čas je první výskyt, zatímco u složitějších plánů aktivační událost neaktivuje všechny dříve než čas spuštění. [*Jaké jsou způsoby, které lze použít počáteční datum a čas?*](#start-time) | 
| **V tyto dny** | Ne | weekDays | Řetězec nebo pole řetězců | Pokud vyberete "Týden", můžete vybrat jeden nebo více dní, kdy chcete spustit pracovní postup: **Pondělí**, **úterý**, **středa**, **čtvrtek**, **pátek**, **sobota**, a **Neděle** | 
| **V těchto hodinách** | Ne | hours | Integer nebo celočíselné pole | Pokud vyberete "Day" nebo "Týden", můžete vybrat jeden nebo více celá čísla od 0 do 23 jako hodin dne, kdy chcete spustit pracovní postup. <p>Například pokud zadáte "číslo 10", "12" a "14", získáte 10 AM, 12 hodin a 14: 00 jako značky hodinu. | 
| **V těchto minutách** | Ne | minutes | Integer nebo celočíselné pole | Pokud vyberete "Day" nebo "Týden", můžete vybrat jeden nebo více celá čísla od 0 do 59 jako minuty v hodině, kdy chcete spustit pracovní postup. <p>Například můžete zadat "30" jako značku minutu a použijeme předchozí příklad hodin dne, získáte 10:30:00, 12:30 odp. a 2:30 odp. | 
||||| 

## <a name="json-example"></a>Příklad JSON

Tady je příklad [definice aktivační události opakování](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="faq"></a>Nejčastější dotazy

<a name="run-once"></a>

**DOTAZ:** Co když chci spustit okamžitě a jednou pouze aplikaci logiky? </br>
**ODPOVĚĎ:** K aktivaci vaší aplikace logiky hned a spusťte jednou bez opakování, můžete použít **plánovače: Spustit jednou úlohy** šablony. Po vytvoření nové aplikace logiky, ale před otevřením v návrháři pro Logic Apps **šablony** části z **kategorie** seznamu vyberte **plán**a pak vyberte šablony:

![Vyberte "plánovače: Spustit jednou úlohy"šablony](./media/connectors-native-recurrence/choose-run-once-template.png)

Nebo, pokud používáte šablony prázdné aplikace logiky, aplikaci logiky s spustit **přijetí požadavku HTTP při - požadavek** aktivační událost. Počáteční čas triggeru předáte jako parametr. Na další krok, přidejte **zpoždění do – naplánovat** akce a zadejte čas při spuštění další akci.

<a name="example-recurrences"></a>

**DOTAZ:** Jaké jsou další příklady plánů opakování? </br>
**ODPOVĚĎ:** Tady jsou další příklady:

| Opakování | Interval | Frekvence | Počáteční čas | V tyto dny | V těchto hodinách | V těchto minutách | Poznámka |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Spuštění každých 15 minut (žádná počáteční datum a čas) | 15 | Minuta | {none} | {není k dispozici} | {none} | {none} | Tento plán okamžitě spustí a pak vypočítá budoucí opakování podle času posledního spuštění. | 
| Spuštění každých 15 minut (s počáteční datum a čas) | 15 | Minuta | *startDate*T*startTime*Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadané datum a čas, pak vypočítá budoucí opakování podle času posledního spuštění. | 
| Spuštění každou hodinu v celou hodinu (pomocí počáteční datum a čas) | 1 | Hodina | *startDate*Thh:00:00Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaný počáteční datum a čas. Budoucí opakování spouštět každou hodinu v značkou "00" minuta. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán se spustí v uvedeném pořadí pouze jeden den v týdnu nebo jeden den za měsíc. | 
| Spuštění každou hodinu, každý den (žádná počáteční datum a čas) | 1 | Hodina | {none} | {není k dispozici} | {none} | {none} | Tento plán okamžitě spustí a vypočítá budoucí opakování podle času posledního spuštění. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán se spustí v uvedeném pořadí pouze jeden den v týdnu nebo jeden den za měsíc. | 
| Spuštění každou hodinu, každý den (s počáteční datum a čas) | 1 | Hodina | *startDate*T*startTime*Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadané datum a čas, pak vypočítá budoucí opakování podle času posledního spuštění. <p>Pokud je frekvence "Týden" nebo "Měsíc", tento plán se spustí v uvedeném pořadí pouze jeden den v týdnu nebo jeden den za měsíc. | 
| Spustit každých 15 minut po hodině, každou hodinu (s počáteční datum a čas) | 1 | Hodina | *startDate*T00:15:00Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas spuštění v 00:15, 1:15:00, 2:15:00 a tak dále. | 
| Spuštění každých 15 minut po hodině, každou hodinu (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Tento plán se spustí v 00:15, 1:15:00, 2:15:00, a tak dále. Tento plán je také ekvivalentní frekvencí "Hodina" a "15" minut čas spuštění. | 
| Spuštění každých 15 minut na značce 15 minut (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se nespustí, dokud další zadané značky 15 minut. | 
| Spuštění v 8:00 každý den (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 8 | {none} | Tento plán se spustí v 8:00 hodin každý den podle zadaného plánu. | 
| Spuštění v 8:00 každý den (s počáteční datum a čas) | 1 | Den | *startDate*T08:00:00Z | {není k dispozici} | {none} | {none} | Tento plán se spustí 8:00 hodin každý den podle zadaný čas začátku. | 
| Spustit v 8:30:00 každý den (žádná počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 8 | 30 | Tento plán se spustí v 8:30:00 každý den podle zadaného plánu. | 
| Spustit v 8:30:00 každý den (s počáteční datum a čas) | 1 | Den | *startDate*T08:30:00Z | {není k dispozici} | {none} | {none} | Tento plán se spustí v zadaný počáteční datum v 8:30:00. | 
| Spuštění v 8:30:00 a 16:30:00 každý den | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30 | | 
| Spustit v 8:30:00, 8:45:00, 16:30:00 a 16:45:00 každý den | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30, 45 | | 
| Spusťte každou sobotu v 17: 00 (žádná počáteční datum a čas) | 1 | Týden | {none} | "Sobota" | 17 | 00 | Tento plán se spustí každou sobotu v 17:00:00. | 
| Spusťte každou sobotu v 17: 00 (s počáteční datum a čas) | 1 | Týden | *startDate*T17:00:00Z | "Sobota" | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaný počáteční datum a čas, v tomto případě 9. září 2017 v 17:00:00. Budoucí opakování spusťte každou sobotu v 17:00:00. | 
| Spuštění každé úterý, čtvrtek v 17: 00 | 1 | Týden | {none} | "Úterý", "Čtvrtek" | 17 | {none} | Tento plán se spustí každé úterý a čtvrtek v 17:00:00. | 
| Spuštění každou hodinu během pracovní doby | 1 | Týden | {none} | Vyberte všechny dny, s výjimkou sobotu a neděli. | Vyberte hodiny dne, který chcete. | Vyberte všechny minuty v hodině, který chcete. | Například pokud 8:00:00 do 17:00 hodin pracovní doby, zvolte "8, 9, 10, 11, 12, 13, 14, 15, 16 a 17" jako hodin dne. <p>Pokud pracovní doby 8:30:00 do 5:30 PM, vyberte předchozí hodiny dne plus "30" jako minuty v hodině. | 
| Spustit jednou pro každý den o víkendech | 1 | Týden | {none} | "Sobota", "Neděle" | Vyberte hodiny dne, který chcete. | Vyberte všechny minuty v hodině, podle potřeby. | Tento plán se spustí podle nastaveného plánu každou sobotu a neděli. | 
| Spuštění každých 15 minut jednou za dva týdny v pondělí pouze | 2 | Týden | {none} | "Pondělí" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se spustí každý druhý pondělí na značce každých 15 minut. | 
| Spuštění každou hodinu za jeden den za měsíc | 1 | Měsíc | {Viz poznámka} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Viz poznámka} | Pokud nechcete zadat počáteční datum a čas, používá tento plán datum a čas vytvoření. Pokud chcete řídit minut u plánu opakování, zadejte minuty v hodině, čas zahájení, nebo použijte čas vytvoření. Například pokud časem spuštění a čas vytvoření 8:25:00, tento plán se spouští v 8:25:00, 9:25 AM, 10:25 AM, a tak dále. | 
||||||||| 

<a name="start-time"></a>

**DOTAZ:** Jaké jsou způsoby, které lze použít počáteční datum a čas? </br>
**ODPOVĚĎ:** Tady jsou některé vzory, které ukazují, jak můžete řídit opakování s počáteční datum a čas a jak modul Logic Apps provede tyto opakování:

| Počáteční čas | Opakování bez plánu | Opakování s plánem | 
| ---------- | --------------------------- | ------------------------ | 
| {none} | První úloha se spustí okamžitě. <p>Spustí budoucí úlohy založené na čas posledního spuštění. | První úloha se spustí okamžitě. <p>Umožňuje spouštět budoucí úlohy podle zadaného plánu. | 
| Počáteční čas v minulosti | Vypočítá doba spuštění na základě zadaný čas začátku a zahození za posledních spustit časy. Spuštění první úlohy v další budoucí čas spuštění. <p>Spustí budoucí úlohy založené na výpočty z posledního času spuštění. <p>Podrobnější vysvětlení najdete v příkladu za touto tabulkou. | Spuštění první úlohy *až po* než čas spuštění podle plánu vypočítaného z času začátku. <p>Umožňuje spouštět budoucí úlohy podle zadaného plánu. <p>**Poznámka:** Je-li zadat počet opakování s plánem, ale nezadávejte hodin nebo minut, než plán, pak budoucích časů spuštění vypočítají na základě hodin nebo minut, v uvedeném pořadí, od prvního spuštění. | 
| Čas spuštění v současné době nebo v budoucnu | Spuštění první úlohy v zadaný čas začátku. <p>Spustí budoucí úlohy založené na výpočty z posledního času spuštění. | Spuštění první úlohy *až po* než čas spuštění podle plánu vypočítaného z času začátku. <p>Umožňuje spouštět budoucí úlohy podle zadaného plánu. <p>**Poznámka:** Je-li zadat počet opakování s plánem, ale nezadávejte hodin nebo minut, než plán, pak budoucích časů spuštění vypočítají na základě hodin nebo minut, v uvedeném pořadí, od prvního spuštění. | 
||||

**Příklad pro poslední počáteční čas opakování, ale žádný plán** 

| Počáteční čas | Aktuální čas | Opakování | Plán |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Každé 2 dny | {none} | 
||||| 

V tomto scénáři, Logic Apps vypočítá modul runtime podle času spuštění zahodí minulé doby spuštění a používá další budoucí čas zahájení pro první spuštění. Po prvním spuštění budoucí spouštění vycházejí z plánu vypočítaného z času začátku. Zde je, jak bude vypadat toto opakování:

| Počáteční čas | První čas spuštění | Budoucí čas spuštění | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** ve 14:00 | 2017-09 -**09** ve 14:00 | 2017-09 -**11** ve 14:00 </br>2017-09 -**13** ve 14:00 </br>2017-09 -**15** ve 14:00 </br>a tak dále.
||||| 

Takže pro tento scénář, bez ohledu na to, jak daleko v minulosti zadáte počáteční čas, například 2017-09 -**05** ve 14:00 nebo 2017-09 -**01** ve 14:00:00, první čas spuštění je stejný.

## <a name="next-steps"></a>Další postup

* [Triggery a akce pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Konektory](../connectors/apis-list.md)
