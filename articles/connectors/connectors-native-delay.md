---
title: Zpoždění další akce v pracovních postupech
description: Počkejte na spuštění další akce v pracovních postupech aplikace logiky pomocí zpoždění nebo zpoždění, dokud akce v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74787332"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Zpoždění spuštění další akce v Azure Logic Apps

Chcete-li, aby vaše aplikace logiky čekala dlouhou dobu před spuštěním další akce, můžete přidat předdefinovanou akci **zpožděného plánování** před akcí v pracovním postupu aplikace logiky. Nebo můžete přidat vestavěnou **prodlevu do doby, než** se před spuštěním další akce počká na určité datum a čas. Další informace o předdefinovaných akcích a triggerech plánování najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh, úkolů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Delay**: před spuštěním další akce počkejte na zadaný počet časových jednotek (například sekund, minut, hodin, dnů, týdnů nebo měsíců).

* **Zpoždění do**: před spuštěním další akce počkat na zadané datum a čas.

Tady je několik příkladů, jak použít tyto akce:

* Počkejte, až pracovní den pošle aktualizaci stavu přes e-mail.

* Před pokračováním a obnovením dat odložit pracovní postup do dokončení volání HTTP.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [Logic Apps](../logic-apps/logic-apps-overview.md) Než budete moct použít akci, vaše aplikace logiky musí nejdřív spustit Trigger. Před přidáním akce zpoždění můžete použít libovolný Trigger, který chcete přidat, a další akce. V tomto tématu se používá aktivační procedura Office 365 Outlook. Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Přidat akci zpoždění

1. V návrháři aplikace logiky v kroku, kam chcete přidat akci zpoždění, vyberte možnost **Nový krok**.

   Chcete-li přidat akci zpoždění mezi kroky, přesuňte ukazatel myši na šipku, která propojuje kroky. Zvolte znaménko plus (+), které se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Delay". V seznamu akce vyberte tuto akci: **zpoždění** .

   ![Přidat akci zpoždění](./media/connectors-native-delay/add-delay-action.png)

1. Zadejte dobu, po kterou se má čekat, než se spustí další akce.

   ![Nastavte dobu zpoždění.](./media/connectors-native-delay/delay-time-intervals.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | Počet | count | Ano | Integer | Počet časových jednotek, které se mají zpozdit |
   | Jednotka | unit | Ano | Řetězec | Jednotka času, například: `Second` , `Minute` , `Hour` , `Day` , `Week` nebo `Month` |
   ||||||

1. Přidejte všechny další akce, které chcete v pracovním postupu spustit.

1. Až budete hotovi, uložte aplikaci logiky.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Přidat akci zpoždění až do

1. V návrháři aplikace logiky v kroku, kam chcete přidat akci zpoždění, vyberte možnost **Nový krok**.

   Chcete-li přidat akci zpoždění mezi kroky, přesuňte ukazatel myši na šipku, která propojuje kroky. Zvolte znaménko plus (+), které se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Delay". V seznamu akce vyberte tuto akci: **zpoždění do**

   ![Přidat akci zpoždění do](./media/connectors-native-delay/add-delay-until-action.png)

1. Zadejte koncové datum a čas, kdy chcete pracovní postup obnovit.

   ![Zadejte časové razítko, kdy se má zpoždění ukončit.](./media/connectors-native-delay/delay-until-timestamp.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | Timestamp | časové razítko | Ano | Řetězec | Koncové datum a čas pro obnovení pracovního postupu v tomto formátu: <p>RRRR-MM-DDThh: mm: ssZ <p>Pokud například požadujete 18. září 2017 na 2:00 odp., zadejte "2017-09-18T14:00:00Z". <p>**Poznámka:** Tento formát času musí odpovídat [specifikaci data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data a času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Bez časového pásma je nutné přidat písmeno "Z" na konci bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Přidejte všechny další akce, které chcete v pracovním postupu spustit.

1. Až budete hotovi, uložte aplikaci logiky.

## <a name="next-steps"></a>Další kroky

* [Vytváření, plánování a spouštění opakovaných úloh a pracovních postupů pomocí triggeru opakování](../connectors/connectors-native-recurrence.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)