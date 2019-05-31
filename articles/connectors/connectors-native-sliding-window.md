---
title: Naplánovat opakované úkoly s posuvné okno aktivační události – Azure Logic Apps
description: Naplánování a spuštění opakované automatizované úlohy a pracovní postupy s triggerem posuvné okno v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299499"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Vytvářet, plánovat a spouštět opakované úlohy a pracovní postupy s triggerem posuvné okno v Azure Logic Apps

Pravidelně spouštět úlohy, procesy nebo úloh, které se musí zpracovat data v průběžné bloky dat, můžete spustit pracovního postupu aplikace logiky s **posuvné okno - plán** aktivační události. Můžete nastavit datum a čas, jakož i časové pásmo pro spuštění pracovního postupu a počet opakování pro opakující se tento pracovní postup. Pokud jsou z jakéhokoli důvodu opakování, Tato aktivační událost zpracovává tyto chybějící opakování. Například při synchronizaci dat mezi databází a úložiště pro zálohování, použití aktivační procedury posuvné okno tak, aby zařízení synchronizovat data bez dalších nákladů na mezery. Další informace o předdefinovaných aktivačních událostech plánovače a akcích najdete v části [plán a opakované automatizované spuštění, úloh a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Tady jsou některé vzory, které tato aktivační událost podporuje:

* Okamžité spuštění a opakovat každých *n* počet sekund, minut nebo hodin.

* Spusťte v určité datum a čas, pak spusťte a opakovat každých *n* počet sekund, minut nebo hodin. S tímto triggerem můžete zadat počáteční čas v minulosti, která spustí všechny minulé opakování.

* Zpoždění opakování pro určitou dobu před spuštěním.

Rozdíly mezi této aktivační události a aktivační události opakování nebo Další informace o plánování opakované pracovní postupy, přečtěte si téma [plán a opakované spouštění automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pokud chcete aktivovat svou aplikaci logiky a spustit pouze jednou v budoucích, viz [spuštění úlohy jednou](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [logic apps](../logic-apps/logic-apps-overview.md). Pokud se službou logic Apps teprve začínáte, přečtěte si [vytvoření vaší první aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Přidání triggeru posuvné okno

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Poté, co se zobrazí návrhář aplikace logiky, do vyhledávacího pole zadejte jako filtr "posuvné okno". Ze seznamu triggerů vyberte tento trigger jako první krok do pracovního postupu aplikace logiky: **Posuvné okno**

   ![Vyberte trigger "Posuvné okno"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte vlastnosti tak, aby každý týden spuštění pracovního postupu.

   ![Nastavit interval a frekvence](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Vlastnost | Požaduje se | Název JSON | Type | Popis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Ano | interval | Integer | Kladné celé číslo, který popisuje, jak často pracovní postup spouští na základě frekvence. Toto jsou minimální a maximální intervaly: <p>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud 6 je interval a frekvenci je "Hodina", pak opakování je každých 6 hodin. |
   | **Frekvence** | Ano | frequency | String | Jednotka času pro opakování: **Druhý**, **minutu**, nebo **hodinu** |
   ||||||

   ![Rozšířené možnosti opakování](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Další možnosti opakování, otevřete **přidat nový parametr** seznamu. 
   Všechny možnosti, které vyberete, zobrazí na aktivační událost po výběru.

   | Vlastnost | Požaduje se | Název JSON | Type | Popis |
   |----------|----------|-----------|------|-------------|
   | **zpoždění** | Ne | delay | String | Doba trvání zpoždění pomocí každého opakování [specifikace formátu ISO 8601 datum čas](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Časové pásmo** | Ne | timeZone | String | Platí, pouze pokud zadáte čas spuštění protože nepřijme tento trigger [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | Ne | startTime | String | Zadejte počáteční datum a čas v tomto formátu: <p>RRRR-MM-ddTHH Pokud vyberte časové pásmo <p>-nebo- <p>RRRR-MM-: ssZ Pokud nevyberete časové pásmo <p>Tak například, pokud chcete 18. září 2017 ve 14:00, zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, jako je například Tichomoří (běžný čas). Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas musí následovat [specifikace formátu ISO 8601 datum čas](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, je nutné přidat písmeno "Z" na konci bez mezer. Tato "Z" odkazuje na ekvivalentní [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>U jednoduchých plánů, počáteční čas je první výskyt, zatímco pro pokročilé opakování triggeru neaktivuje všechny dříve než čas spuštění. [*Jaké jsou způsoby, které lze použít počáteční datum a čas?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Nyní vytvoření zbývající pracovního postupu pomocí dalších akcí. Další akce, které můžete přidat, naleznete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definice pracovního postupu - posuvné okno

V aplikaci logiky základní definice pracovního postupu, které využívá JSON, můžete zobrazit posuvné okno Definice aktivační události s možnostmi, které jste zvolili. Chcete-li zobrazit tuto definici na panelu nástrojů návrháře, zvolte **zobrazení kódu**. Chcete-li vrátit do návrháře, zvolte na panelu nástrojů návrháře **návrháře**.

Tento příklad ukazuje, jak definice aktivační události posuvné okno může vypadat základní definice pracovního postupu kde zpoždění opakování je pět sekund pro hodinové opakování:

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

## <a name="next-steps"></a>Další postup

* [Zpoždění akce dalšího v pracovních postupech](../connectors/connectors-native-delay.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)