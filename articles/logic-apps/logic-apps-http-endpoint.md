---
title: Volání, triggery nebo vnořené pracovní postupy s koncovými body HTTP – Azure Logic Apps | Dokumentace Microsoftu
description: Nastavení koncových bodů HTTP k volání aktivační události, nebo vnořené pracovní postupy pro Azure Logic Apps
services: logic-apps
keywords: pracovní postupy, koncové body HTTP
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: 7920fee1bacf569ac41c36142fc68080b4de5780
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230484"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Volání triggeru, nebo vnořené pracovní postupy s koncovými body HTTP ve službě logic apps

Můžete nativně zpřístupňují synchronní koncových bodů HTTP jako aktivační události na logic apps, takže můžete aktivovat nebo volání aplikací logiky prostřednictvím adresy URL. Lze také vnořit pracovní postupy ve službě logic apps s použitím vzoru volatelné koncové body.

Pokud chcete vytvořit koncové body HTTP, můžete přidat tyto triggery tak, aby aplikace logiky může přijímat příchozí žádosti:

* [Požadavek](../connectors/connectors-native-reqres.md)

* [Webhook připojení rozhraní API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > I když tyto příklady používají **žádosti** aktivační událost, můžete použít některý z uvedených triggerů HTTP, a všechny zásady stejně jako platí i pro další typy triggerů.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Nastavení koncového bodu HTTP pro vaši aplikaci logiky

Vytvořit koncový bod HTTP, přidání triggeru, který může přijímat příchozí žádosti.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com "Azure Portal"). Přejděte do aplikace logiky a otevřete návrhář aplikace logiky.

2. Přidání triggeru, který umožňuje přijímat příchozí žádosti o aplikace logiky. Třeba přidat **žádosti** trigger aplikace logiky.

3.  V části **požádat o schéma JSON hlavní části**, můžete volitelně zadat schéma JSON pro datovou část (data), který očekáváte, že aktivační událost pro příjem.

    Toto schéma návrháře používá ke generování tokenů, které vaše aplikace logiky můžete využívat, analyzovat a předání dat z triggeru prostřednictvím pracovního postupu. 
    Další informace o [tokeny vygenerovat ze schémat JSON](#generated-tokens).

    V tomto příkladu zadejte schéma zobrazí v Návrháři:

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

    ![Přidání akce požadavku][1]

    > [!TIP]
    > 
    > Můžete vytvořit schéma pro ukázkovou datovou část JSON z nástroje, jako je [jsonschema.net](http://jsonschema.net/), nebo **požádat o** aktivační událost výběrem **k vygenerování schématu použít ukázkovou datovou část**. 
    > Zadejte vaši ukázkovou datovou část a zvolte **provádí**.

    Například tuto ukázkovou datovou část:

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

4.  Uložte svou aplikaci logiky. V části **HTTP POST na tuto adresu URL**, by měl nyní najdete na adresu URL pro generované zpětné volání, jako v tomto příkladu:

    ![Adresu URL zpětného volání generovaného koncový bod](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Tato adresa URL obsahuje klíč sdíleného přístupového podpisu (SAS) v parametrech dotazů, které se používají pro ověřování. 
    Můžete také získat adresu URL koncového bodu HTTP z vaší přehled aplikace logiky na webu Azure Portal. V části **historie aktivačních událostí**, vyberte trigger:

    ![Získat adresu URL koncového bodu HTTP z webu Azure portal][2]

    Nebo můžete získat adresu URL tak, že toto volání:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Změnit metodu HTTP pro aktivační událost

Ve výchozím nastavení **požadavek** očekává aktivační událost požadavek HTTP POST, ale můžete použít jinou metodu HTTP. 

> [!NOTE]
> Můžete zadat pouze jednu metodu typu.

1. Na vaše **žádosti** aktivovat, vyberte **zobrazit pokročilé možnosti**.

2. Otevřít **metoda** seznamu. V tomto příkladu vyberte **získat** , aby bylo později otestovat adresu URL vašeho koncového bodu protokolu HTTP.

    > [!NOTE]
    > Můžete vybrat jinou metodu HTTP, nebo zadat vlastní metodu pro aplikaci logiky.

    ![Změna metody HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Přijímají parametry přes adresu URL koncového bodu protokolu HTTP

Pokud chcete, aby vaše adresa URL koncového bodu HTTP pro příjem parametrů, přizpůsobte váš trigger relativní cestu.

1. Na vaše **žádosti** aktivovat, vyberte **zobrazit pokročilé možnosti**. 

2. V části **metoda**, zadejte metodu HTTP, který chcete používat vaši žádost. V tomto příkladu vyberte **získat** metodu, pokud jste tak již neučinili, aby mohl otestovat adresu URL vašeho koncového bodu protokolu HTTP.

      > [!NOTE]
      > Když zadáte relativní cesty pro trigger, musíte také explicitně zadat lze metodu HTTP pro trigger.

3. V části **relativní cesta**, zadejte relativní cestu pro parametr, který by měla přijímat adresu URL, třeba `customers/{customerID}`.

    ![Zadejte metodu HTTP a relativní cestu pro parametr](./media/logic-apps-http-endpoint/relativeurl.png)

4. Chcete-li použít parametr, přidejte **odpovědi** akce aplikace logiky. (Pod triggerem zvolte **nový krok** > **přidat akci** > **odpovědi**) 

5. Vaše odpověď **tělo**, patří token pro parametr, který jste zadali v váš trigger relativní cestu.

    Například chcete-li vrátit `Hello {customerID}`, aktualizovat vaše odpověď **tělo** s `Hello {customerID token}`. 
    V seznamu dynamického obsahu by se zobrazí a označovat `customerID` token pro výběr.

    ![Přidání parametru do těla odpovědi](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Vaše **tělo** by měl vypadat jako v tomto příkladu:

    ![Text odpovědi s parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Uložte svou aplikaci logiky. 

    Vaše adresa URL koncového bodu HTTP teď obsahuje relativní cestu, například: 

    HTTPS&#58;/ / prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. K otestování vašeho koncového bodu HTTP, zkopírujte a vložte adresu URL aktualizované do jiného okna prohlížeče, ale nahraďte `{customerID}` s `123456`, a stiskněte klávesu Enter.

    Váš prohlížeč by se zobrazit následující text: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokeny vygenerovat ze schémat JSON pro vaši aplikaci logiky

Když zadáte schématu JSON ve vaší **požádat o** aktivační událost, Návrhář aplikace logiky generuje tokeny pro vlastnosti v tomto schématu. Pak můžete tyto tokeny pro předávání dat prostřednictvím pracovního postupu aplikace logiky.

Například pokud chcete přidat `title` a `name` vlastnosti schématu JSON, jejich tokeny jsou teď k dispozici pro použití v dalších krocích pracovního postupu. 

Tady je úplný schématu JSON:

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

Pracovní postupy lze vnořit do aplikace logiky tak, že přidáte jiných funkcí logic apps, které můžou přijímat požadavky. Chcete-li zahrnout tyto aplikace logiky, přidejte **Azure Logic Apps – zvolte pracovní postup Logic Apps** akce na váš trigger. Pak můžete vybrat z aplikací logiky nárok.

![Přidat jiné aplikace logiky](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Volání nebo vyvolání aplikace logiky prostřednictvím koncových bodů HTTP

Po vytvoření koncový bod HTTP, můžete aktivovat svou aplikaci logiky přes `POST` metodu pro úplnou adresu URL. Logika aplikace mají integrovanou podporu pro přímý přístup koncových bodů.

> [!NOTE] 
> Chcete-li aplikaci logiky spustit ručně kdykoli, na panelu nástrojů návrháře aplikace logiky nebo zobrazení kódu aplikace logiky zvolte **spustit**.

## <a name="reference-content-from-an-incoming-request"></a>Referenční obsah z příchozího požadavku

Pokud je typ obsahu `application/json`, vlastnosti můžete odkazovat z příchozího požadavku. Obsah, jinak je považován za jednu jednotku binární, kterou můžete předat pro jiná rozhraní API. Chcete-li tento obsah uvnitř pracovního postupu, je nutné převést tento obsah. Například pokud předáte `application/xml` obsahu, můžete použít `@xpath()` extrakci XPath nebo `@json()` převodu XML do formátu JSON. Další informace o [práce pomocí typů obsahu](../logic-apps/logic-apps-content-type.md).

Chcete-li získat výstup z příchozího požadavku, můžete použít `@triggerOutputs()` funkce. Výstup může vypadat jako v tomto příkladu:

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

Pro přístup `body` vlastnost konkrétně byste měli použít `@triggerBody()` zástupce. 

## <a name="respond-to-requests"></a>Reagovat na požadavky

Můžete chtít reagovat na určité požadavky, které spouští aplikace logiky tak, že obsah vrací řízení volajícímu. Chcete-li vytvořit stavový kód, záhlaví a text na vaši odpověď, můžete použít **odpovědi** akce. Tato akce může vyskytovat kdekoli v aplikaci logiky, nikoli pouze na konci pracovního postupu.

> [!NOTE] 
> Pokud vaše aplikace logiky neobsahuje **odpovědi**, koncový bod HTTP reaguje *okamžitě* s **202 přijato** stav. Pro původní požadavek na získání odpovědi také všechny kroky potřebné k odpovědi musí dokončit v rámci [časový limit požadavku](./logic-apps-limits-and-config.md) Pokud zavoláte pracovního postupu jako vnořenou aplikaci logiky. V případě žádná odpověď v rámci tohoto limitu příchozího požadavku, vyprší časový limit a obdrží odpověď HTTP **408 časového limitu klienta**. Nadřazená aplikace logiky pro vnořené logic apps, dál čekat na odpověď až do dokončení, bez ohledu na to, jak dlouho je povinný.

### <a name="construct-the-response"></a>Vytvoření odpovědi

V těle odpovědi může obsahovat více než jedno záhlaví a libovolný typ obsahu. V odpovědi příklad určuje hlavičku, že odpověď obsahuje typ obsahu `application/json`. a text obsahuje `title` a `name`závislosti na schématu JSON dříve aktualizované **žádosti** aktivační události.

![Akce odpovědi HTTP][3]

Odpovědi mít tyto vlastnosti:

| Vlastnost | Popis |
| --- | --- |
| statusCode |Určuje kód stavu HTTP pro zpracování příchozího požadavku. Tento kód může být libovolný platný stavový kód, který začíná 2xx, 4xx a 5xx. Stavové kódy 3xx však nejsou povolené. |
| Záhlaví |Definuje libovolný počet hlaviček, které chcete zahrnout do odpovědi. |
| hlavní část |Určuje objekt textu, který může být řetězec, objekt JSON nebo dokonce binární obsah odkazovaný z předchozího kroku. |

Tady je schéma JSON vypadá nyní **odpovědi** akce:

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
> Chcete-li zobrazit úplnou definici JSON pro vaši aplikaci logiky v návrháři aplikace logiky, zvolte **zobrazení kódu**.

## <a name="q--a"></a>Dotazy a odpovědi

#### <a name="q-what-about-url-security"></a>Otázka: co zabezpečení adresy URL?

Odpověď: azure bezpečně generuje logic app zpětného volání adresy URL pomocí sdílený přístupový podpis (SAS). Tento podpis prochází jako parametr dotazu a musí ověřit dříve, než můžete aktivovat svou aplikaci logiky. Azure generuje podpis pomocí jedinečnou kombinaci tajný klíč aplikace logiky, název aktivační události a operace, která se provádí. Takže pokud někdo získá přístup k logiky tajný klíč aplikace, že nelze generovat platný podpis.

   > [!IMPORTANT]
   > Pro produkci a zabezpečené systémy důrazně doporučujeme před voláním vaší aplikace logiky přímo z prohlížeče, protože:
   > 
   > * Sdílený přístupový klíč se zobrazí v adrese URL.
   > * Mezi zákazníky aplikace logiky nemůže spravovat zásady zabezpečení obsahu z důvodu sdílené domény.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Dotaz: lze nakonfigurovat další koncové body HTTP?

Odpověď: Ano, koncové body HTTP podporují pokročilejší konfigurace prostřednictvím [ **API Management**](../api-management/api-management-key-concepts.md). Tato služba nabízí taky možnost si můžete konzistentně spravovat všechna svoje rozhraní API, včetně aplikací logiky, nastavte si vlastní názvy domén, použijte další metody ověřování a informace, například:

* [Metoda žádosti o změnu](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Změnit segmenty adres URL požadavku](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Nastavení rozhraní API správy domén ve [webu Azure portal](https://portal.azure.com/ "webu Azure portal")
* Nastavte zásady pro kontrolu pro základní ověřování

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Otázka: co se změnilo při migraci schématu z 1. prosince 2014 ve verzi preview?

Odpověď: tady je přehled o tyto změny:

| 1. prosince 2014 ve verzi preview | 1. června 2016 |
| --- | --- |
| Klikněte na tlačítko **naslouchací proces protokolu HTTP** aplikace API |Klikněte na tlačítko **ruční aktivační události** (je vyžadována aplikace bez rozhraní API) |
| Naslouchací proces protokolu HTTP nastavení "*automaticky odešle odpověď*" |Buď patří **odpovědi** akce nebo není v definici pracovního postupu |
| Konfigurace ověřování Basic nebo OAuth |přes správu rozhraní API |
| Konfigurace metody HTTP |V části **zobrazit pokročilé možnosti**, zvolte metodu HTTP |
| Konfigurace relativní cesta |V části **zobrazit pokročilé možnosti**, zadejte relativní cestu |
| Referenční obsah příchozí prostřednictvím `@triggerOutputs().body.Content` |Odkaz `@triggerOutputs().body` |
| **Poslat odpověď HTTP** akce na naslouchací proces protokolu HTTP |Klikněte na tlačítko **odpovědět na požadavek HTTP** (je vyžadována aplikace bez rozhraní API) |

## <a name="get-help"></a>Podpora

Klást otázky, odpovídat na ně a poučit se ze zkušeností jiných uživatelů Azure Logic Apps můžete ve [fóru Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Pokud chcete pomoci při vylepšování Azure Logic Apps a konektorů, hlasujte nebo zanechte své nápady na [webu zpětné vazby uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Vytváření definic aplikací logiky](./logic-apps-author-definitions.md)
* [Zpracování chyb a výjimek](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png