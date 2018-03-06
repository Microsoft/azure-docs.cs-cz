---
title: "Volání, aktivační události nebo vnořit pracovních s koncovými body HTTP - Azure Logic Apps | Microsoft Docs"
description: "Nastavit koncové body HTTP zavolat, aktivaci nebo vnořit pracovní postupy pro Azure Logic Apps"
services: logic-apps
keywords: "pracovní postupy, koncových bodů protokolu HTTP"
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: de4f4ee086fbf3799fcac1f1b008d9237b5e7a09
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Volání, aktivaci nebo vnořit pracovních s koncovými body HTTP v aplikacích logiky

Synchronní koncové body HTTP můžou nativně zpřístupnit jako triggery pro aplikace logiky, mohli aktivovat nebo volání aplikace logiky prostřednictvím adresy URL. Pracovní postupy lze také vnořit ve vašich logic apps pomocí vzor s koncových bodů.

Pokud chcete vytvořit koncových bodů protokolu HTTP, můžete přidat tyto triggery, aby aplikace logiky můžete přijímat příchozí požadavky:

* [Požadavek](../connectors/connectors-native-reqres.md)

* [Rozhraní API připojení Webhooku](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > I když tyto příklady používají **požadavku** aktivační událost, můžete použít některou z uvedených aktivačních událostí protokolu HTTP, a všechny zásady stejně jako se vztahují na jiné typy aktivační události.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Nastavit koncový bod HTTP pro svou aplikaci logiky

Chcete-li vytvořit koncový bod protokolu HTTP, přidejte aktivační událost, která může přijímat příchozí požadavky.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com "Azure Portal"). Přejděte do aplikace logiky a otevřete návrhář aplikace logiky.

2. Přidejte aktivační událost, která umožní aplikaci logiky přijímat příchozí požadavky. Například přidat **požadavku** aktivační svou aplikaci logiky.

3.  V části **požadavku schématu JSON textu**, můžete volitelně zadat schématu JSON pro datová část (data), které očekáváte, že aktivační událost pro příjem.

    Toto schéma návrháře používá ke generování tokenů, které aplikace logiky můžete využívat, analyzovat a předání dat z aktivace prostřednictvím pracovního postupu. 
    Další informace o [tokeny vygenerovat z JSON schémata](#generated-tokens).

    V tomto příkladu zadejte schéma v Návrháři zobrazeny:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Přidat akci požadavku][1]

    > [!TIP]
    > 
    > Schéma pro ukázku datové části JSON můžete vygenerovat z nástroje, jako je [jsonschema.net](http://jsonschema.net/), nebo v **požadavku** aktivační události tak, že zvolíte **datová část ukázky použít ke generování schématu**. 
    > Zadejte vaše datová část ukázky a vyberte **provádí**.

    Například tato ukázková datová část:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    generuje toto schéma:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Uložte svou aplikaci logiky. V části **HTTP POST na tuto adresu URL**, nyní byste měli najít adresu URL generovaného zpětného volání, jako tento ukázkový:

    ![Adresa URL generovaného zpětného volání pro koncový bod](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Tato adresa URL obsahuje klíč sdíleného přístupového podpisu (SAS) v parametry dotazu, které se používají pro ověřování. 
    Můžete také získat adresu URL koncového bodu protokolu HTTP z vaší přehled aplikace logiky na portálu Azure. V části **historie aktivační událost**, vyberte aktivační událost:

    ![Získat adresu URL koncového bodu protokolu HTTP z portálu Azure][2]

    Nebo můžete získat adresu URL tím, že toto volání:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Změnit metodu HTTP aktivační události

Ve výchozím nastavení **požadavku** aktivační událost očekává požadavek HTTP POST, ale můžete použít jinou metodu HTTP. 

> [!NOTE]
> Můžete zadat typ pouze jednu metodu.

1. Na vaše **požadavku** aktivovat, zvolte **zobrazit rozšířené možnosti**.

2. Otevřete **metoda** seznamu. V tomto příkladu vyberte **získat** , aby bylo později otestovat adresu URL vašeho koncového bodu protokolu HTTP.

    > [!NOTE]
    > Můžete vybrat jinou metodu HTTP, nebo zadat vlastní metodu pro aplikaci logiky.

    ![Změnit metodu HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Přijměte parametry prostřednictvím vaše adresa URL koncového bodu protokolu HTTP

Pokud chcete, aby vaše adresa URL koncového bodu protokolu HTTP tak, aby přijímal parametry, přizpůsobit vaší aktivační událost relativní cestu.

1. Na vaše **požadavku** aktivovat, zvolte **zobrazit rozšířené možnosti**. 

2. V části **metoda**, zadejte metodu protokolu HTTP, který chcete žádost o použít. V tomto příkladu vyberte **získat** metodu, pokud jste tak ještě neučinili, tak, aby bylo možné otestovat adresu URL vašeho koncového bodu protokolu HTTP.

      > [!NOTE]
      > Když zadáte relativní cesta aktivační události, musíte zadat metodu HTTP také explicitně aktivační události.

3. V části **relativní cestu**, zadejte relativní cestu pro parametr, který by měl přijmout adresu URL, například `customers/{customerID}`.

    ![Zadejte metodu HTTP a relativní cestu pro parametr](./media/logic-apps-http-endpoint/relativeurl.png)

4. Chcete-li použít parametr, přidejte **odpovědi** akce do aplikace logiky. (V části aktivační událost, zvolte **nový krok** > **přidat akci** > **odpovědi**) 

5. Do odpovědi **textu**, zahrnout token pro parametr, který jste zadali v relativní cestě vaší aktivační události.

    Chcete-li například vrátit `Hello {customerID}`, aktualizovat vaši odpověď **textu** s `Hello {customerID token}`. 
    Seznamu dynamického obsahu by měl zobrazit a zobrazit `customerID` tokenu jste ji vybrat.

    ![Přidání parametrů do odpovědi](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Vaše **textu** by měl vypadat jako tento příklad:

    ![Text odpovědi s parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Uložte svou aplikaci logiky. 

    Vaše adresa URL koncového bodu protokolu HTTP nyní zahrnuje relativní cesta, například: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Pokud chcete otestovat váš koncový bod protokolu HTTP, zkopírujte a vložte adresu URL aktualizované do jiného okna prohlížeče, ale nahraďte `{customerID}` s `123456`, a stiskněte klávesu Enter.

    Váš prohlížeč by měl zobrazit tento text: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokeny vygenerovat z JSON schémata pro svou aplikaci logiky

Když zadáte schématu JSON ve vaší **požadavku** aktivační událost, návrháře logiku aplikace generuje tokeny pro vlastnosti v tomto schématu. Pak můžete tyto tokeny pro předávání dat prostřednictvím pracovní postup aplikace logiky.

Pro tento příklad, pokud přidáte `title` a `name` vlastnosti vašeho schématu JSON, jejich tokeny jsou nyní k dispozici pro použití v dalších krocích pracovního postupu. 

Tady je kompletní schématu JSON:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Vytvoření vnořené pracovní postupy pro logic apps

Pracovní postupy lze vnořit ve vaší aplikaci logiky můžete přidat další logiku aplikace, které může přijímat požadavky. Pokud chcete tyto aplikace logiky, přidejte **Azure Logic Apps – zvolte pracovní postup Logic Apps** akce aktivační událost. Pak můžete vybrat z oprávněné logiku aplikace.

![Přidat další aplikace logiky](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Volání nebo aktivovat aplikace logiky prostřednictvím koncových bodů protokolu HTTP

Jakmile vytvoříte koncový bod protokolu HTTP, můžete aktivovat svou aplikaci logiky prostřednictvím `POST` metodu pro úplnou adresu URL. Logiku aplikace mají integrovanou podporu pro koncové body přímý přístup.

> [!NOTE] 
> Chcete-li ručně spustit kdykoli na panelu nástrojů Návrhář aplikace na základě logiky nebo zobrazení kódu aplikace logiky aplikace logiky zvolte **spustit**.

## <a name="reference-content-from-an-incoming-request"></a>Odkaz na obsah z příchozího požadavku

Pokud je typ obsahu je `application/json`, vlastnosti, můžete odkazovat z příchozího požadavku. Obsah, jinak je považována za binární jedné jednotky, které můžete předat pro jiná rozhraní API. Chcete-li tento obsah uvnitř pracovního postupu, je nutné převést obsah. Například pokud předáte `application/xml` obsahu, můžete použít `@xpath()` pro extrakci XPath nebo `@json()` pro převod XML do formátu JSON. Další informace o [práce s typy obsahu](../logic-apps/logic-apps-content-type.md).

Chcete-li získat výstup z příchozí žádosti, můžete použít `@triggerOutputs()` funkce. Výstup může vypadat jako tento ukázkový:

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Pro přístup k `body` vlastnost můžete konkrétně použít `@triggerBody()` zástupce. 

## <a name="respond-to-requests"></a>Reaguje na požadavky.

Můžete chtít reagovat na určité požadavky, které začínají aplikace logiky vrácením obsah volajícímu. Chcete-li vytvořit stavový kód, záhlaví a text na vaši odpověď, můžete použít **odpovědi** akce. Tato akce může vyskytovat kdekoli v aplikaci logiky, nikoli pouze na konci pracovního postupu.

> [!NOTE] 
> Pokud svou aplikaci logiky neobsahuje **odpovědi**, odpoví koncový bod HTTP *okamžitě* s **202 platných** stavu. Pro původní žádost se získat odpověď, také všechny kroky potřebné pro odpověď musí dokončit v rámci [časový limit požadavku](./logic-apps-limits-and-config.md) Pokud volat pracovní postup jako vnořené logiku aplikace. V případě žádná odpověď v rámci tohoto limitu příchozího požadavku vyprší časový limit a obdrží odpověď HTTP **408 časový limit klienta**. Pro vnořené logic apps pokračuje aplikace logiky nadřazené čekat na odpověď, dokud nebude dokončena, bez ohledu na to, jak dlouho se vyžaduje.

### <a name="construct-the-response"></a>Vytvoření odpovědi

V textu odpovědi může obsahovat více než jedno záhlaví a libovolný typ obsahu. V odpovědi příklad hlavičku Určuje, že odpověď má typ obsahu `application/json`. a text obsahuje `title` a `name`, podle schématu JSON aktualizované dříve **požadavku** aktivační události.

![Akce odpovědi HTTP][3]

Odpovědi mít tyto vlastnosti:

| Vlastnost | Popis |
| --- | --- |
| statusCode |Určuje kód stavu HTTP pro reagovat na příchozí požadavek. Tento kód může být jakýkoli platný stavový kód, který začíná 2xx, 4xx nebo 5xx. Stavové kódy 3xx však nejsou povoleny. |
| hlavičky |Definuje libovolný počet hlaviček, které chcete zahrnout do odpovědi. |
| hlavní část |Určuje objekt textu, který může být řetězec, objekt JSON nebo i binární obsah na něj odkazovat z předchozího kroku. |

Tady je co schématu JSON teď pro vypadá **odpovědi** akce:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Chcete-li zobrazit úplnou definici JSON pro svou aplikaci logiky na návrháře aplikace logiky, zvolte **Code zobrazení**.

## <a name="q--a"></a>Dotazy a odpovědi

#### <a name="q-what-about-url-security"></a>Otázka: co o adresu URL zabezpečení?

Odpověď: azure bezpečně generuje logiku aplikace zpětného volání adresy URL pomocí sdíleného přístupového podpisu (SAS). Tento podpis projdou jako parametr dotazu a musí být ověřené před svou aplikaci logiky můžete aktivovat. Podpis jedinečná kombinace tajný klíč na aplikaci logiky, název aktivační události a operace, které se provádí pomocí vygeneruje Azure. Pokud má uživatel přístup ke klíči tajný logiku aplikace, se proto nelze generovat platný podpis.

   > [!IMPORTANT]
   > Pro produkční a zabezpečených systémů důrazně doporučujeme před volání aplikace logiky přímo z prohlížeče, protože:
   > 
   > * Sdílený přístupový klíč se zobrazí v adrese URL.
   > * Nelze spravovat zabezpečené obsahu zásady z důvodu sdílených domén přes aplikaci logiky odběratele.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Otázka: je možné nakonfigurovat další koncové body HTTP?

Odpověď: Ano koncových bodů protokolu HTTP podporují pokročilejší konfigurace prostřednictvím [ **API Management**](../api-management/api-management-key-concepts.md). Tato služba také nabízí možnost můžete konzistentně spravovat všechna vaše rozhraní API, včetně aplikace logiky, nastavit vlastní názvy domén, použít další metody ověřování a informace, například:

* [Změnit metodu požadavku](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Segmenty adres URL žádosti o změnu](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Nastavení domény API Management do [portál Azure](https://portal.azure.com/ "portálu Azure")
* Nastavte zásady pro kontrolu pro základní ověřování

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Otázka: co změní schéma migraci z verze preview 1 prosince 2014?

Odpověď: Zde je souhrn o tyto změny:

| Preview 1 prosince 2014 | 1. června 2016 |
| --- | --- |
| Klikněte na tlačítko **naslouchací proces protokolu HTTP** aplikace API |Klikněte na tlačítko **ruční aktivační událost** (žádná aplikace API povinné) |
| Naslouchací proces protokolu HTTP nastavení "*automaticky odešle odpověď*" |Buď zahrnout **odpovědi** akce nebo není v definici pracovního postupu |
| Konfigurace ověřování Basic nebo OAuth |přes správu rozhraní API |
| Konfigurace metody HTTP |V části **zobrazit rozšířené možnosti**, zvolte metodu HTTP |
| Nakonfigurujte relativní cestu |V části **zobrazit rozšířené možnosti**, přidejte relativní cestu |
| Referenční příchozí textu prostřednictvím `@triggerOutputs().body.Content` |Referenční dokumentace prostřednictvím `@triggerOutputs().body` |
| **Odeslání odpovědi HTTP** akce naslouchací proces protokolu HTTP |Klikněte na tlačítko **odpovědět na požadavek HTTP** (žádná aplikace API povinné) |

## <a name="get-help"></a>Podpora

Klást otázky, odpovídat na ně a poučit se ze zkušeností jiných uživatelů Azure Logic Apps můžete ve [fóru Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Pokud chcete pomoci při vylepšování Azure Logic Apps a konektorů, hlasujte nebo zanechte své nápady na [webu zpětné vazby uživatelů Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Vytváření definic aplikací logiky](./logic-apps-author-definitions.md)
* [Zpracování chyb a výjimek](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png