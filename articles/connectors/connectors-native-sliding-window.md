---
title: Plánování úloh pro zpracování souvislých dat
description: Vytváření a spouštění opakujících se úloh, které zpracovávají souvislá data pomocí posuvných oken v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74786907"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Naplánujte a spusťte úkoly pro souvislé data pomocí posuvné aktivační události okna v Azure Logic Apps

Aby bylo možné pravidelně spouštět úlohy, procesy nebo úlohy, které musí zpracovávat data v souvislých blocích, můžete spustit pracovní postup aplikace logiky pomocí **posuvné** aktivační události okna. Můžete nastavit datum a čas a také časové pásmo pro spuštění pracovního postupu a opakování pro opakování tohoto pracovního postupu. Pokud z jakéhokoli důvodu neexistují opakování, Tato aktivační událost zpracuje tyto zmeškané opakování. Například při synchronizaci dat mezi databází a úložištěm zálohování použijte aktivační událost posuvných oken tak, aby se data synchronizovaná bez mezer. Další informace o integrovaných triggerech a akcích plánování najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh, úkolů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Tady je několik vzorů, které tato aktivační událost podporuje:

* Spusťte okamžitě a zopakujte každých *n* sekund, minut nebo hodin.

* Spusťte v konkrétní datum a čas a potom spusťte a opakujte každých *n* sekund, minut nebo hodin. Pomocí této aktivační události můžete zadat počáteční čas v minulosti, který bude spouštět všechna minulá opakování.

* Před spuštěním odložit každé opakování na určitou dobu.

Rozdíly mezi touto triggerem a triggerem opakování nebo další informace o plánování opakujících se pracovních postupů najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pokud chcete aktivovat aplikaci logiky a spustit ji jenom jednou v budoucnu, Projděte si téma [Spustit úlohy jenom jednou](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [Logic Apps](../logic-apps/logic-apps-overview.md) Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Přidat aktivační událost posuvných oken

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po zobrazení návrháře aplikace logiky zadejte do vyhledávacího pole "posuvné okno" jako filtr. V seznamu triggery vyberte tuto aktivační událost jako první krok v pracovním postupu aplikace logiky: **posuvné okno** .

   ![Vyberte aktivační událost posuvných oken.](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte tyto vlastnosti tak, aby každý týden spouštěly pracovní postupy.

   ![Nastavení intervalu a frekvence](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Vlastnost | Požaduje se | Název JSON | Typ | Popis |
   |----------|----------|-----------|------|-------------|
   | **Doba** | Ano | interval | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Tady jsou minimální a maximální intervaly: <p>-Hodina: 1 – 12000 hodin </br>-Minute: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "hour", opakování je každých 6 hodin. |
   | **Frekvence** | Ano | frequency | Řetězec | Jednotka času pro opakování: **sekunda**, **minuta**nebo **hodina** |
   ||||||

   ![Rozšířené možnosti opakování](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Pro další možnosti opakování otevřete seznam **Přidat nový parametr** . 
   Všechny možnosti, které vyberete, se zobrazí na triggeru po výběru.

   | Vlastnost | Požaduje se | Název JSON | Typ | Popis |
   |----------|----------|-----------|------|-------------|
   | **Zpoždění** | Ne | způsobené | Řetězec | Doba, po kterou se má zpozdit každé opakování, pomocí [specifikace data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Časové pásmo** | Ne | timeZone | Řetězec | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | Ne | startTime | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p>RRRR-MM-DDThh: mm: SS Pokud vyberete časové pásmo <p>-nebo- <p>RRRR-MM-DDThh: mm: ssZ, pokud nevyberete časové pásmo <p>Pokud například požadujete 18. září 2017 na 2:00 odp., zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, například Tichomoří (běžný čas). Případně zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas musí následovat po [specifikaci data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data a času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, je nutné na konci přidat písmeno "Z" bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>V případě jednoduchých plánů je počáteční čas prvním výskytem, zatímco pro pokročilé opakování se Trigger neaktivuje dříve, než je čas spuštění. [*Jaké jsou způsoby, jak můžu použít počáteční datum a čas?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Nyní Sestavte zbývající pracovní postup s dalšími akcemi. Další akce, které můžete přidat, najdete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definice pracovního postupu – posuvné okno

V základní definici pracovního postupu aplikace logiky, která používá formát JSON, můžete zobrazit poposuvnou definici aktivační události okna s možnostmi, které jste zvolili. Chcete-li zobrazit tuto definici, na panelu nástrojů návrháře vyberte možnost **zobrazení kódu**. Chcete-li se vrátit do návrháře, vyberte na panelu nástrojů **Návrháře Návrhář.**

Tento příklad ukazuje, jak může definice posuvných oken vypadat v základní definici pracovního postupu, kde zpoždění pro každé opakování je pět sekund po hodinové opakování:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Další kroky

* [Zpoždění další akce v pracovních postupech](../connectors/connectors-native-delay.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)