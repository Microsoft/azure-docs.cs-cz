---
title: Naplánovat opakované úkoly s trigger opakování – Azure Logic Apps
description: Naplánování a spuštění opakované automatizovaných úloh a pracovních postupů pomocí triggeru opakování v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297548"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Vytvářet, plánovat a spouštět opakované úlohy a pracovní postupy pomocí triggeru opakování v Azure Logic Apps

Chcete-li pravidelně spuštění úlohy, úlohy nebo procesy podle konkrétního plánu, můžete spustit pracovního postupu aplikace logiky pomocí integrované **opakování – plán** aktivační události. Můžete nastavit datum a čas, jakož i časové pásmo pro spuštění pracovního postupu a počet opakování pro opakující se tento pracovní postup. Pokud z nějakého důvodu promeškají opakování, Tato aktivační událost pokračuje v dalšího naplánovaného intervalu opakování. Další informace o předdefinovaných aktivačních událostech plánovače a akcích najdete v části [plán a opakované automatizované spuštění, úloh a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Tady jsou některé vzory, které tato aktivační událost podporuje pokročilejší opakování a komplexních plánů:

* Okamžité spuštění a opakovat každých *n* počet sekund, minuty, hodiny, dny, týdny nebo měsíce.

* Spusťte v určité datum a čas, pak spusťte a opakovat každých *n* počet sekund, minuty, hodiny, dny, týdny nebo měsíce.

* Spuštění a se opakovaly s jednou nebo několikrát denně, například v 8:00:00 a 17:00:00.

* Spuštění a opakovat každý týden, ale jenom pro konkrétní dny, jako je například sobotu a neděli.

* Spuštění a opakujte každý týden, ale jenom pro konkrétní dny a časy, jako je od pondělí do pátku v 8:00:00 a 17:00:00.

Rozdíly mezi této aktivační události a aktivační události posuvné okno nebo Další informace o plánování opakované pracovní postupy, přečtěte si téma [plán a opakované spouštění automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pokud chcete aktivovat svou aplikaci logiky a spustit pouze jednou v budoucích, viz [spuštění úlohy jednou](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [logic apps](../logic-apps/logic-apps-overview.md). Pokud se službou logic Apps teprve začínáte, přečtěte si [vytvoření vaší první aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Přidání triggeru opakování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Poté, co se zobrazí návrhář aplikace logiky, do vyhledávacího pole zadejte jako filtr "recurrence". Ze seznamu triggerů vyberte tento trigger jako první krok do pracovního postupu aplikace logiky: **Opakování**

   ![Vyberte trigger "Recurrence"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte vlastnosti tak, aby každý týden spuštění pracovního postupu.

   ![Nastavit interval a frekvence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Vlastnost | Požaduje se | Název JSON | Type | Popis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Ano | interval | Integer | Kladné celé číslo, který popisuje, jak často pracovní postup spouští na základě frekvence. Toto jsou minimální a maximální intervaly: <p>-Měsíc: 1 – 16 měsíců </br>-Den: 1 – 500 dnů </br>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud je interval 6 a je frekvence "Měsíc", pak opakování je každých 6 měsíců. |
   | **Frekvence** | Ano | frequency | String | Jednotka času pro opakování: **Druhý**, **minutu**, **hodinu**, **den**, **týden**, nebo **měsíc** |
   ||||||

   Další možnosti plánování, otevřete **přidat nový parametr** seznamu. 
   Všechny možnosti, které vyberete, zobrazí na aktivační událost po výběru.

   ![Rozšířené možnosti plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Vlastnost | Požaduje se | Název JSON | Type | Popis |
   |----------|----------|-----------|------|-------------|
   | **Časové pásmo** | Ne | timeZone | String | Platí, pouze pokud zadáte čas spuštění protože nepřijme tento trigger [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | Ne | startTime | String | Zadejte počáteční datum a čas v tomto formátu: <p>RRRR-MM-ddTHH Pokud vyberte časové pásmo <p>-nebo- <p>RRRR-MM-: ssZ Pokud nevyberete časové pásmo <p>Tak například, pokud chcete 18. září 2017 ve 14:00, zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, jako je například Tichomoří (běžný čas). Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas musí následovat [specifikace formátu ISO 8601 datum čas](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, je nutné přidat písmeno "Z" na konci bez mezer. Tato "Z" odkazuje na ekvivalentní [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>U jednoduchých plánů, počáteční čas je první výskyt, zatímco u složitějších plánů aktivační událost neaktivuje všechny dříve než čas spuštění. [*Jaké jsou způsoby, které lze použít počáteční datum a čas?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **V tyto dny** | Ne | weekDays | Řetězec nebo pole řetězců | Pokud vyberete "Týden", můžete vybrat jeden nebo více dní, kdy chcete spustit pracovní postup: **Pondělí**, **úterý**, **středa**, **čtvrtek**, **pátek**, **sobota**, a **Neděle** |
   | **V těchto hodinách** | Ne | hours | Integer nebo celočíselné pole | Pokud vyberete "Day" nebo "Týden", můžete vybrat jeden nebo více celá čísla od 0 do 23 jako hodin dne má ke spuštění pracovního postupu. <p><p>Pokud zadáte "číslo 10", "12" a "14", například získáte 10 AM, 12 hodin a 14: 00 hodin dne, ale dobu, po které den se počítají na základě při spuštění opakování. Pokud chcete nastavit dobu, po které den, zadejte hodnotu **v těchto minutách** vlastnost. |
   | **V těchto minutách** | Ne | minutes | Integer nebo celočíselné pole | Pokud vyberete "Day" nebo "Týden", můžete vybrat jeden nebo více celá čísla od 0 do 59 jako minuty v hodině, kdy chcete spustit pracovní postup. <p>Například můžete zadat "30" jako značku minutu a použijeme předchozí příklad hodin dne, získáte 10:30:00, 12:30 odp. a 2:30 odp. |
   |||||

   Předpokládejme například, že dnes je od pondělí 4. září 2017. Následující trigger opakování neaktivuje *všechny dřív* než počáteční datum a čas, který je od pondělí 18. září 2017 v 8:00 RÁNO PST. Plán opakování je ale nastavená pro 10:30:00, 12:30 odp. a 2:30 PM v pondělí pouze. To je poprvé, aktivuje se a vytvoří instanci pracovního postupu aplikace logiky je v 10:30:00. Další informace o jak časy zahájení práce, informace najdete v těchto [spustit příklady čas](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Budoucí spouštění následkem toho došlo na 12:30 odp. a 14:30:00 stejného dne. Každé opakování vytvoří vlastní instance pracovního postupu. Poté opakuje celý plán vše přes znovu příští pondělí. [*Jaké jsou některé další výskyty příklad?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Rozšířený příklad plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Aktivační událost se zobrazí náhled pro zadané opakování jenom v případě, že vyberete jako frekvence "Den" nebo "Týden".

1. Nyní vytvoření zbývající pracovního postupu pomocí dalších akcí. Další akce, které můžete přidat, naleznete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definice pracovního postupu – opakování

V aplikaci logiky základní definice pracovního postupu, které využívá JSON, můžete zobrazit [definice aktivační události opakování](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) s možnostmi, které jste zvolili. Chcete-li zobrazit tuto definici na panelu nástrojů návrháře, zvolte **zobrazení kódu**. Chcete-li vrátit do návrháře, zvolte na panelu nástrojů návrháře **návrháře**.

Tento příklad ukazuje, jak může vypadat definice aktivační události opakování v základní definici pracovního postupu:

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
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Další postup

* [Pozastavit pracovních postupů s akcemi zpoždění](../connectors/connectors-native-delay.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)
