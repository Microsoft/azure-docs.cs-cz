---
title: Čekání a reakce na události
description: Automatizujte pracovní postupy, které spouštějí, pozastaví a obnoví na základě událostí na koncovém bodu služby pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80656285"
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
> Logic Apps vynutila TLS (Transport Layer Security) 1,2 při přijetí volání zpět do triggeru nebo akce Webhooku protokolu HTTP. Pokud se zobrazí chyby handshake TLS, ujistěte se, že používáte TLS 1,2. V případě příchozích volání jsou zde podporované šifrovací sady:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

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

Tato integrovaná aktivační událost volá koncový bod přihlášení k odběru cílové služby a zaregistruje adresu URL zpětného volání s cílovou službou. Vaše aplikace logiky potom počká, než cílová služba pošle `HTTP POST` požadavek na adresu URL zpětného volání. Když dojde k této události, aktivační událost se aktivuje a předá do pracovního postupu veškerá data v žádosti.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

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

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Otevřete aplikaci logiky v návrháři aplikace logiky.

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

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech Trigger a Action, které jsou podobné ostatním, najdete v tématu [parametry Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Podrobnosti výstupu

Zde jsou další informace o výstupech z triggeru nebo akce Webhooku HTTP, které vrací tyto informace:

| Název vlastnosti | Typ | Description |
|---------------|------|-------------|
| záhlaví | odkazy objektů | Hlavičky z požadavku |
| text | odkazy objektů | Objekt JSON | Objekt s obsahem textu z požadavku |
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

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
