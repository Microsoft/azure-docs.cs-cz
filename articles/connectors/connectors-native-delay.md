---
title: Zpoždění akce dalšího v pracovních postupech – Azure Logic Apps
description: Počkejte provádět další akce v pracovních postupů aplikace logiky pomocí akce zpoždění nebo zpoždění až v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297620"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Zpoždění spuštění další akce v Azure Logic Apps

Pokud chcete, aby vaše aplikace logiky, počkejte dobu před spuštěním další akci, můžete přidat předdefinované **zpoždění – naplánovat** akce před akci v pracovním postupu vaší aplikace logiky. Nebo můžete přidat předdefinované **zpoždění do – naplánovat** akci počkat do určité datum a čas před spuštěním další akci. Další informace o předdefinovaných plán akcí a triggerů, naleznete v tématu [plán a opakované automatizované spuštění, úloh a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **zpoždění**: Čekat zadaný počet časových jednotek, jako je například sekund, minuty, hodiny, dny, týdny nebo měsíce před spuštěním další akce.

* **Zpoždění do**: Počkejte, až do zadaného data a času před spuštěním další akce.

Tady jsou některé způsoby příklad použití těchto akcí:

* Počkejte, dokud jeden den v týdnu pro odeslání e-mailu aktualizace stavu.

* Dokud se nedokončí volání protokolu HTTP před obnovení a načítání dat, zpoždění pracovního postupu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [logic apps](../logic-apps/logic-apps-overview.md). Než použijete akci, třeba nejprve spustit aplikaci logiky s triggerem. Můžete použít libovolný trigger a přidejte další akce předtím, než přidáte akci zpoždění. Toto téma používá aktivační událost Office 365 Outlook. Pokud se službou logic Apps teprve začínáte, přečtěte si [vytvoření vaší první aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Přidání zpoždění akce

1. V návrháři aplikace logiky v kroku, ve které chcete přidat akci zpoždění, zvolte **nový krok**.

   Chcete-li přidat akci zpoždění mezi jednotlivými kroky, přesuňte ukazatel myši na šipku, která se připojuje kroky. Vyberte znaménko plus (+), který se zobrazí a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "zpoždění". Ze seznamu akcí vyberte tuto akci: **zpoždění**

   ![Přidání akce "Zpoždění"](./media/connectors-native-delay/add-delay-action.png)

1. Zadejte dobu čekání před spuštěním další akce.

   ![Nastavte dobu zpoždění](./media/connectors-native-delay/delay-time-intervals.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | Count | count | Ano | Integer | Počet časových jednotek zpoždění |
   | Jednotka | Jednotka | Ano | String | Jednotka času, například: `Second`, `Minute`, `Hour`, `Day`, `Week`, nebo `Month` |
   ||||||

1. Přidejte všechny další akce, které chcete spustit v pracovním postupu.

1. Jakmile budete hotovi, uložte svou aplikaci logiky.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Přidání zpoždění – dokud akce

1. V návrháři aplikace logiky v kroku, ve které chcete přidat akci zpoždění, zvolte **nový krok**.

   Chcete-li přidat akci zpoždění mezi jednotlivými kroky, přesuňte ukazatel myši na šipku, která se připojuje kroky. Vyberte znaménko plus (+), který se zobrazí a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "zpoždění". Ze seznamu akcí vyberte tuto akci: **Zpoždění do**

   ![Přidání akce "Zpoždění do"](./media/connectors-native-delay/add-delay-until-action.png)

1. Zadejte koncové datum a čas pro kdy budete chtít pokračovat v pracovním postupu.

   ![Zadejte časové razítko času ukončení zpoždění](./media/connectors-native-delay/delay-until-timestamp.png)

   | Vlastnost | Název JSON | Požaduje se | Typ | Popis |
   |----------|-----------|----------|------|-------------|
   | Timestamp | časové razítko | Ano | String | Koncové datum a čas pro obnovení pracovního postupu pomocí tohoto formátu: <p>YYYY-MM-DDThh:mm:ssZ <p>Tak například, pokud chcete 18. září 2017 ve 14:00, zadejte "2017-09-18T14:00:00Z". <p>**Poznámka:** Musí dodržovat tento formát času [specifikace formátu ISO 8601 datum čas](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Bez časové pásmo je nutné přidat písmeno "Z" na konci bez mezer. Tato "Z" odkazuje na ekvivalentní [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Přidejte všechny další akce, které chcete spustit v pracovním postupu.

1. Jakmile budete hotovi, uložte svou aplikaci logiky.

## <a name="next-steps"></a>Další postup

* [Vytvářet, plánovat a spouštět opakované úlohy a pracovní postupy pomocí triggeru opakování](../connectors/connectors-native-recurrence.md)
* [Konektory pro Logic Apps](../connectors/apis-list.md)