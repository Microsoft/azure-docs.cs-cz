---
title: Čekání a reakce na události
description: Automatizujte pracovní postupy, které spouštějí, pozastaví a obnoví na základě událostí na koncovém bodu služby pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 14daa37a414e814ed6de036bbb625933ce2532e0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888120"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Vytváření a spouštění automatizovaných pracovních postupů založených na událostech pomocí webhooků HTTP v Azure Logic Apps

Díky [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovanému konektoru Webhooku protokolu HTTP můžete automatizovat pracovní postupy, které čekají a spouštějí na základě konkrétních událostí, ke kterým dochází na koncovém bodu http nebo https vytvořením Logic Apps. Můžete například vytvořit aplikaci logiky, která monitoruje koncový bod služby čekáním na konkrétní událost před aktivací pracovního postupu a spuštěním zadaných akcí místo pravidelného ověřování a *cyklického dotazování* daného koncového bodu.

Tady je několik příkladů pracovních postupů založených na událostech:

* Před aktivací spuštění aplikace logiky počkejte, než se položka dorazí z [centra událostí Azure](https://github.com/logicappsio/EventHubAPI) .
* Před pokračováním pracovního postupu počkejte na schválení.

## <a name="how-do-webhooks-work"></a>Jak Webhooky fungují?

Trigger Webhooku protokolu HTTP je založený na události, který nezávisí na pravidelné kontrole a dotazování na nové položky. Když uložíte aplikaci logiky, která začíná triggerem Webhooku, nebo když změníte aplikaci logiky z disabled na povolenou, Trigger Webhooku se *přihlásí* ke konkrétní službě nebo koncovému bodu tím, že pomocí této služby nebo koncového bodu zaregistruje *adresu URL zpětného volání* . Aktivační událost pak počká, až služba nebo koncový bod zavolá adresu URL, která spustí aplikaci logiky. Podobně jako u [triggeru žádosti](connectors-native-reqres.md)se aplikace logiky aktivuje hned, jakmile dojde k zadané události. Aktivační procedura *odhlásí odběr* služby nebo koncového bodu, pokud odeberete Trigger a uložíte aplikaci logiky, nebo když změníte aplikaci logiky z povoleno na zakázáno.

Akce Webhooku protokolu HTTP je také založená na událostech a *přihlašuje* se k odběru konkrétní služby nebo koncového bodu registrací *adresy URL zpětného volání* s touto službou nebo koncovým bodem. Akce Webhooku pozastaví pracovní postup aplikace logiky a počká, dokud služba nebo koncový bod nevolá adresu URL předtím, než aplikace logiky pokračuje v běhu. Aplikace logiky akcí zruší *odběr* služby nebo koncového bodu v těchto případech:

* Po úspěšném dokončení akce Webhooku
* Pokud při čekání na odpověď dojde ke zrušení běhu aplikace logiky
* Před vypršením časového limitu aplikace logiky

Příkladem akce Webhooku, která následuje za tímto vzorem, je například akce [**poslat e-mail se schválením**](connectors-create-api-office365-outlook.md) konektoru Office 365 Outlooku. Tento model můžete roztáhnout do libovolné služby pomocí akce Webhooku.

> [!NOTE]
> Logic Apps vynutila TLS (Transport Layer Security) 1,2 při přijetí volání zpět do triggeru nebo akce Webhooku protokolu HTTP. Pokud se zobrazí chyby handshake SSL, ujistěte se, že používáte TLS 1,2.

Další informace najdete v těchto tématech:

* [Aktivační parametry Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooky a předplatná](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Vytváření vlastních rozhraní API, která podporují Webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL již nasazeného koncového bodu nebo rozhraní API, který podporuje pro [triggery Webhooku v aplikacích Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) nebo [Webhooku v akcích](../logic-apps/logic-apps-create-api-app.md#webhook-actions) Webhooku v aplikacích logiky, podle potřeby

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ve které chcete počkat na konkrétní události v cílovém koncovém bodě. Pokud chcete začít s triggerem Webhooku HTTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Webhooku protokolu HTTP, spusťte aplikaci logiky s libovolným triggerem, který chcete. V tomto příkladu se jako první krok používá Trigger HTTP.

## <a name="add-an-http-webhook-trigger"></a>Přidání triggeru Webhooku HTTP

Tato integrovaná aktivační událost zaregistruje adresu URL zpětného volání se zadanou službou a počká, až tato služba pošle do této adresy URL požadavek HTTP POST. Když dojde k této události, Trigger se aktivuje a okamžitě spustí aplikaci logiky.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. V Návrháři zadejte do vyhledávacího pole "Webhook http" jako filtr. V seznamu **triggery** vyberte aktivační událost **http Webhooku** .

   ![Vybrat Trigger Webhooku protokolu HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   V tomto příkladu se aktivační událost přejmenuje na Trigger Webhooku HTTP, aby měl krok výstižnější název. Dále tento příklad přidá akci Webhooku HTTP a oba názvy musí být jedinečné.

1. Zadejte hodnoty pro [parametry triggeru Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) , které chcete použít pro přihlášení k odběru a zrušení odběru, například:

   ![Zadejte parametry triggeru Webhooku protokolu HTTP.](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

   Další informace o typech ověřování dostupných pro Webhook HTTP najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Pokračujte v vytváření pracovního postupu aplikace logiky s akcemi, které se spustí, když se Trigger aktivuje.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Uložení aplikace logiky volá koncový bod pro předplatné a zaregistruje adresu URL zpětného volání pro aktivaci této aplikace logiky.

1. Když teď cílová služba pošle požadavek `HTTP POST` na adresu URL zpětného volání, aplikace logiky se aktivuje a zahrne všechna data, která se předávají prostřednictvím žádosti.

## <a name="add-an-http-webhook-action"></a>Přidat akci Webhooku HTTP

Tato Vestavěná akce zaregistruje adresu URL zpětného volání se zadanou službou, pozastaví pracovní postup aplikace logiky a počká, až tato služba odešle požadavek HTTP POST do této adresy URL. Když dojde k této události, akce pokračuje v běhu aplikace logiky.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se jako první krok používá Trigger Webhooku HTTP.

1. V kroku, kam chcete přidat akci Webhooku HTTP, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V Návrháři zadejte do vyhledávacího pole "Webhook http" jako filtr. V seznamu **Akce** vyberte akci **Webhooku http** .

   ![Vybrat akci Webhooku protokolu HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Tento příklad přejmenuje akci na akci Webhooku protokolu HTTP, aby měl krok výstižnější název.

1. Zadejte hodnoty parametrů akce Webhooku protokolu HTTP, které se podobají [parametrům triggeru Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) , které chcete použít pro přihlášení k odběru a zrušení odběru, například:

   ![Zadejte parametry akce Webhooku protokolu HTTP.](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Během běhu aplikace logiky volá koncový bod přihlášení k odběru při spuštění této akce. Vaše aplikace logiky potom pozastaví pracovní postup a počká, až cílová služba pošle na adresu URL zpětného volání požadavek `HTTP POST`. Pokud se akce úspěšně dokončí, akce odhlásit odběr koncového bodu a vaše aplikace logiky pokračuje ve spuštění pracovního postupu.

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

   Další informace o typech ověřování dostupných pro Webhook HTTP najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech Trigger a Action, které jsou podobné ostatním, najdete v tématu [parametry Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Podrobnosti výstupu

Zde jsou další informace o výstupech z triggeru nebo akce Webhooku HTTP, které vrací tyto informace:

| Název vlastnosti | Typ | Popis |
|---------------|------|-------------|
| záhlaví | object | Hlavičky z požadavku |
| text | object | JSON – objekt | Objekt s obsahem textu z požadavku |
| stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Přijato |
| 400 | Nesprávná žádost |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nenalezeno |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
