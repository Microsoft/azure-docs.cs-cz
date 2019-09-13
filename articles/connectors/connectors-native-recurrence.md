---
title: Plánování opakujících se úloh s triggerem opakování – Azure Logic Apps
description: Naplánování a spouštění opakujících se automatizovaných úloh a pracovních postupů s triggerem opakování v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0bd7262daf23f205552e46bc3ca2802cf35f85db
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914459"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Vytváření, plánování a spouštění opakujících se úloh a pracovních postupů s triggerem opakování v Azure Logic Apps

Chcete-li pravidelně spouštět úlohy, procesy nebo úlohy podle konkrétního plánu, můžete spustit pracovní postup aplikace logiky s integrovanou aktivační procedurou **opakování – plán** . Můžete nastavit datum a čas a také časové pásmo pro spuštění pracovního postupu a opakování pro opakování tohoto pracovního postupu. Pokud z nějakého důvodu neexistují opakování, Tato aktivační událost pokračuje v dalším naplánovaném intervalu. Další informace o integrovaných triggerech a akcích plánování najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh, úkolů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

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

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [Logic Apps](../logic-apps/logic-apps-overview.md) Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Přidat Trigger opakování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po zobrazení návrháře aplikace logiky zadejte do vyhledávacího pole "opakování" jako filtr. V seznamu triggery vyberte jako první krok v pracovním postupu aplikace logiky tuto aktivační událost: **Opakování**

   ![Vybrat aktivační událost opakování](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Nastavte interval a frekvenci opakování. V tomto příkladu nastavte tyto vlastnosti tak, aby každý týden spouštěly pracovní postupy.

   ![Nastavení intervalu a frekvence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Vlastnost | Požadováno | Název JSON | type | Popis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Ano | interval | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Tady jsou minimální a maximální intervaly: <p>Měsíčně 1-16 měsíců </br>Dnu 1-500 dní </br>Hodiny 1 – 12000 hodiny </br>Za 1 – 72000 minut </br>První 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month" (měsíc), opakování je každých 6 měsíců. |
   | **Frekvence** | Ano | frequency | Řetězec | Jednotka času pro opakování: **Sekundy**, **minuty**, **hodiny**, **den**, **týden**nebo **měsíc** |
   ||||||

   Pro další možnosti plánování otevřete seznam **Přidat nový parametr** . 
   Všechny možnosti, které vyberete, se zobrazí na triggeru po výběru.

   ![Pokročilé možnosti plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Vlastnost | Požadováno | Název JSON | type | Popis |
   |----------|----------|-----------|------|-------------|
   | **Časové pásmo** | Ne | timeZone | Řetězec | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | Ne | startTime | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p>RRRR-MM-DDThh: mm: SS Pokud vyberete časové pásmo <p>-nebo- <p>RRRR-MM-DDThh: mm: ssZ, pokud nevyberete časové pásmo <p>Pokud například požadujete 18. září 2017 na 2:00 odp., zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, například Tichomoří (běžný čas). Případně zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas má v budoucnosti maximálně 49 let a musí následovat za [specifikací data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)a času UTC, ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, je nutné na konci přidat písmeno "Z" bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>V případě jednoduchých plánů je počáteční čas prvním výskytem, ale u složitých plánů se Trigger neaktivuje dříve, než je čas spuštění. [*Jaké jsou způsoby, jak můžu použít počáteční datum a čas?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **V tyto dny** | Ne | weekDays | Řetězec nebo pole řetězců | Pokud vyberete týden, můžete vybrat jeden nebo více dní, kdy chcete pracovní postup spustit: **Pondělí**, **úterý**, **Středa**, **čtvrtek**, **pátek**, **sobota**a **neděle** |
   | **V těchto hodinách** | Ne | hod | Celočíselné nebo celočíselné pole | Pokud vyberete "Day" nebo "Week", můžete vybrat jedno nebo více celých čísel od 0 do 23 jako hodiny dne, kdy chcete pracovní postup spustit. <p><p>Pokud například zadáte "10", "12" a "14", dostanete v hodinách dne 10 DOP., 12 hodin a 2 ODP. v závislosti na dni se ale vypočítají minuty dne v závislosti na spuštění opakování. Chcete-li nastavit minuty dne, zadejte hodnotu vlastnosti pro tento počet **minut** . |
   | **V těchto minutách** | Ne | minutes | Celočíselné nebo celočíselné pole | Pokud vyberete "Day" nebo "Week", můžete vybrat jedno nebo více celých čísel od 0 do 59 jako minuty hodiny, kdy chcete pracovní postup spustit. <p>Například můžete zadat "30" jako znak minuty a použít předchozí příklad pro hodiny dne, získáte 10:30 dop. 12:30 ODP. a 2:30 PM. |
   |||||

   Předpokládejme například, že dnešní den je pondělí, 4. září 2017. Následující Trigger opakování se neaktivuje *dříve* než počáteční datum a čas, což je pondělí, 18. září 2017 na 8:00 AM PST. Plán opakování se ale nastaví na 10:30 dop. 12:30 ODP. a 2:30 PM jenom v pondělí. Takže když se Trigger spustí poprvé a vytvoří instanci pracovního postupu aplikace logiky, je k dispozici 10:30. Další informace o tom, jak časy spuštění fungují, najdete v těchto [příkladech počátečního času](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   K budoucím běhům dochází v 12:30 PM a 2:30 odpoledne na stejný den. Každé opakování vytvoří svou vlastní instanci pracovního postupu. Potom se celý plán opakuje znovu v příštím pondělí. [*Jaké jsou některé další příklady výskytů?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Příklad pokročilého plánování](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

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
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Další postup

* [Pozastavit pracovní postupy s akcemi zpoždění](../connectors/connectors-native-delay.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)
