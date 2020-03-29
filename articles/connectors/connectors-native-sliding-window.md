---
title: Naplánování úloh pro zpracování souvislých dat
description: Vytváření a spouštění opakovaných úloh, které zpracovávají souvislá data pomocí posuvných oken v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786907"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Plánování a spouštění úloh pro souvislá data pomocí aktivační události posuvného okna v aplikacích Azure Logic Apps

Chcete-li pravidelně spouštět úlohy, procesy nebo úlohy, které musí zpracovávat data v souvislých blocích, můžete spustit pracovní postup aplikace logiky pomocí aktivační události **posuvné okno.** Můžete nastavit datum a čas a také časové pásmo pro spuštění pracovního postupu a opakování tohoto pracovního postupu. Pokud opakování jsou vynechány z jakéhokoli důvodu, tato aktivační událost zpracuje ty zmeškané opakování. Například při synchronizaci dat mezi databází a úložištěm záloh použijte aktivační událost posuvné okno tak, aby se data synchronizovala bez vzniku mezer. Další informace o předdefinovaných aktivačních událostech a akcích plánu najdete v [tématu Plánování a spouštění opakovaných automatizovaných úloh, úloh a pracovních postupů pomocí aplikací Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Zde jsou některé vzory, které tato aktivační událost podporuje:

* Spusťte okamžitě a opakujte *každých n* minut, minut nebo hodin.

* Začněte v určitém datu a čase, pak spusťte a opakujte *každých n* minut, minut nebo hodin. Pomocí této aktivační události můžete určit čas zahájení v minulosti, který spouští všechny minulé opakování.

* Před spuštěním zpozdíte každé opakování o určitou dobu.

Rozdíly mezi touto aktivační událostí a aktivační událostí opakování nebo další informace o plánování opakovaných pracovních postupů najdete v [tématu Plánování a spouštění opakovaných automatizovaných úloh, procesů a pracovních postupů pomocí aplikací Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pokud chcete spustit aplikaci logiky a spustit pouze jednou v budoucnu, přečtěte si, viz [Spustit úlohy pouze jednou](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [aplikacích logiky](../logic-apps/logic-apps-overview.md). Pokud s aplikacemi logiky teprve začínáte, přečtěte si, [jak vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Přidat aktivační událost posuvného okna

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po návrháři aplikace logiky se do vyhledávacího pole zobrazí jako filtr "posuvné okno". Ze seznamu aktivačních událostí vyberte tuto aktivační událost jako první krok v pracovním postupu aplikace logiky: **Posuvné okno**

   ![Vyberte aktivační událost Posuvné okno.](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte tyto vlastnosti tak, aby spouštěli pracovní postup každý týden.

   ![Nastavit interval a frekvenci](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Vlastnost | Požaduje se | Název JSON | Typ | Popis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Ano | interval | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Zde jsou minimální a maximální intervaly: <p>- Hodina: 1-12 000 hodin </br>- Minuta: 1-72 000 minut </br>- Druhý: 1-9,999,999 sekund<p>Pokud je například interval 6 a frekvence je "Hodina", opakování je každých 6 hodin. |
   | **Frequency** | Ano | frequency | Řetězec | Jednotka času pro opakování: **Sekunda**, **Minuta**nebo **Hodina** |
   ||||||

   ![Rozšířené možnosti opakování](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Další možnosti opakování zobrazíte v seznamu **Přidat nový parametr.** 
   Všechny vybrané možnosti se zobrazí na aktivační události po výběru.

   | Vlastnost | Požaduje se | Název JSON | Typ | Popis |
   |----------|----------|-----------|------|-------------|
   | **Zpoždění** | Ne | Zpoždění | Řetězec | Doba trvání zpoždění každého opakování pomocí [specifikace data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Časové pásmo** | Ne | timeZone | Řetězec | Použije se pouze v případě, že zadáte čas zahájení, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | Ne | startTime | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p>YYYY-MM-DDThh:mm:ss, pokud vyberete časové pásmo <p>-nebo- <p>YYYY-MM-DDThh:mm:ssZ, pokud nevyberete časové pásmo <p>Pokud tedy chcete například 18. Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas musí následovat [podle specifikace data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data uTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, musíte přidat písmeno "Z" na konci bez mezer. Toto "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány je počáteční čas prvním výskytem, zatímco u pokročilých opakování se aktivační událost nespustí dříve než čas zahájení. [*Jakými způsoby lze použít počáteční datum a čas?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Nyní vytvořte zbývající pracovní postup s dalšími akcemi. Další akce, které můžete přidat, najdete [v tématu konektory pro Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definice pracovního postupu – posuvné okno

V základní definici pracovního postupu aplikace logiky, která používá JSON, můžete zobrazit definici aktivační události posuvného okna s možnostmi, které jste zvolili. Chcete-li tuto definici zobrazit, zvolte na panelu nástrojů **návrháře zobrazení kódu**. Chcete-li se vrátit k návrháři, zvolte na panelu nástrojů **návrháře Návrhář**.

Tento příklad ukazuje, jak může vypadat definice aktivační události posuvného okna v základní definici pracovního postupu, kde je zpoždění pro každé opakování pět sekund pro hodinové opakování:

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