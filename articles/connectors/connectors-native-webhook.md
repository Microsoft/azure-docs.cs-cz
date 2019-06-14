---
title: Vytvořit pracovní postupy založené na události nebo akce – Azure Logic Apps | Dokumentace Microsoftu
description: Automatizovat pracovní postupy založené na události nebo akce pomocí webhooků a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447187"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Vytvořit pracovní postupy založené na události nebo akce pomocí webhooků a Azure Logic Apps

Pomocí aktivační události a akce webhooku můžete spustit, pozastavit a obnovit toky k provedení těchto úloh:

* Aktivaci [Azure Event Hubs](https://github.com/logicappsio/EventHubAPI) při přijetí položky
* Čekání na schválení před pokračováním pracovní postup

Další informace o [jak vytvořit vlastní rozhraní API, které podporují webhooku](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Pomocí aktivační události webhooku

A [ *aktivační událost* ](../connectors/apis-list.md) je událost, která spustí pracovní postup aplikace logiky. Trigger webhooku je založený na událostech, které nejsou závislé na dotazování pro nové položky. Při uložení aplikace logiky s triggerem webhook nebo při změně svou aplikaci logiky ze zakázaného na povolený trigger webhooku *přihlásí* zadaná služba nebo koncový bod tak, že zaregistrujete *adresuURLzpětnéhovolání* s tímto služba nebo koncový bod. Aktivační události potom použije tuto adresu URL pro spuštění aplikace logiky podle potřeby. Podobně jako [triggeru požadavku](connectors-native-reqres.md), aktivuje aplikace logiky se okamžitě, když se stane Očekávaná událost. Aktivační událost *ruší registraci* -li odebrat aktivační událost a uložení aplikace logiky, nebo při změně svou aplikaci logiky z povoleno na zakázáno.

Tady je příklad, který ukazuje, jak nastavit aktivační událost HTTP v návrháři aplikace logiky. V krocích se předpokládá, že jste již nasadili nebo mají přístup k rozhraní API, která následuje [odběru webhooku a odhlášení odběru model ve službě logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**Přidání triggeru webhooku**

1. Přidat **HTTP Webhook** aktivační událost jako první krok v aplikaci logiky.
2. Vyplňte parametry pro přihlášení odběru webhooku a zrušit její volání.

   Tento krok používá stejný vzor jako [akce HTTP](connectors-native-http.md) formátu.

     ![Trigger HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Přidejte alespoň jednu akci.
4. Klikněte na tlačítko **Uložit** publikovat aplikaci logiky. Tento krok volání koncového bodu přihlásit k odběru se adresa URL zpětného volání, které jsou potřeba k aktivaci tato aplikace logiky.
5. Vždy, když služba provede `HTTP POST` na adresu URL zpětného volání, aplikace logiky je vyvoláno a zahrnuje všechna data předaná do požadavku.

## <a name="use-the-webhook-action"></a>Pomocí akce webhooku

[ *Akce* ](../connectors/apis-list.md) je operace, která je definována a spustit v pracovním postupu vaší aplikace logiky. Při spuštění akce webhooku, tato akce aplikace logiky *přihlásí* zadaná služba nebo koncový bod tak, že zaregistrujete *adresu URL zpětného volání* s tímto služba nebo koncový bod. Akce webhooku potom počká, dokud, servisní volání adresy URL před obnoví aplikace logiky spuštěná. Aplikace logiky odhlásí služba nebo koncový bod v těchto případech: 

* Po úspěšném dokončení akce webhooku
* Pokud se při čekání na odpověď zruší běh aplikace logiky
* Před logiku aplikace vyprší časový limit

Například [ **odeslat schvalovací e-mail** ](connectors-create-api-office365-outlook.md) akce je příklad akce webhooku, který používá tento vzor. Tento model můžete rozšířit do jakékoli služby prostřednictvím akce webhooku. 

Tady je příklad, který ukazuje, jak vytvořit akce webhooku v návrháři aplikace logiky. Tento postup předpokládá, že jste již nasadili nebo mají přístup k rozhraní API, která následuje [odběru webhooku a odhlášení odběru používaným ve službě logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

**Přidání akce webhooku**

1. Zvolte **nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte "webhooku" Najít **HTTP Webhook** akce.

    ![Výběr akce dotazu](./media/connectors-native-webhook/using-action-1.png)

3. Vyplňte parametry pro přihlášení odběru webhooku a zrušit její volání

   Tento krok používá stejný vzor jako [akce HTTP](connectors-native-http.md) formátu.

     ![Akce dokončení dotazu](./media/connectors-native-webhook/using-action-2.png)
   
   Aplikace logiky za běhu, zavolá koncový bod přihlásit k odběru po dosažení tohoto kroku.

4. Klikněte na tlačítko **Uložit** publikovat aplikaci logiky.

## <a name="technical-details"></a>Technické podrobnosti

Tady jsou další podrobnosti o aktivační události a akce podporuje tento webhook.

## <a name="webhook-triggers"></a>Aktivační události Webhooku

| Akce | Popis |
| --- | --- |
| HTTP Webhook |Odběru adresu URL zpětného volání pro službu, která může volat adresu URL, která se aktivuje aplikace logiky podle potřeby. |

### <a name="trigger-details"></a>Podrobnosti o triggeru

#### <a name="http-webhook"></a>HTTP Webhook

Odběru adresu URL zpětného volání pro službu, která může volat adresu URL, která se aktivuje aplikace logiky podle potřeby.
\* Znamená, že požadované pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Přihlášení odběru metoda * |method |Metoda HTTP pro žádosti o předplatné |
| Přihlášení odběru URI * |uri |Identifikátor URI HTTP pro žádosti o předplatné |
| Odhlásit odběr – metoda * |method |Metoda HTTP pro žádost o zrušení odběru |
| Odhlásit odběr URI * |uri |Identifikátor URI HTTP pro žádost o zrušení odběru |
| Přihlášení odběru textu |Text |Požadavku HTTP pro přihlášení k odběru |
| Přihlášení odběru záhlaví |Záhlaví |Hlavičky požadavků HTTP pro přihlášení k odběru |
| Ověřování přihlášení odběru |ověřování |Ověřování protokolu HTTP pro přihlášení k odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |
| Odhlásit odběr textu |Text |Požadavku HTTP pro zrušení odběru |
| Odhlásit odběr záhlaví |Záhlaví |Hlavičky požadavků HTTP pro zrušení odběru |
| Odhlásit odběr ověřování |ověřování |Ověřování protokolu HTTP pro zrušení odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |

**Podrobnosti výstupu**

Požadavek Webhooku

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Hlavičky |objekt |Hlavičky požadavku Webhooku |
| Tělo |objekt |Objekt požadavku Webhooku |
| Stavový kód |int |Stavový kód žádosti o Webhooku |

## <a name="webhook-actions"></a>Akce Webhooku

| Akce | Popis |
| --- | --- |
| HTTP Webhook |Adresa URL zpětného volání službě, která může volat adresu URL obnovit podle potřeby kroku pracovního postupu odběru. |

### <a name="action-details"></a>Podrobnosti akce

#### <a name="http-webhook"></a>HTTP Webhook

Adresa URL zpětného volání službě, která může volat adresu URL obnovit podle potřeby kroku pracovního postupu odběru.
\* Znamená, že požadované pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Přihlášení odběru metoda * |method |Metoda HTTP pro žádosti o předplatné |
| Přihlášení odběru URI * |uri |Identifikátor URI HTTP pro žádosti o předplatné |
| Odhlásit odběr – metoda * |method |Metoda HTTP pro žádost o zrušení odběru |
| Odhlásit odběr URI * |uri |Identifikátor URI HTTP pro žádost o zrušení odběru |
| Přihlášení odběru textu |Text |Požadavku HTTP pro přihlášení k odběru |
| Přihlášení odběru záhlaví |Záhlaví |Hlavičky požadavků HTTP pro přihlášení k odběru |
| Ověřování přihlášení odběru |ověřování |Ověřování protokolu HTTP pro přihlášení k odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |
| Odhlásit odběr textu |Text |Požadavku HTTP pro zrušení odběru |
| Odhlásit odběr záhlaví |Záhlaví |Hlavičky požadavků HTTP pro zrušení odběru |
| Odhlásit odběr ověřování |ověřování |Ověřování protokolu HTTP pro zrušení odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |

**Podrobnosti výstupu**

Požadavek Webhooku

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Hlavičky |objekt |Hlavičky požadavku Webhooku |
| Tělo |objekt |Objekt požadavku Webhooku |
| Stavový kód |int |Stavový kód žádosti o Webhooku |

## <a name="next-steps"></a>Další postup

* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vyhledání dalších konektorů](apis-list.md)
