---
title: Plánování opakovaných úkolů a pracovních postupů
description: Plánování a spouštění opakovaných automatizovaných úloh a pracovních postupů pomocí aktivační události opakování v Aplikacích Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445862"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí aktivační události opakování v Aplikacích Logika Azure

Chcete-li pravidelně spouštět úlohy, procesy nebo úlohy podle konkrétního plánu, můžete spustit pracovní postup aplikace logiky pomocí integrované aktivační události **Opakování – plán.** Můžete nastavit datum a čas a také časové pásmo pro spuštění pracovního postupu a opakování tohoto pracovního postupu. Pokud opakování jsou vynechány z jakéhokoli důvodu, tato aktivační událost pokračuje opakující se v dalším naplánovaném intervalu. Další informace o předdefinovaných aktivačních událostech a akcích plánu najdete v [tématu Plánování a spouštění opakovaných automatizovaných úloh, úloh a pracovních postupů pomocí aplikací Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Zde jsou některé vzory, které tato aktivační událost podporuje spolu s pokročilejšími opakováními a složitými plány:

* Spusťte okamžitě a opakujte *každých n* počet sekund, minut, hodin, dnů, týdnů nebo měsíců.

* Začněte v určitém datu a čase, pak spusťte a opakujte *každých n* minut, minut, hodin, dnů, týdnů nebo měsíců.

* Spouštět a opakovat jednou nebo vícekrát každý den, například v 8:00 a 17:00.

* Spouštět a opakovat každý týden, ale pouze pro určité dny, jako je sobota a neděle.

* Spouštět a opakovat každý týden, ale pouze pro určité dny a časy, například od pondělí do pátku v 8:00 a 17:00.

Rozdíly mezi touto aktivační událostí a aktivační událostí posuvného okna nebo další informace o plánování opakovaných pracovních postupů najdete v [tématu Plánování a spouštění opakovaných automatizovaných úloh, procesů a pracovních postupů pomocí aplikací Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pokud chcete spustit aplikaci logiky a spustit pouze jednou v budoucnu, přečtěte si, viz [Spustit úlohy pouze jednou](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [aplikacích logiky](../logic-apps/logic-apps-overview.md). Pokud s aplikacemi logiky teprve začínáte, přečtěte si, [jak vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Přidat aktivační událost opakování

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po app návrháře logiky se `recurrence` zobrazí ve vyhledávacím poli zadejte jako filtr. Ze seznamu aktivačních událostí vyberte tuto aktivační událost jako první krok v pracovním postupu aplikace logiky: **Opakování**

   ![Vyberte aktivační událost Opakování.](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte tyto vlastnosti tak, aby spouštěli pracovní postup každý týden.

   ![Nastavit interval a frekvenci](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | **Interval** | `interval` | Ano | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Zde jsou minimální a maximální intervaly: <p>- Měsíc: 1-16 měsíců </br>- Den: 1-500 dní </br>- Hodina: 1-12 000 hodin </br>- Minuta: 1-72 000 minut </br>- Druhý: 1-9,999,999 sekund<p>Pokud je například interval 6 a frekvence je "Měsíc", opakování je každých 6 měsíců. |
   | **Frequency** | `frequency` | Ano | Řetězec | Jednotka času pro opakování: **Sekunda**, **Minuta**, **Hodina**, **Den**, **Týden**nebo **Měsíc** |
   ||||||

   > [!IMPORTANT]
   > Pokud opakování neurčují možnosti rozšířeného plánování, jsou budoucí opakování založena na čase posledního spuštění.
   > Počáteční časy pro tyto opakování může posunzou kvůli faktorům, jako je latence během volání úložiště. Chcete-li se ujistit, že vaše aplikace logiky nezmešká opakování, zejména pokud je frekvence ve dnech nebo delších, použijte jednu z těchto možností:
   > 
   > * Zadejte čas zahájení opakování.
   > 
   > * Zadejte hodiny a minuty, kdy chcete spustit opakování pomocí **At these hours** a At these **minutes** vlastnosti.
   > 
   > * Použijte [aktivační událost posuvné okno](../connectors/connectors-native-sliding-window.md), nikoli aktivační událost opakování.

1. Chcete-li nastavit možnosti rozšířeného plánování, otevřete seznam **Přidat nový parametr.** Všechny vybrané možnosti se zobrazí na aktivační události po výběru.

   ![Možnosti rozšířeného plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | **Časové pásmo** | `timeZone` | Ne | Řetězec | Použije se pouze v případě, že zadáte čas zahájení, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | `startTime` | Ne | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p>YYYY-MM-DDThh:mm:ss, pokud vyberete časové pásmo <p>-nebo- <p>YYYY-MM-DDThh:mm:ssZ, pokud nevyberete časové pásmo <p>Pokud tedy chcete například 18. Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento čas zahájení má v budoucnu maximálně 49 let a musí se řídit [specifikací data iso 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, musíte přidat písmeno "Z" na konci bez mezer. Toto "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány je počáteční čas prvním výskytem, zatímco pro složité plány se aktivační událost nespustí dříve než čas zahájení. [*Jakými způsoby lze použít počáteční datum a čas?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **V tyto dny** | `weekDays` | Ne | Pole řetězce nebo řetězce | Pokud vyberete možnost Týden, můžete vybrat jeden nebo více dní, kdy chcete pracovní postup spustit: **pondělí**, **úterý**, **středa**, **čtvrtek**, **pátek**, **sobota**a **neděle.** |
   | **V těchto hodinách** | `hours` | Ne | Celé číslo nebo celé pole | Pokud vyberete "Den" nebo "Týden", můžete vybrat jedno nebo více celá čísla od 0 do 23 jako hodiny dne, kdy chcete spustit pracovní postup. <p><p>Například pokud zadáte "10", "12" a "14", dostanete 10:00, 12:00 a 14:00 pro hodiny dne, ale minuty dne jsou vypočteny na základě zahájení opakování. Chcete-li nastavit minuty dne, zadejte hodnotu **vlastnosti Na tyto minuty.** |
   | **V těchto minutách** | `minutes` | Ne | Celé číslo nebo celé pole | Pokud vyberete "Den" nebo "Týden", můžete vybrat jedno nebo více celá čísla od 0 do 59 jako minuty hodiny, kdy chcete spustit pracovní postup. <p>Můžete například zadat "30" jako značku minuty a pomocí předchozího příkladu pro hodiny dne získáte 10:30, 12:30 a 14:30. |
   |||||

   Předpokládejme například, že dnes je pondělí, září 4, 2017. Následující aktivační událost opakování se nespustí *dříve* než počáteční datum a čas, což je pondělí, září 18, 2017 v 8:00 PST. Plán opakování je však nastaven pouze na 10:30, 12:30 a 14:30 pouze v pondělí. Takže první spuštění aktivační události a vytvoří instanci pracovního postupu aplikace logiky je v 10:30. Další informace o tom, jak fungují časy zahájení, naleznete v těchto [příkladech času zahájení](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Budoucí běhy se dějí ve 12:30 a 14:30 ve stejný den. Každé opakování vytvoří vlastní instanci pracovního postupu. Po tom, celý rozvrh se opakuje znovu příští pondělí. [*Jaké jsou další příklady výskytů?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Příklad rozšířeného plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Aktivační událost zobrazuje náhled zadaného opakování pouze v případě, že jako frekvenci vyberete "Den" nebo "Týden".

1. Nyní vytvořte zbývající pracovní postup s dalšími akcemi. Další akce, které můžete přidat, najdete [v tématu konektory pro Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definice pracovního postupu – opakování

V základní definici pracovního postupu aplikace logiky, která používá JSON, můžete zobrazit [definici aktivační události opakování](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) s možnostmi, které jste zvolili. Chcete-li tuto definici zobrazit, zvolte na panelu nástrojů **návrháře zobrazení kódu**. Chcete-li se vrátit k návrháři, zvolte na panelu nástrojů **návrháře Návrhář**.

Tento příklad ukazuje, jak může definice aktivační události opakování vypadat v základní definici pracovního postupu:

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

## <a name="next-steps"></a>Další kroky

* [Pozastavení pracovních postupů pomocí akcí zpoždění](../connectors/connectors-native-delay.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)
