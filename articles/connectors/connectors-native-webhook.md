---
title: Čekání a reakce na události
description: Automatizujte pracovní postupy, které spouštějí, pozastaví a obnoví na základě událostí na koncovém bodu služby pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 7c6f3c4e3e4a2a29fe6a02c03043e3dfb81a2010
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89227895"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Vytváření a spouštění automatizovaných pracovních postupů založených na událostech pomocí webhooků HTTP v Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovaného konektoru Webhooku protokolu HTTP můžete vytvářet automatizované úlohy a pracovní postupy, které se přihlásí k odběru koncového bodu služby, počkat na konkrétní události a spustit je na základě těchto událostí, nikoli pravidelně kontrolovat nebo *dotazovat* tento koncový bod.

Tady je několik příkladů pracovních postupů založených na webhookech:

* Před aktivací spuštění aplikace logiky počkejte, než se položka dorazí z [centra událostí Azure](https://github.com/logicappsio/EventHubAPI) .
* Před pokračováním pracovního postupu počkejte na schválení.

Tento článek ukazuje, jak použít Trigger Webhooku a akci Webhooku, aby vaše aplikace logiky mohla přijímat a reagovat na události na koncovém bodu služby.

## <a name="how-do-webhooks-work"></a>Jak Webhooky fungují?

Trigger Webhooku je založený na události, který nezávisí na pravidelné kontrole a dotazování na nové položky. Když uložíte aplikaci logiky, která začíná triggerem Webhooku, nebo když změníte aplikaci logiky z disabled na povolenou, Trigger Webhooku se *přihlásí k odběru* zadaného koncového bodu služby registrací *adresy URL zpětného volání* s tímto koncovým bodem. Aktivační událost potom počká, až koncový bod služby zavolá adresu URL, která spustí aplikaci logiky. Podobně jako u [triggeru žádosti](connectors-native-reqres.md)se aplikace logiky aktivuje hned, jakmile dojde k zadané události. Trigger Webhooku zruší *odběr* koncového bodu služby, pokud odeberete Trigger a uložíte aplikaci logiky nebo když změníte aplikaci logiky z povoleno na zakázáno.

Akce Webhooku je také založená na událostech a *odběrem* zadaného koncového bodu služby REGISTRUJE *adresu URL zpětného volání* s tímto koncovým bodem. Akce Webhooku pozastaví pracovní postup aplikace logiky a počká, až koncový bod služby zavolá adresu URL předtím, než pokračuje v běhu aplikace logiky. Akce Webhooku zruší *odběr* koncového bodu služby v těchto případech:

* Po úspěšném dokončení akce Webhooku
* Pokud při čekání na odpověď dojde ke zrušení běhu aplikace logiky
* Před vypršením časového limitu aplikace logiky

Příkladem akce Webhooku, která následuje za tímto vzorem, je například akce [**poslat e-mail se schválením**](connectors-create-api-office365-outlook.md) konektoru Office 365 Outlooku. Tento model můžete roztáhnout do libovolné služby pomocí akce Webhooku.

Další informace najdete v těchto tématech:

* [Webhooky a předplatná](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Vytváření vlastních rozhraní API, která podporují Webhook](../logic-apps/logic-apps-create-api-app.md)

Informace o šifrování, zabezpečení a autorizaci příchozích volání do vaší aplikace logiky, jako je například [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security), dříve označované jako SSL (Secure SOCKETS Layer) (SSL) nebo [Azure Active Directory otevřené ověřování (Azure AD OAuth)](../active-directory/develop/index.yml), najdete v tématu [zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí na základě požadavků](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Předpoklady

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL již nasazeného koncového bodu nebo rozhraní API, který podporuje pro [triggery Webhooku v aplikacích Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) nebo [Webhooku v akcích](../logic-apps/logic-apps-create-api-app.md#webhook-actions) Webhooku v aplikacích logiky, podle potřeby

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ve které chcete počkat na konkrétní události v cílovém koncovém bodě. Pokud chcete začít s triggerem Webhooku HTTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Webhooku protokolu HTTP, spusťte aplikaci logiky s libovolným triggerem, který chcete. V tomto příkladu se jako první krok používá Trigger HTTP.

## <a name="add-an-http-webhook-trigger"></a>Přidání triggeru Webhooku HTTP

Tato integrovaná aktivační událost volá koncový bod přihlášení k odběru cílové služby a zaregistruje adresu URL zpětného volání s cílovou službou. Vaše aplikace logiky potom počká, než cílová služba pošle `HTTP POST` požadavek na adresu URL zpětného volání. Když dojde k této události, aktivační událost se aktivuje a předá do pracovního postupu veškerá data v žádosti.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. Do vyhledávacího pole návrháře zadejte `http webhook` jako filtr. V seznamu **triggery** vyberte aktivační událost **http Webhooku** .

   ![Vybrat Trigger Webhooku protokolu HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Tento příklad přejmenuje Trigger na `HTTP Webhook trigger` tak, aby měl krok výstižnější název. Dále tento příklad přidá akci Webhooku HTTP a oba názvy musí být jedinečné.

1. Zadejte hodnoty pro [parametry triggeru Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) , které chcete použít pro odběry a zrušení odběru volání.

   V tomto příkladu Trigger zahrnuje metody, identifikátory URI a texty zpráv, které se mají použít při provádění operací přihlášení k odběru a zrušení odběru.

   ![Zadejte parametry triggeru Webhooku protokolu HTTP.](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Předplatné – metoda** | Yes | Metoda, která se má použít při přihlášení k odběru cílového koncového bodu |
   | **Přihlášení k odběru – identifikátor URI** | Yes | Adresa URL, která se má použít pro přihlášení k odběru cílového koncového bodu |
   | **Přihlášení k odběru – tělo** | No | Libovolný text zprávy, který má být zahrnut do žádosti o přihlášení k odběru. Tento příklad obsahuje adresu URL zpětného volání, která jednoznačně identifikuje předplatitele, což je vaše aplikace logiky, pomocí `@listCallbackUrl()` výrazu pro načtení adresy URL zpětného volání aplikace logiky. |
   | **Zrušit odběr – metoda** | No | Metoda, která se má použít při zrušení odběru cílového koncového bodu |
   | **Odhlásit odběr – identifikátor URI** | No | Adresa URL, která se má použít k odhlášení odběru cílového koncového bodu |
   | **Odhlásit odběr – tělo** | No | Volitelný text zprávy, který se má zahrnout do žádosti o zrušení odběru <p><p>**Poznámka**: Tato vlastnost nepodporuje použití `listCallbackUrl()` funkce. Trigger ale automaticky zahrne a pošle hlavičky `x-ms-client-tracking-id` a `x-ms-workflow-operation-name` , které může cílová služba použít k jednoznačné identifikaci předplatitele. |
   ||||

1. Chcete-li přidat další vlastnosti aktivační události, otevřete seznam **Přidat nový parametr** .

   ![Přidat další vlastnosti triggeru](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Pokud například potřebujete použít ověřování, můžete přidat **odběr-ověřování a zrušit** **odběr vlastností ověřování** . Další informace o typech ověřování dostupných pro Webhook HTTP najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Pokračujte v vytváření pracovního postupu aplikace logiky s akcemi, které se spustí, když se Trigger aktivuje.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Uložení aplikace logiky volá koncový bod přihlášení do cílové služby a zaregistruje adresu URL zpětného volání. Vaše aplikace logiky potom počká, než cílová služba pošle `HTTP POST` požadavek na adresu URL zpětného volání. Když dojde k této události, aktivační událost se aktivuje a předá do pracovního postupu veškerá data v žádosti. Pokud se tato operace úspěšně dokončí, Trigger se odhlásí od koncového bodu a vaše aplikace logiky pokračuje zbývajícím pracovním postupem.

## <a name="add-an-http-webhook-action"></a>Přidat akci Webhooku HTTP

Tato Vestavěná akce volá koncový bod přihlášení k odběru v cílové službě a zaregistruje adresu URL zpětného volání s cílovou službou. Vaše aplikace logiky se pak pozastaví a počká, až cílová služba pošle `HTTP POST` požadavek na adresu URL zpětného volání. Pokud k této události dojde, akce předá do pracovního postupu všechna data v žádosti. Pokud se operace úspěšně dokončí, akce odhlásit odběr koncového bodu a vaše aplikace logiky pokračuje v běhu zbývajícího pracovního postupu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se jako první krok používá Trigger Webhooku HTTP.

1. V kroku, kam chcete přidat akci Webhooku HTTP, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole návrháře zadejte `http webhook` jako filtr. V seznamu **Akce** vyberte akci **Webhooku http** .

   ![Vybrat akci Webhooku protokolu HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Tento příklad přejmenuje akci na akci Webhooku protokolu HTTP, aby měl krok výstižnější název.

1. Zadejte hodnoty pro parametry akce Webhooku protokolu HTTP, které se podobají [parametrům triggeru Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), které chcete použít pro přihlášení k odběru a zrušení odběru.

   V tomto příkladu zahrnuje tato akce metody, identifikátory URI a texty zpráv, které se mají použít při provádění operací přihlášení k odběru a zrušení odběru.

   ![Zadejte parametry akce Webhooku protokolu HTTP.](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Předplatné – metoda** | Yes | Metoda, která se má použít při přihlášení k odběru cílového koncového bodu |
   | **Přihlášení k odběru – identifikátor URI** | Yes | Adresa URL, která se má použít pro přihlášení k odběru cílového koncového bodu |
   | **Přihlášení k odběru – tělo** | No | Libovolný text zprávy, který má být zahrnut do žádosti o přihlášení k odběru. Tento příklad obsahuje adresu URL zpětného volání, která jednoznačně identifikuje předplatitele, což je vaše aplikace logiky, pomocí `@listCallbackUrl()` výrazu pro načtení adresy URL zpětného volání aplikace logiky. |
   | **Zrušit odběr – metoda** | No | Metoda, která se má použít při zrušení odběru cílového koncového bodu |
   | **Odhlásit odběr – identifikátor URI** | No | Adresa URL, která se má použít k odhlášení odběru cílového koncového bodu |
   | **Odhlásit odběr – tělo** | No | Volitelný text zprávy, který se má zahrnout do žádosti o zrušení odběru <p><p>**Poznámka**: Tato vlastnost nepodporuje použití `listCallbackUrl()` funkce. Tato akce ale automaticky zahrne a pošle hlavičky `x-ms-client-tracking-id` a `x-ms-workflow-operation-name` , které může cílová služba použít k jednoznačné identifikaci předplatitele. |
   ||||

1. Chcete-li přidat další vlastnosti akce, otevřete seznam **Přidat nový parametr** .

   ![Přidat další vlastnosti akce](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Pokud například potřebujete použít ověřování, můžete přidat **odběr-ověřování a zrušit** **odběr vlastností ověřování** . Další informace o typech ověřování dostupných pro Webhook HTTP najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Teď, když se tato akce spustí, vaše aplikace logiky zavolá koncový bod přihlášení k cílové službě a zaregistruje adresu URL zpětného volání. Aplikace logiky potom pozastaví pracovní postup a počká, až cílová služba odešle `HTTP POST` požadavek na adresu URL zpětného volání. Pokud k této události dojde, akce předá do pracovního postupu všechna data v žádosti. Pokud se operace úspěšně dokončí, akce odhlásit odběr koncového bodu a vaše aplikace logiky pokračuje v běhu zbývajícího pracovního postupu.

## <a name="trigger-and-action-outputs"></a>Výstupy triggeru a akce

Zde jsou další informace o výstupech z triggeru nebo akce Webhooku HTTP, které vrací tyto informace:

| Název vlastnosti | Typ | Description |
|---------------|------|-------------|
| záhlaví | object | Hlavičky z požadavku |
| text | object | Objekt JSON | Objekt s obsahem textu z požadavku |
| stavový kód | int | Stavový kód z požadavku |
|||

| Stavový kód | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Přijato |
| 400 | Chybný požadavek |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nenalezeno |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech Trigger a Action, které jsou podobné ostatním, najdete v tématu [parametry Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

## <a name="next-steps"></a>Další kroky

* [Zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí na základě požadavků](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Konektory pro Logic Apps](../connectors/apis-list.md)
