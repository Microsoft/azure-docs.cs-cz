---
title: Vytvoření úkolů na základě událostí a pracovních postupů v Azure Logic Apps
description: Spuštění, pozastavení a obnovení automatizovaných úloh, procesy a pracovní postupy založené na události, ke kterým dochází na koncový bod pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541353"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatizace úloh založený na událostech a pracovních postupů pomocí webhooků HTTP v Azure Logic Apps

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovaného konektoru Webhooku protokolu HTTP, můžete automatizovat pracovní postupy, které počkejte a spusťte závislosti na konkrétní události, ke kterým dochází na koncový bod HTTP nebo HTTPS díky vytváření aplikací logiky. Například můžete vytvořit aplikaci logiky, která monitoruje koncového bodu služby tím, že pro určitou událost před aktivuje pracovní postup a spuštění zadaných akcí, spíše než pravidelně kontroluje nebo *dotazování* tohoto koncového bodu.

Tady jsou některé příklady založené na události pracovních postupů:

* Počkejte položky můžete přejít z [Azure Event Hubs](https://github.com/logicappsio/EventHubAPI) před aktivací běh aplikace logiky.
* Počkejte, než budete pokračovat pracovního postupu schvalování.

## <a name="how-do-webhooks-work"></a>Jak fungují webhooků?

Aktivační událost webhooku HTTP je založený na událostech, které nejsou závislé na kontrolu nebo dotazování pravidelně pro nové položky. Při uložení aplikace logiky, která se spouští triggerem webhook nebo při změně svou aplikaci logiky ze zakázaného na povolený, trigger webhooku *přihlásí* konkrétní služba nebo koncový bod tak, že zaregistrujete *adresuURLzpětnéhovolání* s tímto služba nebo koncový bod. Aktivační události potom počká tento služba nebo koncový bod pro volání adresu URL, která se spustí aplikace logiky. Podobně jako [triggeru požadavku](connectors-native-reqres.md), aktivuje aplikace logiky se okamžitě, když se stane zadané události. Aktivační událost *ruší registraci* ze služby nebo koncového bodu je-li odebrat aktivační událost a uložte aplikaci logiky nebo při změně svou aplikaci logiky z povoleno na zakázáno.

Akce webhooku HTTP je taky založený na událostech a *přihlásí* konkrétní služba nebo koncový bod tak, že zaregistrujete *adresu URL zpětného volání* s tímto služba nebo koncový bod. Pozastaví pracovní postup aplikace logiky a počká, dokud se služba nebo koncový bod volání adresy URL před obnoví aplikace logiky spuštěná akce webhooku. Akce aplikace logiky *ruší registraci* ze služby nebo koncového bodu v těchto případech:

* Po úspěšném dokončení akce webhooku
* Pokud se při čekání na odpověď zruší běh aplikace logiky
* Před logiku aplikace vyprší časový limit

Například Office 365 Outlook konektoru [ **odeslat schvalovací e-mail** ](connectors-create-api-office365-outlook.md) akce je příklad akce webhooku, který používá tento vzor. Tento model můžete rozšířit na libovolnou službu pomocí akce webhooku.

Další informace najdete v těchto tématech:

* [Parametry triggeru Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooky a předplatná](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Vytvoření vlastních rozhraní API, které podporují webhooku](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL pro koncový bod již nasazenou nebo rozhraní API, které podporuje webhook odběru a vzor pro zrušení odběru [triggerů webhooků v logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) nebo [akce webhooku v logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) podle potřeby

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ve které chcete počkat na konkrétní akce na cílový koncový bod. Spustit s triggerem HTTP Webhook [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Použití akce Webhooku protokolu HTTP, začněte libovolný trigger, který chcete svou aplikaci logiky. Tento příklad používá jako první krok triggeru HTTP.

## <a name="add-an-http-webhook-trigger"></a>Přidat aktivační událost Webhooku protokolu HTTP

Tento trigger integrované zaregistruje zadanou službu adresu URL zpětného volání a čeká na tuto službu k odeslání požadavku HTTP POST na tuto adresu URL. Pokud k této události dojde, aktivuje se a okamžitě spustí aplikace logiky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete váš prázdné aplikace logiky v návrháři aplikace logiky.

1. V Návrháři zadejte do vyhledávacího pole "http webhook" jako filtr. Z **triggery** seznamu, vyberte **HTTP Webhook** aktivační události.

   ![Vyberte trigger Webhooku protokolu HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Tento příklad přejmenuje aktivační události "Trigger Webhooku HTTP" tak, aby měl více popisný název kroku. Také v příkladu později přidá akce Webhooku protokolu HTTP a oba názvy musí být jedinečný.

1. Zadejte hodnoty pro [parametry aktivační události Webhooku HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) , že chcete použít pro přihlásit k odběru a zrušit její volání, například:

   ![Zadejte parametry aktivační události Webhooku protokolu HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Chcete-li přidat další dostupné parametry, otevřete **přidat nový parametr** seznam a vyberte požadované parametry.

   Další informace o dostupných typech ověřování pro HTTP Webhook najdete v tématu [ověřování protokolu HTTP triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Pokračujte v sestavování pracovního postupu aplikace logiky s akcemi, které běží na aktivaci triggeru.

1. Jakmile budete hotovi, budete hotovi, nezapomeňte si uložit aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

   Uložení aplikace logiky zavolá koncový bod přihlásit k odběru a zaregistruje adresu URL zpětného volání pro spuštění této aplikace logiky.

1. Vždy, když cílovou službu nyní, odešle `HTTP POST` žádost o adresu URL zpětného volání, aktivuje aplikace logiky a obsahuje všechna data, která se předá prostřednictvím požadavku.

## <a name="add-an-http-webhook-action"></a>Přidání akce Webhooku protokolu HTTP

Tato akce integrované zaregistruje zadanou službu adresu URL zpětného volání, pozastaví pracovní postup aplikace logiky a čeká na tuto službu k odeslání požadavku HTTP POST na tuto adresu URL. Pokud k této události dojde, akce obnoví spuštění aplikace logiky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevření aplikace logiky v návrháři aplikace logiky.

   Tento příklad používá trigger Webhooku HTTP jako první krok.

1. V části krok, ve které chcete přidat akce Webhooku protokolu HTTP, vyberte **nový krok**.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. Vyberte znaménko plus ( **+** ), který se zobrazí a pak vyberte **přidat akci**.

1. V Návrháři zadejte do vyhledávacího pole "http webhook" jako filtr. Z **akce** seznamu, vyberte **HTTP Webhook** akce.

   ![Výběr akce Webhooku protokolu HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Tento příklad přejmenuje akci, která se "Akce Webhooku HTTP" tak, aby měl více popisný název kroku.

1. Zadejte hodnoty pro HTTP Webhook parametry akce, které jsou podobné [parametry aktivační události Webhooku HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) , že chcete použít pro přihlásit k odběru a zrušit její volání, například:

   ![Zadejte parametry akce Webhooku protokolu HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Za běhu aplikace logiky zavolá koncový bod přihlásit k odběru při spuštění této akce. Aplikace logiky pak pozastaví pracovní postup a čeká na cílovou službu k odeslání `HTTP POST` žádost o adresu URL zpětného volání. Pokud se akce dokončí úspěšně, zruší akci z koncového bodu a pokračuje v aplikaci logiky spuštění pracovního postupu.

1. Chcete-li přidat další dostupné parametry, otevřete **přidat nový parametr** seznam a vyberte požadované parametry.

   Další informace o dostupných typech ověřování pro HTTP Webhook najdete v tématu [ověřování protokolu HTTP triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Jakmile budete hotovi, nezapomeňte si uložit aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech aktivační události a akce, které jsou podobné k sobě navzájem, naleznete v tématu [parametry Webhooku HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Podrobnosti výstupu

Zde jsou další informace o výstupy z triggeru Webhooku protokolu HTTP nebo akce, které vrací tyto informace:

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
