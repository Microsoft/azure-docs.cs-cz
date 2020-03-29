---
title: Zpoždění další akce v pracovních postupech
description: Počkejte na spuštění další akce v pracovních postupech aplikace logiky pomocí akce Zpoždění nebo Zpoždění do Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787332"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Zpoždění spuštění další akce v Azure Logic Apps

Chcete-li, aby vaše aplikace logiky čekala určitou dobu před spuštěním další akce, můžete přidat předdefinovanou akci **Zpoždění – naplánovat** akci před akci v pracovním postupu aplikace logiky. Nebo můžete přidat předdefinované **Zpoždění do - Naplánovat** akci čekat na určité datum a čas před spuštěním další akce. Další informace o předdefinovaných akcích a aktivačních událostech plánu najdete v [tématu Plánování a spouštění opakovaných automatizovaných úloh, úloh a pracovních postupů pomocí aplikací Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Zpoždění**: Před spuštěním další akce počkejte na zadaný počet časových jednotek, například sekund, minut, hodin, dnů, týdnů nebo měsíců.

* **Zpoždění do**: Počkejte na zadané datum a čas, než bude spuštěna další akce.

Zde je několik příkladů použití těchto akcí:

* Počkejte až do pracovního dne, abyste odeslali aktualizaci stavu prostředpo e-mailem.

* Zpozdit pracovní postup, dokud nebude ukončeno volání HTTP, než se znovu načte a nenačte data.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [aplikacích logiky](../logic-apps/logic-apps-overview.md). Před použitím akce, aplikace logiky musí nejprve začít s aktivační událostí. Před přidáním akce zpoždění můžete použít libovolnou aktivační událost a přidat další akce. Toto téma používá aktivační událost Outlooku Office 365. Pokud s aplikacemi logiky teprve začínáte, přečtěte si, [jak vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Přidat akci Zpoždění

1. V Návrháři aplikace logiky v kroku, kam chcete přidat akci zpoždění, zvolte **Nový krok**.

   Chcete-li přidat akci zpoždění mezi kroky, přesuňte ukazatel myši na šipku, která kroky spojuje. Zvolte znaménko plus (+), které se zobrazí, a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "delay". Ze seznamu akcí vyberte tuto akci: **Zpoždění**

   ![Přidat akci Zpoždění](./media/connectors-native-delay/add-delay-action.png)

1. Zadejte dobu čekání, než se spustí další akce.

   ![Nastavit dobu zpoždění](./media/connectors-native-delay/delay-time-intervals.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | Počet | count | Ano | Integer | Počet časových jednotek, které mají být zpožděny |
   | Jednotka | unit | Ano | Řetězec | Jednotka času, například: `Second` `Minute`, `Hour` `Day`, `Week`, , , nebo`Month` |
   ||||||

1. Přidejte další akce, které chcete spustit v pracovním postupu.

1. Až budete hotovi, uložte aplikaci logiky.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Přidat akci Zpoždění-do

1. V Návrháři aplikace logiky v kroku, kam chcete přidat akci zpoždění, zvolte **Nový krok**.

   Chcete-li přidat akci zpoždění mezi kroky, přesuňte ukazatel myši na šipku, která kroky spojuje. Zvolte znaménko plus (+), které se zobrazí, a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "delay". Ze seznamu akcí vyberte tuto akci: **Zpoždění do**

   ![Přidat akci "Zdržet do"](./media/connectors-native-delay/add-delay-until-action.png)

1. Zadejte koncové datum a čas, kdy chcete pokračovat v pracovním postupu.

   ![Určit časové razítko pro ukončení zpoždění](./media/connectors-native-delay/delay-until-timestamp.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | Časové razítko | časové razítko | Ano | Řetězec | Koncové datum a čas pro obnovení pracovního postupu v tomto formátu: <p>YYYY-MM-DDThh:mm:ssZ <p>Pokud tedy chcete například 18. <p>**Poznámka:** Tento formát času musí dodržovat [specifikaci data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu času data UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Bez časového pásma je nutné přidat písmeno "Z" na konci bez mezer. Toto "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Přidejte další akce, které chcete spustit v pracovním postupu.

1. Až budete hotovi, uložte aplikaci logiky.

## <a name="next-steps"></a>Další kroky

* [Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí aktivační události Opakování](../connectors/connectors-native-recurrence.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)