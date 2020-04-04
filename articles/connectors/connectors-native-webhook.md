---
title: Čekání a reakce na události
description: Automatizujte pracovní postupy, které aktivují, pozastavují a obnovují na základě událostí v koncovém bodě služby pomocí Aplikací logiky Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656285"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Vytváření a spouštění automatizovaných pracovních postupů založených na událostech pomocí http webhooků v Aplikacích Azure Logic Apps

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovaným konektorem HTTP Webhook můžete automatizovat pracovní postupy, které čekají a spouštějí na základě konkrétních událostí, ke kterým dochází v koncovém bodě HTTP nebo HTTPS, a to vytvářením aplikací logiky. Můžete například vytvořit aplikaci logiky, která monitoruje koncový bod služby čekáním na konkrétní událost před aktivací pracovního postupu a spuštěním zadaných akcí, nikoli pravidelně kontrolu nebo *dotazování* tohoto koncového bodu.

Zde jsou některé příklady pracovních postupů založených na událostech:

* Počkejte na položku, která má být doručena z [Centra událostí Azure](https://github.com/logicappsio/EventHubAPI) před spuštěním aplikace logiky.
* Před pokračováním v pracovním postupu počkejte na schválení.

## <a name="how-do-webhooks-work"></a>Jak fungují webhooky?

Aktivační událost http webhooku je založená na událostech, která nezávisí na pravidelné kontrole nebo dotazování nových položek. Když uložíte aplikaci *logiky,* která začíná aktivační událostí webhooku, nebo když změníte aplikaci logiky z depozitní na povolenou, aktivační událost webhooku se přihlásí k odběru konkrétní služby nebo koncového bodu registrací *adresy URL zpětného volání* s danou službou nebo koncovým bodem. Aktivační událost pak čeká na tuto službu nebo koncový bod volat adresu URL, která spustí aplikaci logiky. Podobně jako [požadavek aktivační události](connectors-native-reqres.md), aplikace logiky se aktivuje okamžitě, když dojde k zadané události. Aktivační událost *se odhlásí* ze služby nebo koncového bodu, pokud odeberete aktivační událost a uložíte aplikaci logiky nebo když změníte aplikaci logiky z povolené na zakázané.

Akce http webhooku je také založená na *událostech* a přihlásí se k určité službě nebo koncovému bodu registrací *adresy URL zpětného volání* s danou službou nebo koncovým bodem. Akce webhooku pozastaví pracovní postup aplikace logiky a čeká, dokud služba nebo koncový bod nezavolá adresu URL, než bude aplikace logiky obnovena. Aplikace logiky akce *se odhlásí* ze služby nebo koncového bodu v těchto případech:

* Po úspěšném dokončení akce webhooku
* Pokud je spuštění aplikace logiky zrušeno při čekání na odpověď
* Před časovým ustoužení aplikace logiky

Například [**e-mailová**](connectors-create-api-office365-outlook.md) akce Odeslat schválení schválení aplikace Office 365 Outlook je příkladem akce webhooku, která následuje podle tohoto vzoru. Tento vzor můžete rozšířit do libovolné služby pomocí akce webhooku.

> [!NOTE]
> Logic Apps vynucuje zabezpečení transportní vrstvy (TLS) 1.2 při příjmu volání zpět na aktivační událost nebo akci webového háku HTTP. Pokud se zobrazí chyby tls handshake, ujistěte se, že používáte TLS 1.2. Pro příchozí hovory jsou zde podporované šifrovací sady:
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

* [Parametry aktivační události http webhooku](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webové háčky a předplatná](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Vytvoření vlastních api, která podporují webový hák](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL pro již nasazený koncový bod nebo rozhraní API, které podporuje webhooku přihlásit a odhlásit vzor pro [webhooku aktivačních událostí v aplikacích logiky](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) nebo [webhooku akce v aplikacích logiky](../logic-apps/logic-apps-create-api-app.md#webhook-actions) podle potřeby

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, kde chcete čekat na konkrétní události v cílovém koncovém bodě. Chcete-li začít s aktivační událostí HTTP Webhook, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci HTTP Webhook, spusťte aplikaci logiky s libovolnou aktivační událostí, kterou chcete. Tento příklad používá aktivační událost HTTP jako první krok.

## <a name="add-an-http-webhook-trigger"></a>Přidání aktivační události HTTP Webhook

Tato integrovaná aktivační událost volá koncový bod odběru v cílové službě a zaregistruje adresu URL zpětného volání s cílovou službou. Aplikace logiky pak čeká na cílovou `HTTP POST` službu odeslat požadavek na adresu URL zpětného volání. Když dojde k této události, aktivační událost se aktivuje a předá všechna data v požadavku spolu s pracovním postupem.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Do vyhledávacího pole návrháře `http webhook` zadejte jako filtr. V seznamu **Aktivační události** vyberte aktivační událost **HTTP Webhook.**

   ![Vybrat aktivační událost HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Tento příklad přejmenuje `HTTP Webhook trigger` aktivační událost tak, aby krok měl popisnější název. Příklad také později přidá akci HTTP Webhook a oba názvy musí být jedinečné.

1. Zadejte hodnoty [parametrů aktivační události HTTP Webhook,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) které chcete použít pro přihlášení k odběru a odhlášení volání.

   V tomto příkladu aktivační událost zahrnuje metody, identifikátory URI a těla zpráv, které se mají použít při provádění operací odběru a odhlášení.

   ![Zadejte parametry aktivační události http webhooku](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Předplatné – metoda** | Ano | Metoda, která se má použít při přihlášení k odběru cílového koncového bodu |
   | **Přihlásit se k odběru - IDENTIFIKÁTOR URI** | Ano | Adresa URL, která se má použít pro přihlášení k odběru cílového koncového bodu |
   | **Přihlásit se k odběru - Tělo** | Ne | Jakékoli tělo zprávy zahrnout do žádosti o přihlášení k odběru. Tento příklad zahrnuje adresu URL zpětného volání, která jednoznačně identifikuje odběratele, což je vaše aplikace logiky, pomocí výrazu `@listCallbackUrl()` k načtení adresy URL zpětného volání aplikace logiky. |
   | **Odhlásit - metoda** | Ne | Metoda, která se má použít při odhlášení od cílového koncového bodu |
   | **Odhlásit - IDENTIFIKÁTOR URI** | Ne | Adresa URL, která se má použít pro odhlášení od cílového koncového bodu |
   | **Odhlásit - tělo** | Ne | Volitelný text zprávy, který má být zahrnut do žádosti o odhlášení <p><p>**Poznámka:** Tato vlastnost nepodporuje `listCallbackUrl()` pomocí funkce. Aktivační událost však automaticky zahrnuje a `x-ms-client-tracking-id` `x-ms-workflow-operation-name`odešle záhlaví a , které cílová služba můžete použít k jednoznačné identifikaci odběratele. |
   ||||

1. Chcete-li přidat další vlastnosti aktivační události, otevřete seznam **Přidat nový parametr.**

   ![Přidání dalších vlastností aktivační události](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Například pokud potřebujete použít ověřování, můžete přidat **Přihlásit se k odběru - Ověřování** a **Odhlásit - Ověřování** vlastnosti. Další informace o typech ověřování dostupných pro protokol HTTP Webhook naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Pokračujte v vytváření pracovního postupu aplikace logiky pomocí akcí, které se spustí při aktivaci aktivační události.

1. Až budete hotovi, hotovo, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Uložení aplikace logiky volá koncový bod odběru v cílové službě a zaregistruje adresu URL zpětného volání. Aplikace logiky pak čeká na cílovou `HTTP POST` službu odeslat požadavek na adresu URL zpětného volání. Když dojde k této události, aktivační událost se aktivuje a předá všechna data v požadavku spolu s pracovním postupem. Pokud tato operace úspěšně dokončí, aktivační událost se odhlásí z koncového bodu a aplikace logiky pokračuje zbývající pracovní postup.

## <a name="add-an-http-webhook-action"></a>Přidání akce http webhooku

Tato předdefinovaná akce volá koncový bod odběru v cílové službě a zaregistruje adresu URL zpětného volání s cílovou službou. Aplikace logiky pak pozastaví a čeká na `HTTP POST` cílovou službu odeslat požadavek na adresu URL zpětného volání. Když dojde k této události, akce předá všechna data v požadavku spolu s pracovním postupem. Pokud se operace úspěšně dokončí, akce se odhlásí z koncového bodu a aplikace logiky pokračuje v spuštění zbývajícího pracovního postupu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá aktivační událost HTTP Webhook jako první krok.

1. Pod krokem, kam chcete přidat akci HTTP Webhook, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole návrháře `http webhook` zadejte jako filtr. Ze seznamu **Akce** vyberte akci **HTTP Webhook.**

   ![Vybrat akci HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Tento příklad přejmenuje akci na "AKCE HTTP Webhook" tak, aby krok měl popisnější název.

1. Zadejte hodnoty parametrů akce HTTP Webhook, které jsou podobné [parametrům aktivační události HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), které chcete použít pro přihlášení k odběru a odhlášení volání.

   V tomto příkladu akce zahrnuje metody, identifikátory URI a těla zpráv, které se mají použít při provádění operací odběru a odhlášení.

   ![Zadejte parametry akce HTTP Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Předplatné – metoda** | Ano | Metoda, která se má použít při přihlášení k odběru cílového koncového bodu |
   | **Přihlásit se k odběru - IDENTIFIKÁTOR URI** | Ano | Adresa URL, která se má použít pro přihlášení k odběru cílového koncového bodu |
   | **Přihlásit se k odběru - Tělo** | Ne | Jakékoli tělo zprávy zahrnout do žádosti o přihlášení k odběru. Tento příklad zahrnuje adresu URL zpětného volání, která jednoznačně identifikuje odběratele, což je vaše aplikace logiky, pomocí výrazu `@listCallbackUrl()` k načtení adresy URL zpětného volání aplikace logiky. |
   | **Odhlásit - metoda** | Ne | Metoda, která se má použít při odhlášení od cílového koncového bodu |
   | **Odhlásit - IDENTIFIKÁTOR URI** | Ne | Adresa URL, která se má použít pro odhlášení od cílového koncového bodu |
   | **Odhlásit - tělo** | Ne | Volitelný text zprávy, který má být zahrnut do žádosti o odhlášení <p><p>**Poznámka:** Tato vlastnost nepodporuje `listCallbackUrl()` pomocí funkce. Akce však automaticky zahrnuje a odešle záhlaví `x-ms-client-tracking-id` a `x-ms-workflow-operation-name`, které cílová služba můžete použít k jednoznačné identifikaci odběratele. |
   ||||

1. Chcete-li přidat další vlastnosti akce, otevřete seznam **Přidat nový parametr.**

   ![Přidání dalších vlastností akce](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Například pokud potřebujete použít ověřování, můžete přidat **Přihlásit se k odběru - Ověřování** a **Odhlásit - Ověřování** vlastnosti. Další informace o typech ověřování dostupných pro protokol HTTP Webhook naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Teď, když tato akce spustí, aplikace logiky volá koncový bod odběru na cílové služby a zaregistruje adresu URL zpětného volání. Aplikace logiky pak pozastaví pracovní postup a čeká `HTTP POST` na cílovou službu odeslat požadavek na adresu URL zpětného volání. Když dojde k této události, akce předá všechna data v požadavku spolu s pracovním postupem. Pokud se operace úspěšně dokončí, akce se odhlásí z koncového bodu a aplikace logiky pokračuje v spuštění zbývajícího pracovního postupu.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech aktivační události a akce, které jsou navzájem podobné, naleznete v [tématu http webhook parametry](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Podrobnosti o výstupu

Zde je více informací o výstupech z aktivační události nebo akce HTTP Webhook, která vrací tyto informace:

| Název vlastnosti | Typ | Popis |
|---------------|------|-------------|
| Záhlaví | objekt | Záhlaví z požadavku |
| text | objekt | Objekt JSON | Objekt s obsahem těla z požadavku |
| stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Chybný požadavek |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nebyl nalezen. |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
