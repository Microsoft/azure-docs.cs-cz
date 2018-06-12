---
title: Konektor Webhooku pro Azure Logic Apps | Microsoft Docs
description: Jak používat akce webhooku a aktivačních událostí k provádění akcí jako pole filtru z aplikace logiky
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 647dcebdd8d66ca49e14b51633e6a4a3ce6cb32b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296556"
---
# <a name="get-started-with-the-webhook-connector"></a>Začínáme s konektorem webhooku

Akce webhooku a aktivační události můžete spustit, pozastavit a obnovit toky k provedení těchto úloh:

* Aktivovat z [centra událostí Azure](https://github.com/logicappsio/EventHubAPI) při doručení položky
* Čekat na schválení před pokračováním pracovního postupu

Další informace o [vytvoření vlastní rozhraní API, které podporují webhook, jehož](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Použít aktivační události webhooku

A [ *aktivační událost* ](connectors-overview.md) je událost, která spustí pracovní postup aplikace logiky. Aktivační události webhooku je založený na událostech a nemá spoléhají na cyklické dotazování na nové položky. Jako [aktivační událost požadavku](connectors-native-reqres.md), aktivuje se v aplikaci logiky rychlých, který se stane událost. Zaregistruje aktivační události webhooku *adresu URL zpětné volání* pro služby a používá tuto adresu URL k aktivovat aplikaci logiky jako potřeby.

Tady je příklad, který ukazuje, jak nastavit aktivační procedury HTTP v Návrháři logiku aplikace. Postup předpokládá už mají nasazenou nebo přístupu k rozhraní API, které odpovídá [webhooku přihlášení k odběru a odhlášení vzor v aplikacích logiky](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Přihlásit k odběru volání, vždy, když aplikace logiky je uložit s novou webhooku nebo přechod ze zakázaného na povolený. Volání unsubscribe, provádí při aktivační události webhooku logiku aplikace je odebrat a uložit nebo přechod z povoleno na zakázáno.

**Chcete-li přidat aktivační události webhooku**

1. Přidat **Webhooku protokolu HTTP** aktivační událost jako první krok v aplikaci logiky.
2. Zadejte parametry pro webhook přihlášení k odběru a odhlášení volání.

   Tento krok používá se stejný vzor, jako [akce HTTP](connectors-native-http.md) formátu.

     ![Aktivace protokolu HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Přidejte alespoň jednu akci.
4. Klikněte na tlačítko **Uložit** k publikování aplikace logiky. Tento krok zavolá koncový bod přihlásit k odběru s adresou URL zpětné volání potřebné k aktivaci této logiky aplikace.
5. Vždy, když služba umožňuje `HTTP POST` na adresu URL zpětné volání, aplikaci logiky aktivuje a zahrnuje všechna data předaná do žádosti.

## <a name="use-the-webhook-action"></a>Použije se akce webhooku

[ *Akce* ](connectors-overview.md) se operace provádí v pracovním postupu definované v aplikaci logiky. Akce webhooku registruje *adresu URL zpětné volání* s služby a počká na adresu URL je volána před provedením obnovení. ["Odeslat E-mail schválení"](connectors-create-api-office365-outlook.md) je příkladem konektor, který následuje tohoto vzoru. Tento vzor můžete rozšířit do jakékoli služby prostřednictvím akce webhooku. 

Tady je příklad, který ukazuje, jak nastavit akce webhooku v Návrháři logiku aplikace. Tento postup předpokládá už mají nasazenou nebo přístupu k rozhraní API, které odpovídá [webhooku přihlášení k odběru a odhlášení způsobem používaným v aplikacích logiky](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Přihlásit k odběru volání, když aplikace logiky, provede akce webhooku. Volání unsubscribe, provádí při spuštění je zrušená při čekání na odpověď, nebo před logiku aplikace časového limitu.

**Chcete-li přidat akce webhooku**

1. Zvolte **nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte "webhooku" Najít **Webhooku protokolu HTTP** akce.

    ![Vyberte akci dotazu](./media/connectors-native-webhook/using-action-1.png)

3. Zadejte parametry pro webhook přihlášení k odběru a odhlášení volání

   Tento krok používá se stejný vzor, jako [akce HTTP](connectors-native-http.md) formátu.

     ![Dokončení dotazu akce](./media/connectors-native-webhook/using-action-2.png)
   
   V době běhu aplikace logiky zavolá koncový bod přihlásit k odběru po dosažení tohoto kroku.

4. Klikněte na tlačítko **Uložit** k publikování aplikace logiky.

## <a name="technical-details"></a>Technické podrobnosti

Zde jsou další informace o aktivační události a akce se tento webhook podporuje.

## <a name="webhook-triggers"></a>Aktivační události Webhooku

| Akce | Popis |
| --- | --- |
| HTTP Webhook |Přihlášení k odběru adresu URL zpětné volání pro službu, která můžete volat adresu URL aplikace logiky podle potřeby aktivovat. |

### <a name="trigger-details"></a>Podrobnosti o aktivační události

#### <a name="http-webhook"></a>HTTP Webhook

Přihlášení k odběru adresu URL zpětné volání pro službu, která můžete volat adresu URL aplikace logiky podle potřeby aktivovat.
* Znamená povinné pole.

| Zobrazovaný název | Název vlastnosti | Popis |
| --- | --- | --- |
| Přihlášení k odběru metoda * |method |Metoda HTTP pro žádost o přihlásit k odběru |
| Přihlášení k odběru URI * |identifikátor uri |Identifikátor URI HTTP má pro požadavek přihlásit k odběru |
| Odhlášení metoda * |method |Metoda HTTP pro žádosti o odhlášení odběru |
| Odhlášení URI * |identifikátor uri |Identifikátor URI HTTP pro žádosti o odhlášení odběru |
| Přihlášení k odběru textu |hlavní část |Požadavku HTTP pro přihlásit k odběru |
| Přihlášení k odběru hlavičky |hlavičky |Hlavičky požadavku HTTP pro přihlásit k odběru |
| Přihlášení k odběru ověřování |Ověřování |Ověřování protokolu HTTP pro přihlásit k odběru. [Najdete v části konektor HTTP](connectors-native-http.md#authentication) podrobnosti |
| Odhlášení textu |hlavní část |Požadavku HTTP pro zrušení odběru |
| Odhlášení hlavičky |hlavičky |Hlavičky požadavku HTTP pro zrušení odběru |
| Zrušit ověřování |Ověřování |Ověřování protokolu HTTP pro zrušení odběru. [Najdete v části konektor HTTP](connectors-native-http.md#authentication) podrobnosti |

**Podrobnosti o výstupu**

Žádost o Webhooku

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky žádosti Webhooku |
| Tělo |objekt |Objekt žádosti Webhooku |
| Stavový kód |celá čísla |Stavový kód žádosti o Webhooku |

## <a name="webhook-actions"></a>Akce Webhooku

| Akce | Popis |
| --- | --- |
| HTTP Webhook |Přihlášení k odběru adresu URL zpětné volání pro službu, která můžete volat adresu URL k obnovení pracovního postupu krok podle potřeby. |

### <a name="action-details"></a>Detaily akce

#### <a name="http-webhook"></a>HTTP Webhook

Přihlášení k odběru adresu URL zpětné volání pro službu, která můžete volat adresu URL k obnovení pracovního postupu krok podle potřeby.
* Znamená povinné pole.

| Zobrazovaný název | Název vlastnosti | Popis |
| --- | --- | --- |
| Přihlášení k odběru metoda * |method |Metoda HTTP pro žádost o přihlásit k odběru |
| Přihlášení k odběru URI * |identifikátor uri |Identifikátor URI HTTP má pro požadavek přihlásit k odběru |
| Odhlášení metoda * |method |Metoda HTTP pro žádosti o odhlášení odběru |
| Odhlášení URI * |identifikátor uri |Identifikátor URI HTTP pro žádosti o odhlášení odběru |
| Přihlášení k odběru textu |hlavní část |Požadavku HTTP pro přihlásit k odběru |
| Přihlášení k odběru hlavičky |hlavičky |Hlavičky požadavku HTTP pro přihlásit k odběru |
| Přihlášení k odběru ověřování |Ověřování |Ověřování protokolu HTTP pro přihlásit k odběru. [Najdete v části konektor HTTP](connectors-native-http.md#authentication) podrobnosti |
| Odhlášení textu |hlavní část |Požadavku HTTP pro zrušení odběru |
| Odhlášení hlavičky |hlavičky |Hlavičky požadavku HTTP pro zrušení odběru |
| Zrušit ověřování |Ověřování |Ověřování protokolu HTTP pro zrušení odběru. [Najdete v části konektor HTTP](connectors-native-http.md#authentication) podrobnosti |

**Podrobnosti o výstupu**

Žádost o Webhooku

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky žádosti Webhooku |
| Tělo |objekt |Objekt žádosti Webhooku |
| Stavový kód |celá čísla |Stavový kód žádosti o Webhooku |

## <a name="next-steps"></a>Další postup

* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vyhledání jiných konektorů](apis-list.md)