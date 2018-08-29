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
ms.openlocfilehash: 7b1886321ca4afd4b4710bd9fddf16d2d5eb224b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126583"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Vytvořit pracovní postupy založené na události nebo akce pomocí webhooků a Azure Logic Apps

Pomocí aktivační události a akce webhooku můžete spustit, pozastavit a obnovit toky k provedení těchto úloh:

* Aktivaci [Azure Event Hubs](https://github.com/logicappsio/EventHubAPI) při přijetí položky
* Čekání na schválení před pokračováním pracovní postup

Další informace o [jak vytvořit vlastní rozhraní API, které podporují webhooku](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Pomocí aktivační události webhooku

A [ *aktivační událost* ](connectors-overview.md) je událost, která spustí pracovní postup aplikace logiky. Aktivační událost webhooku je založený na událostech a nemusí spoléhat na dotazování pro nové položky. Podobně jako [triggeru požadavku](connectors-native-reqres.md), aplikace logiky je vyvoláno pro rychlé, ke které dochází události. Zaregistruje trigger webhooku *adresu URL zpětného volání* pro službu a používá tuto adresu URL, aby se mohly aktivovat aplikaci logiky podle potřeby.

Tady je příklad, který ukazuje, jak nastavit aktivační událost HTTP v návrháři aplikace logiky. V krocích se předpokládá, že jste již nasadili nebo mají přístup k rozhraní API, která následuje [odběru webhooku a odhlášení odběru model ve službě logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Přihlásit k odběru volání, pokaždé, když se aplikace logiky se uloží nový webhook nebo přechod ze zakázaného na povolený. Je provedeno volání zrušení odběru, když trigger webhooku aplikace logiky je odebrat a uložit nebo přechod z povoleno na zakázáno.

**Přidání triggeru webhooku**

1. Přidat **HTTP Webhook** aktivační událost jako první krok v aplikaci logiky.
2. Vyplňte parametry pro přihlášení odběru webhooku a zrušit její volání.

   Tento krok používá stejný vzor jako [akce HTTP](connectors-native-http.md) formátu.

     ![HTTP Trigger](./media/connectors-native-webhook/using-trigger.png)

3. Přidejte alespoň jednu akci.
4. Klikněte na tlačítko **Uložit** publikovat aplikaci logiky. Tento krok volání koncového bodu přihlásit k odběru se adresa URL zpětného volání, které jsou potřeba k aktivaci tato aplikace logiky.
5. Vždy, když služba provede `HTTP POST` na adresu URL zpětného volání, aplikace logiky je vyvoláno a zahrnuje všechna data předaná do požadavku.

## <a name="use-the-webhook-action"></a>Pomocí akce webhooku

[ *Akce* ](connectors-overview.md) operace provádí pracovní postup definovaný v aplikaci logiky. Zaregistruje akce webhooku *adresu URL zpětného volání* služby a počká na adresu URL je volána před provedením obnovení. ["Odeslat E-mail se schválením"](connectors-create-api-office365-outlook.md) je příkladem konektor, který používá tento vzor. Tento model můžete rozšířit do jakékoli služby prostřednictvím akce webhooku. 

Tady je příklad, který ukazuje, jak vytvořit akce webhooku v návrháři aplikace logiky. Tento postup předpokládá, že jste již nasadili nebo mají přístup k rozhraní API, která následuje [odběru webhooku a odhlášení odběru používaným ve službě logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Přihlásit k odběru volání, když aplikace logiky provede akce webhooku. Odhlásit odběr při volání při spuštění je zrušena při čekání na odpověď, nebo před logiku aplikace vyprší časový limit.

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
* Znamená, že požadované pole.

| Zobrazovaný název | Název vlastnosti | Popis |
| --- | --- | --- |
| Přihlášení odběru metoda * |method |Metoda HTTP pro žádosti o předplatné |
| Přihlášení odběru URI * |identifikátor uri |Identifikátor URI HTTP pro žádosti o předplatné |
| Odhlásit odběr – metoda * |method |Metoda HTTP pro žádost o zrušení odběru |
| Odhlásit odběr URI * |identifikátor uri |Identifikátor URI HTTP pro žádost o zrušení odběru |
| Přihlášení odběru textu |hlavní část |Požadavku HTTP pro přihlášení k odběru |
| Přihlášení odběru záhlaví |hlavičky |Hlavičky požadavků HTTP pro přihlášení k odběru |
| Ověřování přihlášení odběru |Ověřování |Ověřování protokolu HTTP pro přihlášení k odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |
| Odhlásit odběr textu |hlavní část |Požadavku HTTP pro zrušení odběru |
| Odhlásit odběr záhlaví |hlavičky |Hlavičky požadavků HTTP pro zrušení odběru |
| Odhlásit odběr ověřování |Ověřování |Ověřování protokolu HTTP pro zrušení odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |

**Podrobnosti výstupu**

Požadavek Webhooku

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky požadavku Webhooku |
| Tělo |objekt |Objekt požadavku Webhooku |
| Stavový kód |int |Stavový kód žádosti o Webhooku |

## <a name="webhook-actions"></a>Akce Webhooku

| Akce | Popis |
| --- | --- |
| HTTP Webhook |Adresa URL zpětného volání službě, která může volat adresu URL obnovit podle potřeby kroku pracovního postupu odběru. |

### <a name="action-details"></a>Detaily akce

#### <a name="http-webhook"></a>HTTP Webhook

Adresa URL zpětného volání službě, která může volat adresu URL obnovit podle potřeby kroku pracovního postupu odběru.
* Znamená, že požadované pole.

| Zobrazovaný název | Název vlastnosti | Popis |
| --- | --- | --- |
| Přihlášení odběru metoda * |method |Metoda HTTP pro žádosti o předplatné |
| Přihlášení odběru URI * |identifikátor uri |Identifikátor URI HTTP pro žádosti o předplatné |
| Odhlásit odběr – metoda * |method |Metoda HTTP pro žádost o zrušení odběru |
| Odhlásit odběr URI * |identifikátor uri |Identifikátor URI HTTP pro žádost o zrušení odběru |
| Přihlášení odběru textu |hlavní část |Požadavku HTTP pro přihlášení k odběru |
| Přihlášení odběru záhlaví |hlavičky |Hlavičky požadavků HTTP pro přihlášení k odběru |
| Ověřování přihlášení odběru |Ověřování |Ověřování protokolu HTTP pro přihlášení k odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |
| Odhlásit odběr textu |hlavní část |Požadavku HTTP pro zrušení odběru |
| Odhlásit odběr záhlaví |hlavičky |Hlavičky požadavků HTTP pro zrušení odběru |
| Odhlásit odběr ověřování |Ověřování |Ověřování protokolu HTTP pro zrušení odběru. [Zobrazit konektor HTTP](connectors-native-http.md#authentication) podrobnosti |

**Podrobnosti výstupu**

Požadavek Webhooku

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky požadavku Webhooku |
| Tělo |objekt |Objekt požadavku Webhooku |
| Stavový kód |int |Stavový kód žádosti o Webhooku |

## <a name="next-steps"></a>Další postup

* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vyhledání dalších konektorů](apis-list.md)