---
title: Připojení ke koncovým bodům protokol HTTP nebo HTTPS z Azure Logic Apps
description: Monitorování koncových bodů HTTP nebo HTTPS v automatizovaných úloh, procesy a pracovní postupy s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541244"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Volání koncových bodů HTTP nebo HTTPS pomocí Azure Logic Apps

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovaného konektoru HTTP, můžete automatizovat pracovní postupy, které pravidelně volání jakékoli koncového bodu protokolu HTTP nebo HTTPS díky vytváření aplikací logiky. Můžete například monitorovat koncový bod služby pro váš web tak, že zkontrolujete tohoto koncového bodu podle zadaného plánu. Při určité události se odehrává na tohoto koncového bodu, jako je například webu směrem dolů, událost aktivuje pracovní postup aplikace logiky a spustí zadané akce.

Chcete-li zkontrolovat nebo *dotazování* koncový bod v pravidelných intervalech, můžete použít aktivační událost HTTP jako první krok v pracovním postupu. Na každé kontrole, odešle aktivační událost volání nebo *požadavek* ke koncovému bodu. Odpověď koncový bod určuje, jestli se spouští pracovní postup aplikace logiky. Aktivační událost předá jakýkoli obsah z odpovědi na akce ve vaší aplikaci logiky.

Akce HTTP můžete použít jako další krok v pracovním postupu pro volání koncového bodu, pokud chcete. Odpověď koncový bod určuje, jak spustit zbývající akce pracovního postupu.

Na základě koncový bod cílové funkce, které se konektor HTTP podporuje zabezpečení TLS (Transport Layer) verze 1.0, 1.1 a 1.2. Logic Apps vyjedná s koncovým bodem přes pomocí nejvyšší podporovaná verze je to možné. Ano například pokud koncový bod podporuje 1.2, konektor používá 1.2 nejprve. V opačném případě konektor používá následující nejvyšší podporovaná verze.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL cílového koncového bodu, který chcete volat

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ze kterého má volat cílový koncový bod. Spustit s triggerem HTTP [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Použití akce HTTP, začněte s libovolný trigger, který chcete svou aplikaci logiky. Tento příklad používá jako první krok triggeru HTTP.

## <a name="add-an-http-trigger"></a>Přidání triggeru HTTP

Tato aktivační událost integrované provede volání protokolu HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete váš prázdné aplikace logiky v návrháři aplikace logiky.

1. V Návrháři zadejte do vyhledávacího pole "http" jako filtr. Z **triggery** seznamu, vyberte **HTTP** aktivační události.

   ![Výběr triggeru HTTP](./media/connectors-native-http/select-http-trigger.png)

   Tento příklad přejmenuje aktivační události "Triggeru HTTP" tak, aby měl více popisný název kroku. Také v příkladu později přidá akci HTTP a oba názvy musí být jedinečný.

1. Zadejte hodnoty pro [parametry aktivační události HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) , který chcete zahrnout do volání do cílového koncového bodu. Nastavení opakování pro jak často chcete, aby se aktivační událost zkontrolujte cílový koncový bod.

   ![Zadejte parametry triggeru HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Další informace o dostupných typech ověřování pro protokol HTTP, naleznete v tématu [ověřování protokolu HTTP triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Chcete-li přidat další dostupné parametry, otevřete **přidat nový parametr** seznam a vyberte požadované parametry.

1. Pokračujte v sestavování pracovního postupu aplikace logiky s akcemi, které běží na aktivaci triggeru.

1. Jakmile budete hotovi, budete hotovi, nezapomeňte si uložit aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

## <a name="add-an-http-action"></a>Přidání akce HTTP

Toto integrované akce provede volání protokolu HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevření aplikace logiky v návrháři aplikace logiky.

   Tento příklad používá jako první krok triggeru HTTP.

1. V části krok, ve které chcete přidat akci HTTP, vyberte **nový krok**.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. Vyberte znaménko plus ( **+** ), který se zobrazí a pak vyberte **přidat akci**.

1. V Návrháři zadejte do vyhledávacího pole "http" jako filtr. Z **akce** seznamu, vyberte **HTTP** akce.

   ![Výběr akce HTTP](./media/connectors-native-http/select-http-action.png)

   Tento příklad přejmenuje akci, která se "Akce HTTP" tak, aby měl více popisný název kroku.

1. Zadejte hodnoty pro [parametry akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) , který chcete zahrnout do volání do cílového koncového bodu.

   ![Zadejte parametry akce HTTP](./media/connectors-native-http/http-action-parameters.png)

   Další informace o dostupných typech ověřování pro protokol HTTP, naleznete v tématu [ověřování protokolu HTTP triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Chcete-li přidat další dostupné parametry, otevřete **přidat nový parametr** seznam a vyberte požadované parametry.

1. Jakmile budete hotovi, nezapomeňte si uložit aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech aktivační události a akce najdete v těchto částech:

* [Parametry triggeru HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parametry akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Podrobnosti výstupu

Zde jsou další informace o výstupy z triggeru HTTP nebo akce, které vrací tyto informace:

| Název vlastnosti | Type | Popis |
|---------------|------|-------------|
| Záhlaví | objekt | Hlavičky požadavku |
| Text | objekt | JSON – objekt | Objekt s obsah textu požadavku |
| Stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Přijato |
| 400 | Nesprávná žádost |
| 401 | Neautorizováno |
| 403 | Zakázáno |
| 404 | Nebyl nalezen |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
