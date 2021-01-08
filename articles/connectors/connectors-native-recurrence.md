---
title: Plánování opakujících se úloh a pracovních postupů
description: Naplánování a spouštění opakujících se automatizovaných úloh a pracovních postupů s triggerem opakování v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 9565ad1efc5ae3dc03b94c78ce8ce52e8dd48c65
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019189"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Vytváření, plánování a spouštění opakujících se úloh a pracovních postupů s triggerem opakování v Azure Logic Apps

Aby bylo možné pravidelně spouštět úkoly, procesy nebo úlohy podle konkrétního plánu, můžete spustit pracovní postup aplikace logiky s integrovaným triggerem **opakování** , který se nativně spustí v Azure Logic Apps. Můžete nastavit datum a čas a také časové pásmo pro spuštění pracovního postupu a opakování pro opakování tohoto pracovního postupu. Pokud se aktivační událost z jakéhokoli důvodu opakuje, například kvůli přerušení nebo zakázaným pracovním postupům, Tato aktivační událost nezpracovává zmeškané opakování, ale restartuje opakování v dalším naplánovaném intervalu. Další informace o integrovaných triggerech a akcích plánování najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh, úkolů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Tady je několik vzorů, které tato aktivační událost podporuje spolu s pokročilejšími opakováními a složitými plány:

* Spusťte okamžitě a opakujte každých *n* sekund, minut, hodin, dnů, týdnů nebo měsíců.

* Spusťte v konkrétní datum a čas a potom spusťte a opakujte každý *n* počet sekund, minut, hodin, dnů, týdnů nebo měsíců.

* Každý den se spouští a opakuje každý den v jednom nebo více časech, například v 8:00 dop. a 5:00 odp.

* Spusťte a opakujte každý týden, ale pouze pro konkrétní dny, například sobotu a neděle.

* Spusťte a opakujte každý týden, ale pouze za konkrétní dny a časy, například pondělí až pátek v 8:00 a 5:00 odp.

Rozdíly mezi touto triggerem a aktivační událostí posuvných oken nebo další informace o plánování opakovaných pracovních postupů najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pokud chcete aktivovat aplikaci logiky a spustit ji jenom jednou v budoucnu, Projděte si téma [Spustit úlohy jenom jednou](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [Logic Apps](../logic-apps/logic-apps-overview.md) Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Přidat Trigger opakování

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po zobrazení návrháře aplikace logiky zadejte do vyhledávacího pole `recurrence` jako filtr. V seznamu triggerů vyberte tuto aktivační událost jako první krok v pracovním postupu aplikace logiky: **opakování**

   ![Vybrat aktivační událost opakování](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte tyto vlastnosti tak, aby každý týden spouštěly pracovní postupy.

   ![Nastavení intervalu a frekvence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | **Interval** | `interval` | Ano | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Tady jsou minimální a maximální intervaly: <p>-Month: 1-16 měsíců <br>-Week: 1-71 týdnů <br>Denní: 1-500 dní <br>-Hodina: 1 – 12000 hodin <br>-Minute: 1 – 72000 minut <br>-Sekunda: 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month" (měsíc), opakování je každých 6 měsíců. |
   | **Frekvence** | `frequency` | Ano | Řetězec | Jednotka času pro opakování: **sekunda**, **minuta**, **hodina**, **den**, **týden** nebo **měsíc** |
   ||||||

   > [!IMPORTANT]
   > Pokud opakování nespecifikují pokročilé možnosti plánování, budoucí opakování vycházejí z času posledního spuštění.
   > Časy zahájení těchto opakování se můžou zpomalit kvůli faktorům, jako je latence během volání úložiště. Abyste se ujistili, že vaše aplikace logiky nezpůsobí opakování, zejména pokud je frekvence ve dnech nebo delší, použijte jednu z těchto možností:
   > 
   > * Zadejte čas zahájení opakování.
   > 
   > * Zadejte hodiny a minuty pro spuštění opakování pomocí vlastností s názvem **v těchto hodinách** a **v těchto minutách**.
   > 
   > * Použijte [aktivační událost posuvných oken](../connectors/connectors-native-sliding-window.md)místo triggeru opakování.

1. Chcete-li nastavit pokročilé možnosti plánování, otevřete seznam **Přidat nový parametr** . Všechny možnosti, které vyberete, se zobrazí na triggeru po výběru.

   ![Pokročilé možnosti plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | **Časové pásmo** | `timeZone` | No | Řetězec | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | `startTime` | No | Řetězec | Zadejte počáteční datum a čas, který má maximálně 49 let v budoucnosti a musí následovat za [specifikací data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)a času UTC, ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset): <p><p>RRRR-MM-DDThh: mm: SS Pokud vyberete časové pásmo <p>-nebo- <p>RRRR-MM-DDThh: mm: ssZ, pokud nevyberete časové pásmo <p>Pokud například požadujete 18. září 2020 na 2:00 odp., zadejte "2020-09-18T14:00:00" a vyberte časové pásmo, například Tichomoří (běžný čas). Případně zadejte "2020-09-18T14:00:00Z" bez časového pásma. <p><p>**Důležité informace:** Pokud nevyberete časové pásmo, je nutné na konci přidat písmeno "Z" bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). Pokud vyberete hodnotu časového pásma, nemusíte na konec hodnoty **času zahájení** přidat "Z". V takovém případě Logic Apps ignoruje hodnotu časového pásma, protože "Z" označuje formát času UTC. <p><p>V případě jednoduchých plánů je počáteční čas prvním výskytem, ale u složitých plánů se Trigger neaktivuje dříve, než je čas spuštění. [*Jaké jsou způsoby, jak můžu použít počáteční datum a čas?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **V tyto dny** | `weekDays` | No | Řetězec nebo pole řetězců | Pokud vyberete týden, můžete vybrat jeden nebo více dní, kdy chcete pracovní postup spustit: **pondělí**, **úterý**, **Středa**, **čtvrtek**, **pátek**, **sobota** a **neděle** . |
   | **V těchto hodinách** | `hours` | No | Celočíselné nebo celočíselné pole | Pokud vyberete "Day" nebo "Week", můžete vybrat jedno nebo více celých čísel od 0 do 23 jako hodiny dne, kdy chcete pracovní postup spustit. <p><p>Pokud například zadáte "10", "12" a "14", dostanete v hodinách dne 10 DOP., 12 hodin a 2 ODP. v závislosti na dni se ale vypočítají minuty dne v závislosti na spuštění opakování. Chcete-li nastavit konkrétní minuty dne, například 10:00 dop. 12:00 odp. a 2:00 PM, zadejte tyto hodnoty pomocí vlastnosti s názvem **v těchto minutách**. |
   | **V těchto minutách** | `minutes` | No | Celočíselné nebo celočíselné pole | Pokud vyberete "Day" nebo "Week", můžete vybrat jedno nebo více celých čísel od 0 do 59 jako minuty hodiny, kdy chcete pracovní postup spustit. <p>Například můžete zadat "30" jako znak minuty a použít předchozí příklad pro hodiny dne, získáte 10:30 dop. 12:30 ODP. a 2:30 PM. <p>**Poznámka**: časové razítko spouštěného běhu se někdy může v naplánovaném čase lišit až o 1 minutu. Pokud potřebujete předat časové razítko přesně tak, jak je naplánováno na následné akce, můžete pomocí výrazů šablony změnit časové razítko odpovídajícím způsobem. Další informace najdete v tématu [funkce pro datum a čas pro výrazy](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Předpokládejme například, že dnes je pátek, 4. září 2020. Následující Trigger opakování se neaktivuje *dříve* než počáteční datum a čas, který je pátek, 18. září 2020 na 8:00 AM PST. Plán opakování se ale nastaví na 10:30 dop. 12:30 ODP. a 2:30 PM jenom v pondělí. Takže když se Trigger poprvé spustí a vytvoří instanci pracovního postupu aplikace logiky, bude v pondělí v 10:30. Další informace o tom, jak časy spuštění fungují, najdete v těchto [příkladech počátečního času](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   K budoucím běhům dochází v 12:30 PM a 2:30 odpoledne na stejný den. Každé opakování vytvoří svou vlastní instanci pracovního postupu. Potom se celý plán opakuje znovu v příštím pondělí. [*Jaké jsou některé další příklady výskytů?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Příklad pokročilého plánování](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > Aktivační událost zobrazí náhled pro zadané opakování pouze v případě, že jako frekvenci vyberete "Day" nebo "Week".

1. Nyní Sestavte zbývající pracovní postup s dalšími akcemi. Další akce, které můžete přidat, najdete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definice pracovního postupu – opakování

V základní definici pracovního postupu aplikace logiky, která používá JSON, můžete zobrazit [definici triggeru opakování](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) s možnostmi, které jste zvolili. Chcete-li zobrazit tuto definici, na panelu nástrojů návrháře vyberte možnost **zobrazení kódu**. Chcete-li se vrátit do návrháře, vyberte na panelu nástrojů **Návrháře Návrhář.**

Tento příklad ukazuje, jak může definice triggeru opakování vypadat v základní definici pracovního postupu:

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
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Opětovné spuštění klávesy SHIFT mezi letním a standardním časem

Opakující se předdefinované aktivační události dodržují plán, který jste nastavili, včetně jakéhokoli časového pásma, které zadáte. Pokud nevyberete časové pásmo, může letní čas (letní čas) ovlivnit dobu, kdy se triggery spouštějí, například posune čas zahájení o hodinu dopředu při zahájení LETNÍho času a jednu hodinu zpět v případě, kdy končí letní čas.

Aby se zabránilo tomuto posunu, aby vaše aplikace logiky běžela v zadaném čase začátku, ujistěte se, že jste vybrali časové pásmo. V takovém případě se čas UTC pro vaši aplikaci logiky posune také k čítači změny sezónních časů. Nicméně některá časová období můžou způsobit problémy, když se posunou čas. Další informace a příklady naleznete v tématu [opakování pro letní čas a běžný čas](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Další kroky

* [Pozastavit pracovní postupy s akcemi zpoždění](../connectors/connectors-native-delay.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)
