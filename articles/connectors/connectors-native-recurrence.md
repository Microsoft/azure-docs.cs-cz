---
title: Plán úlohy a pravidelně spouštění pracovních postupů – Azure Logic Apps | Microsoft Docs
description: Vytvoření a plánování pravidelně spuštěné úlohy, akce, pracovní postupy, procesy a úloh s logic apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 3bd396355681cdde486cfbea7004c9c1aece09da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296783"
---
# <a name="create-and-schedule-regularly-running-tasks-with-azure-logic-apps"></a>Vytvoření a naplánovat pravidelné spuštěné úlohy službou Azure Logic Apps

Při plánování úloh, akce, úlohy a procesy, které pravidelně spouštět, můžete vytvořit aplikaci logiky pracovního postupu, který začíná **plán - opakování** [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts). Pomocí této aktivační události můžete nastavit datum a čas pro spuštění opakování a plán opakování pro provádění úloh, jako jsou tyto příklady a další:

* Získat interních datových: [spustit uloženou proceduru SQL](../connectors/connectors-create-api-sqlazure.md) každý den.
* Načíst externí data: načítat zprávy o počasí z NOAA každých 15 minut.
* Data sestavy: e-mailu souhrnu pro všechna objednávek větší než určitou velikostí minulého týdne.
* Zpracování dat: Compress dnes odeslal bitové kopie každý den v týdnu, hodiny mimo špičku.
* Vyčištění dat: Odstraňte všechny tweetů starší než tři měsíce.
* Archivaci dat: nabízenou faktury ke službě zálohování každý měsíc.

Této aktivační události podporuje mnoho vzory, třeba:

* Spustit okamžitě a opakovat každých *n* počet sekund, minut, hodiny, dny, týdny nebo měsíce.
* Spuštění na určitý čas, pak spusťte a opakovat každých *n* počet sekund, minut, hodiny, dny, týdny nebo měsíce.
* Spusťte a opakujte na jeden nebo více časy denně, například v 8:00:00 a 17:00:00.
* Spusťte a opakovat každý týden, ale jenom pro konkrétní dny, jako je například sobota a neděle.
* Spusťte a opakovat každý týden, ale jenom pro konkrétní dny a časy, jako je například od pondělí do pátku v 8:00:00 a 17:00:00.

Při opakování aktivační událost se aktivuje vždy, když, Logic Apps vytvoří a spustí novou instanci pracovního postupu aplikace logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Jinak si můžete [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Přidat do aplikace logiky aktivační událost opakování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření aplikace logiky prázdné nebo další [postup vytvoření aplikace logiky prázdné](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Jakmile se objeví návrhář aplikace logiky do vyhledávacího pole zadejte "recurrence" jako filtr. Vyberte **plán - opakování** aktivační události. 

   ![Plán - aktivační událost opakování](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Této aktivační události je nyní prvním krokem při aplikaci logiky.

3. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte tyto vlastnosti k workflow spouštět každý týden. 

   ![Interval sady a frekvence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Pro další možnosti plánování, zvolte **zobrazit rozšířené možnosti**. 

   ![Další možnosti](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Nyní můžete nastavit tyto možnosti: 

   * Nastavte počáteční datum a čas pro ohlásí aktivační událost. 
   Pokud zadáte počáteční datum a čas, můžete taky použít časové pásmo. 

   * Pokud vyberete "Dne" nebo "Týden" frekvence, můžete vybrat konkrétní časy pro opakování. 

   * Pokud zvolíte možnost "Týden", můžete vybrat konkrétní dny v týdnu příliš.
   
   ![Rozšířené možnosti plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Předpokládejme například, že dnes je pondělí 4 září 2017. 
   Následující aktivační událost opakování není fire *všechny dřív* než počáteční datum a čas, který je pondělí 18 září 2017 v 8:00 AM PST. 
   Nicméně je nastavit plán opakování pro 10:30 AM, 12:30:00 a 14:30:00 v pondělí jenom. Proto poprvé, aktivační událost aktivuje a vytvoří instanci pracovního postupu logiku aplikace je na 10:30 AM. 
   Další informace o jak časy spuštění pracovních, najdete v těchto [spustit čas příklady](#start-time).
   Budoucí spustí dojít na 12:30:00 a 14:30:00 ve stejný den. 
   Každý opakování vytvoří vlastní instanci pracovního postupu. Potom celý plán opakuje všechny přes znovu další pondělí. 
   [*Jaké jsou některé další příklad výskytů?*](#example-recurrences)

   ![Pokročilé plánování příklad](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Aktivační událost ukazuje náhledu pro zadané opakování, pouze když zvolíte "Dne" nebo "Týden" jako frekvence.
   
6. Nyní sestavení zbývající pracovního postupu s akcemi nebo toku řídicí příkazy. Další akce, které můžete přidat, naleznete v části [konektory](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Podrobnosti o aktivační události

Můžete nakonfigurovat tyto vlastnosti pro aktivační událost opakování.

| Název | Požaduje se | Název vlastnosti | Typ | Popis | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frekvence** | Ano | frequency | Řetězec | Jednotka času pro opakování: **druhý**, **minutu**, **hodinu**, **den**, **týden**, nebo  **Měsíc** | 
| **Interval** | Ano | interval | Integer | Kladné celé číslo, které popisuje, jak často pracovní postup bude spuštěn na základě četnosti. <p>Výchozí interval je 1. Toto jsou minimální a maximální hodnotou: <p>-Měsíc: 1-16 měsíců </br>-Den: 1-500 dnů </br>-Hodina: 1-12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Druhý: 1-9,999,999 sekund<p>Například pokud je interval 6 a frekvenci "Měsíc", pak opakování je každých 6 měsíců. | 
| **Časové pásmo** | Ne | timeZone | Řetězec | Platí, pouze pokud zadáte počáteční čas protože této aktivační události není přijmout [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. | 
| **Čas spuštění** | Ne | startTime | Řetězec | Zadejte čas spuštění v tomto formátu: <p>RRRR-MM-ddTHH Pokud vyberete časové pásmo <p>-nebo- <p>RRRR-MM-ddTHH Pokud nevyberete časové pásmo <p>Tak například, pokud chcete 18 září 2017 na 2:00 PM, zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, jako je například Tichomoří (čas). Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** tento čas zahájení musí následovat [ISO 8601 datum čas specifikace](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, musíte přidat písmeno "Z" bez žádné mezery na konci. Tento "Z" odkazuje na ekvivalent [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány, čas spuštění je první výskyt, zatímco pro komplexní plány, aktivační událost není fire všechny dřív, než čas spuštění. [*Jaké jsou způsoby, které lze použít počáteční datum a čas?*](#start-time) | 
| **V tyto dny** | Ne | weekDays | Řetězec nebo pole řetězců | Pokud zvolíte možnost "Týden", můžete vybrat jeden nebo více dní, kdy chcete spustit pracovní postup: **pondělí**, **úterý**, **středa**, **čtvrtek** , **Pátek**, **sobota**, a **neděle** | 
| **V těchto hodinách** | Ne | hours | Celé číslo nebo číslo pole | Pokud vyberete "Dne" nebo "Týden", můžete vybrat jeden nebo více celých čísel od 0 do 23 jako hodin dne, kdy chcete spustit pracovní postup. <p>Například pokud zadáte "10", "12" a "14", získáte 10 AM, 12 PM a 14: 00 jako značky hodinu. | 
| **V těchto minutách** | Ne | minutes | Celé číslo nebo číslo pole | Pokud vyberete "Dne" nebo "Týden", můžete vybrat jeden nebo více celých čísel od 0 do 59 minut hodina, kdy chcete spustit pracovní postup. <p>Například můžete zadat "30" jako minutu značky a použijeme předchozí příklad hodin dne, získáte 10:30 AM, 12:30:00 a 14:30:00. | 
||||| 

## <a name="json-example"></a>Příklad JSON

Tady je příklad [definici aktivace opakování](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
{
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
}
```

## <a name="faq"></a>Nejčastější dotazy

<a name="example-recurrences"></a>

**Otázka:** co jsou další příklad opakování plány? </br>
**Odpověď:** Zde jsou další příklady:

| Opakování | Interval | Frekvence | Čas spuštění | V tyto dny | V těchto hodinách | V těchto minutách | Poznámka |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Spustit každých 15 minut (žádné počáteční datum a čas) | 15 | Minuta | {none} | {není k dispozici} | {none} | {none} | Tento plán okamžitě spustí a pak vypočítá budoucí opakování podle času posledního spuštění. | 
| Spustit každých 15 minut (s počáteční datum a čas) | 15 | Minuta | *startDate*T*startTime*Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas, pak vypočítá budoucí opakování podle času posledního spuštění. | 
| Spouštět každou hodinu na hodinu (s počáteční datum a čas) | 1 | Hodina | *startDate*Thh:00:00Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas. Budoucí opakování spouštět každou hodinu na "00" minutu značce. <p>Pokud je četnost "Týden" nebo "Měsíc", tento plán spouští v uvedeném pořadí pouze jeden den týdně nebo měsíčně jeden den. | 
| Spouštět každou hodinu, každý den (žádné počáteční datum a čas) | 1 | Hodina | {none} | {není k dispozici} | {none} | {none} | Tento plán okamžitě spustí a vypočítá budoucí opakování podle času posledního spuštění. <p>Pokud je četnost "Týden" nebo "Měsíc", tento plán spouští v uvedeném pořadí pouze jeden den týdně nebo měsíčně jeden den. | 
| Spouštět každou hodinu, každý den (s počáteční datum a čas) | 1 | Hodina | *startDate*T*startTime*Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas, pak vypočítá budoucí opakování podle času posledního spuštění. <p>Pokud je četnost "Týden" nebo "Měsíc", tento plán spouští v uvedeném pořadí pouze jeden den týdně nebo měsíčně jeden den. | 
| Spustit každých 15 minut po hodině, každou hodinu (s počáteční datum a čas) | 1 | Hodina | *startDate*T00:15:00Z | {není k dispozici} | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas, spuštěné v 00:15 AM, 1:15:00, 2:15:00 a tak dále. | 
| Spustit každých 15 minut po hodině, každou hodinu (žádné počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Tento plán spouští v 00:15 AM, 1:15:00, 2:15:00, a tak dále. Navíc tento plán je ekvivalentní četností "Hodina" a čas spuštění s "15" minut. | 
| Spustit každých 15 minut na značce 15 minut (žádné počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán nelze spustit, dokud další zadaný označit 15 minut. | 
| Spustit v 8:00 AM každý den (žádné počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 8 | {none} | Tento plán spouští v 8:00 AM každý den, na základě zadaného plánu. | 
| Spustit v 8:00 AM každý den (s počáteční datum a čas) | 1 | Den | *startDate*T08:00:00Z | {není k dispozici} | {none} | {none} | Tento plán spustí 8:00 AM každý den podle zadaným časem spuštění. | 
| Spustit v 8:30 AM každý den (žádné počáteční datum a čas) | 1 | Den | {none} | {není k dispozici} | 8 | 30 | Tento plán spouští v 8:30 AM každý den, na základě zadaného plánu. | 
| Spustit v 8:30 AM každý den (s počáteční datum a čas) | 1 | Den | *startDate*T08:30:00Z | {není k dispozici} | {none} | {none} | Tento plán se spustí v zadaný počáteční datum v 8:30:00. | 
| Spustit v 8:30 AM a 4:30 PM každý den | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30 | | 
| Spustit v 8:30 AM, 8:45 AM, 4:30 PM a 4:45 PM každý den | 1 | Den | {none} | {není k dispozici} | 8, 16 | 30, 45 | | 
| Spusťte každou sobotu v 17: 00 (žádné počáteční datum a čas) | 1 | Týden | {none} | "Sobota" | 17 | 00 | Tento plán spouští každou sobotu v 17:00:00. | 
| Spusťte každou sobotu v 17: 00 (s počáteční datum a čas) | 1 | Týden | *startDate*T17:00:00Z | "Sobota" | {none} | {none} | Nelze spustit tento plán *všechny dřív* než zadaná počáteční datum a čas, v takovém případě 9 září 2017 v 17:00:00. Budoucí opakování spusťte každou sobotu v 17:00:00. | 
| Spustit každé úterý, čtvrtek v 17: 00 | 1 | Týden | {none} | "Úterý", "Čtvrtek" | 17 | {none} | Tento plán spouští každé úterý a čtvrtek v 17:00:00. | 
| Spouštět každou hodinu během pracovní doby | 1 | Týden | {none} | Vyberte všechny dny s výjimkou sobota a neděle. | Vyberte hodiny dne, který chcete. | Vyberte všechny minut za hodinu, který chcete. | Například pokud vaše pracovní dobu 8:00 do 5:00 hodin, pak vyberte "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako hodin dne. <p>Pokud vaše pracovní dobu 8:30:00 do 5:30 PM, vyberte předchozí hodiny dne plus "30" minut za hodinu. | 
| Spustit jednou pro každý den o víkendech | 1 | Týden | {none} | "Sobota", "Neděle" | Vyberte hodiny dne, který chcete. | Vyberte libovolný počet minut za hodinu podle potřeby. | Tento plán spouští každou sobotu a neděli podle nastaveného plánu. | 
| Spustit každých 15 minut v pondělí pouze jednou za dva týdny | 2 | Týden | {none} | "Pondělí" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Tento plán se spustí každý druhý pondělí na značce každých 15 minut. | 
| Spouštět každou hodinu pro jeden den za měsíc | 1 | Měsíc | {Viz poznámka} | {není k dispozici} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Viz poznámka} | Pokud nezadáte počáteční datum a čas, použije tento plán vytváření datum a čas. K řízení minut pro plán opakování, zadejte počet minut hodinu, čas zahájení, nebo použijte čas vytvoření. Například pokud časem spuštění a čas vytvoření 8:25 AM, tento plán, které se spouští v 8:25:00, 9:25:00, 10:25 AM, a tak dále. | 
||||||||| 

<a name="start-time"></a>

**Otázka:** co jsou způsoby, které lze použít počáteční datum a čas? </br>
**Odpověď:** tady jsou některé vzorů, které ukazují, jak můžete řídit opakování s počáteční datum a čas, a jak modul Logic Apps provede tyto opakování:

| Čas spuštění | Opakování bez plánu | Opakování s plánem | 
| ---------- | --------------------------- | ------------------------ | 
| {none} | První úlohy se spustí okamžitě. <p>Běží budoucí úlohy podle času posledního spuštění. | První úlohy se spustí okamžitě. <p>Spustí budoucí úlohy podle zadaného plánu. | 
| Počáteční čas v minulosti. | Vypočítá doby běhu na základě zadaným časem spuštění a časy zahození za posledních spustit. V další budoucí běh spustí první zatížení. <p>Spustí budoucí úlohy podle na výpočty z času posledního spuštění. <p>Další vysvětlení najdete v příkladu za touto tabulkou. | Spustí první úloha *již dříve* než čas zahájení, na základě plánu vypočítaných z čas spuštění. <p>Spustí budoucí úlohy podle zadaného plánu. <p>**Poznámka:** Pokud zadat plán opakování, ale nezadávejte hodinách nebo minutách pro plán, pak budoucí doby běhu se počítají pomocí hodinách nebo minutách, v uvedeném pořadí, od prvního spuštění. | 
| Počáteční čas v současné době nebo v budoucnosti | První úlohy se spustí v určený čas spuštění. <p>Spustí budoucí úlohy podle na výpočty z času posledního spuštění. | Spustí první úloha *již dříve* než čas zahájení, na základě plánu vypočítaných z čas spuštění. <p>Spustí budoucí úlohy podle zadaného plánu. <p>**Poznámka:** Pokud zadat plán opakování, ale nezadávejte hodinách nebo minutách pro plán, pak budoucí doby běhu se počítají pomocí hodinách nebo minutách, v uvedeném pořadí, od prvního spuštění. | 
||||

**Příklad pro poslední čas spuštění s opakování, ale žádný plán** 

| Čas spuštění | Aktuální čas | Opakování | Plán |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Za dva dny | {none} | 
||||| 

V tomto scénáři Logic Apps modul počítá podle času zahájení času spuštění zahodí uplynula doby běhu a používá další budoucí počáteční čas pro během prvního spuštění. Po této první běh jsou budoucí spustí podle plánu vypočítaných z čas spuštění. Zde je, jak vypadá toto opakování:

| Čas spuštění | První běhu | Budoucí času spuštění | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** ve 2:00 | 2017-09 -**09** ve 2:00 | 2017-09 -**11** ve 2:00 </br>2017-09 -**13** ve 2:00 </br>2017-09 -**15** ve 2:00 </br>a tak dále...
||||| 

Proto pro tento scénář, bez ohledu na tom, jak daleko v minulosti Určuje počáteční čas, například 2017-09 -**05** na 2:00 PM nebo 2017-09 -**01** na 2:00 PM, první běh je stejný.

## <a name="next-steps"></a>Další postup

* [Triggery a akce pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Konektory](../connectors/apis-list.md)
