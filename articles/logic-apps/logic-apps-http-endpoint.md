---
title: Volání, triggery nebo vnořování Logic Apps – Azure Logic Apps
description: Nastavte koncové body HTTP na volání, triggery nebo vnořené pracovní postupy aplikace logiky v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: 6e5a8eda3891b3b356e0cbd7b6d2e22e4a70c278
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799720"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Volání, triggery nebo vnořování aplikací logiky pomocí koncových bodů HTTP v Azure Logic Apps

Můžete nativně vystavovat synchronní koncové body HTTP jako triggery v aplikacích logiky, abyste mohli aktivovat nebo volat aplikace logiky prostřednictvím adresy URL. Pracovní postupy můžete ve svých aplikacích logiky vnořovat také pomocí vzoru koncových bodů, které lze volat.

K vytvoření koncových bodů HTTP můžete přidat tyto triggery, aby aplikace logiky mohly přijímat příchozí požadavky:

* [Požadavek](../connectors/connectors-native-reqres.md)

* [Webhook připojení rozhraní API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > I když tyto příklady používají aktivační událost **žádosti** , můžete použít kteroukoli z uvedených triggerů založených na žádostech a všechny zásady se stejným způsobem vztahují na ostatní typy triggerů.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Nastavení koncového bodu HTTP pro aplikaci logiky

Pokud chcete vytvořit koncový bod HTTP, přidejte Trigger, který může přijímat příchozí požadavky.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com "Portál Azure"). Přejít do vaší aplikace logiky a otevřít návrháře aplikace logiky

1. Přidejte Trigger, který umožní vaší aplikaci logiky přijímat příchozí požadavky. Přidejte například Trigger **Request** do aplikace logiky.

1. V části **schéma JSON textu žádosti**můžete volitelně zadat schéma JSON pro datovou část (data), které očekáváte, že aktivační událost obdrží.

   Návrhář používá toto schéma pro generování tokenů, které může vaše aplikace logiky použít ke zpracování, analýze a předávání dat z triggeru prostřednictvím pracovního postupu. Přečtěte si další informace o [tokenech generovaných ze schémat JSON](#generated-tokens).

   V tomto příkladu zadejte toto schéma, jak je znázorněno v Návrháři:

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

   ![Přidat akci žádosti](./media/logic-apps-http-endpoint/manualtrigger.png)

   > [!TIP]
   >
   > Můžete vygenerovat schéma pro ukázkovou datovou část JSON z nástroje, jako je [jsonschema.NET](https://jsonschema.net/) , nebo v triggeru **žádosti** výběrem možnosti **použít ukázkovou datovou část k vygenerování schématu**. Zadejte svou ukázkovou datovou část a klikněte na **Hotovo**.

   Například Tato ukázková datová část:

   ```json
   {
      "address": "21 2nd Street, New York, New York"
   }
   ```

   vygeneruje toto schéma:

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

1. Uložte svou aplikaci logiky. V části **http post na tuto adresu URL**byste teď měli najít VYGENEROVANOU adresu URL zpětného volání, jako je například:

   ![Vygenerovaná adresa URL zpětného volání pro koncový bod](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Tato adresa URL obsahuje klíč sdíleného přístupového podpisu (SAS) v parametrech dotazu, který se používá pro ověřování. Adresu URL koncového bodu HTTP můžete také získat z přehledu aplikace logiky v Azure Portal. V části **Historie aktivační události**vyberte aktivační událost:

   ![Získat adresu URL koncového bodu HTTP z Azure Portal](./media/logic-apps-http-endpoint/manualtriggerurl.png)

   Můžete také získat adresu URL tím, že zadáte toto volání:

    ```http
    POST https://management.azure.com/{logic-app-resource-ID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Změna metody HTTP triggeru

Ve výchozím nastavení očekává **aktivační událost požadavek HTTP** post, ale můžete použít jinou metodu HTTP.

> [!NOTE]
> Můžete zadat jenom jeden typ metody.

1. Na triggeru **žádosti** vyberte **Zobrazit upřesňující možnosti**.

2. Otevřete seznam **metod** . V tomto příkladu vyberte **získat** , abyste mohli otestovat adresu URL koncového bodu http později.

   > [!NOTE]
   > Můžete vybrat jakoukoli jinou metodu HTTP nebo zadat vlastní metodu pro vlastní aplikaci logiky.

   ![Změnit metodu HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Přijmout parametry prostřednictvím adresy URL koncového bodu HTTP

Pokud chcete, aby adresa URL koncového bodu HTTP přijímala parametry, přizpůsobte si relativní cestu triggeru.

1. Na triggeru **žádosti** vyberte **Zobrazit upřesňující možnosti**. 

2. V části **Metoda**zadejte metodu HTTP, kterou požadujete, aby vaše žádost používala. V tomto příkladu vyberte metodu **Get** , pokud jste to ještě neudělali, abyste mohli otestovat adresu URL koncového bodu http.

   > [!NOTE]
   > Když zadáte relativní cestu k triggeru, musíte explicitně zadat metodu HTTP triggeru.

3. V části **relativní cesta**zadejte relativní cestu k parametru, který by měla adresa URL přijmout, například `customers/{customerID}`.

   ![Zadejte metodu HTTP a relativní cestu pro parametr](./media/logic-apps-http-endpoint/relativeurl.png)

4. Pokud chcete použít parametr, přidejte do aplikace logiky akci **odpovědi** . (V rámci triggeru vyberte **Nový krok**  > **přidat akci**  > **odpověď**) 

5. Do **těla**odpovědi přidejte token pro parametr, který jste zadali v relativní cestě triggeru.

   Pokud například chcete vrátit `Hello {customerID}`, aktualizujte **tělo** odpovědi pomocí `Hello {customerID token}`. Seznam dynamického obsahu by se měl zobrazit a zobrazit `customerID` token, který můžete vybrat.

   ![Přidat parametr do textu odpovědi](./media/logic-apps-http-endpoint/relativeurlresponse.png)

   Váš **text** by měl vypadat jako v tomto příkladu:

   ![Tělo odpovědi s parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Uložte svou aplikaci logiky. 

    Adresa URL koncového bodu HTTP teď obsahuje relativní cestu, například: 

    ```http
    https://prod-00.southcentralus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/customers/{customerID}...
    ```

7. Chcete-li otestovat koncový bod HTTP, zkopírujte a vložte aktualizovanou adresu URL do jiného okna prohlížeče, ale `{customerID}` nahraďte `123456` a stiskněte klávesu ENTER.

   V prohlížeči by se měl zobrazit tento text: `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokeny generované ze schémat JSON pro aplikaci logiky

Když v triggeru **požadavku** zadáte schéma JSON, návrhář aplikace logiky vygeneruje tokeny pro vlastnosti v tomto schématu. Tyto tokeny pak můžete použít k předávání dat prostřednictvím pracovního postupu aplikace logiky.

V tomto příkladu Pokud přidáte `title` a `name` vlastnosti do schématu JSON, jejich tokeny jsou teď dostupné pro použití v pozdějších krocích pracovního postupu. 

Toto je kompletní schéma JSON:

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

## <a name="create-nested-workflows-for-logic-apps"></a>Vytváření vnořených pracovních postupů pro Logic Apps

Pracovní postupy můžete v aplikaci logiky vnořovat přidáním dalších aplikací logiky, které můžou přijímat požadavky. Pokud chcete zahrnout tyto aplikace logiky, přidejte **Azure Logic Apps – vyberte akci pracovního postupu Logic Apps** pro aktivační událost. Pak můžete vybrat z oprávněných aplikací logiky.

![Přidat další aplikaci logiky](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Volání nebo spuštění Logic Apps prostřednictvím koncových bodů HTTP

Po vytvoření koncového bodu HTTP můžete aktivovat aplikaci logiky prostřednictvím `POST` metody na úplnou adresu URL. Logic Apps mají integrovanou podporu koncových bodů s přímým přístupem.

> [!NOTE] 
> Pokud chcete aplikaci logiky kdykoli spustit ručně, klikněte v návrháři aplikace logiky nebo na panelu nástrojů zobrazení kódu aplikace logiky na **Spustit**.

## <a name="reference-content-from-an-incoming-request"></a>Odkazování na obsah z příchozího požadavku

Pokud je typ obsahu `application/json`, můžete odkazovat na vlastnosti z příchozího požadavku. V opačném případě se obsah považuje za jednu binární jednotku, kterou můžete předat jiným rozhraním API. Chcete-li odkazovat na tento obsah v rámci pracovního postupu, je nutné tento obsah převést. Například Pokud předáte `application/xml` obsah, můžete použít `@xpath()` pro extrakci XPath nebo `@json()` pro převod XML na formát JSON. Přečtěte si informace o [práci s typy obsahu](../logic-apps/logic-apps-content-type.md).

Chcete-li získat výstup z příchozího požadavku, můžete použít funkci `@triggerOutputs()`. Výstup může vypadat jako v tomto příkladu:

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

Pokud chcete získat přístup k vlastnosti `body` specificky, můžete použít zástupce `@triggerBody()`.

## <a name="respond-to-requests"></a>Reakce na žádosti

Je možné, že budete chtít reagovat na určité požadavky, které spustí aplikaci logiky vrácením obsahu volajícímu. Chcete-li vytvořit stavový kód, hlavičku a text pro odpověď, můžete použít akci **reakce** . Tato akce se může objevit kdekoli ve vaší aplikaci logiky, ne jen na konci pracovního postupu.

> [!NOTE] 
> Pokud vaše aplikace logiky neobsahuje **odpověď**, koncový bod http *okamžitě* odpoví stavem **přijato 202** . Pro původní požadavek na získání odpovědi také musí být všechny kroky požadované pro odpověď dokončeny v rámci [časového limitu požadavku](./logic-apps-limits-and-config.md) , pokud pracovní postup nebudete volat jako vnořenou aplikaci logiky. Pokud v rámci tohoto limitu nedojde k žádné odezvě, vyprší časový limit příchozího požadavku a obdrží **časový limit klienta**HTTP Response 408. Pro vnořené aplikace logiky pokračuje nadřazená aplikace logiky na odpověď až do dokončení, bez ohledu na to, kolik času je potřeba.

### <a name="construct-the-response"></a>Sestavit odpověď

V těle odpovědi můžete zahrnout více než jednu hlavičku a jakýkoliv typ obsahu. V příkladu odpovědi záhlaví určuje, že odpověď má typ obsahu `application/json`. a tělo obsahuje `title` a `name` na základě schématu JSON aktualizovaného dříve pro aktivační událost **žádosti** .

![Akce odpovědi HTTP](./media/logic-apps-http-endpoint/response.png)

Odpovědi mají tyto vlastnosti:

| Vlastnost | Popis |
| --- | --- |
| statusCode |Určuje stavový kód protokolu HTTP pro odpověď na příchozí požadavek. Tento kód může být libovolný platný stavový kód, který začíná na 2xx, 4xx nebo 5xx. Nicméně stavové kódy 3xx nejsou povoleny. |
| Záhlaví |Definuje libovolný počet hlaviček, které se mají zahrnout do odpovědi. |
| těles |Určuje objekt textu, který může být řetězec, objekt JSON nebo i binární obsah, na který odkazuje předchozí krok. |

Tady je schéma JSON, které vypadá teď jako akce **odpovědi** :

``` json
"Response": {
   "type": "Response",
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
   "runAfter": {}
}
```

> [!TIP]
> Chcete-li zobrazit úplnou definici JSON pro vaši aplikaci logiky, v návrháři aplikace logiky vyberte možnost **zobrazení kódu**.

## <a name="q--a"></a>Dotazy a odpovědi

#### <a name="q-what-about-url-security"></a>Otázka: co je zabezpečení adresy URL?

Odpověď: Azure bezpečně generuje adresy URL zpětného volání aplikace logiky pomocí sdíleného přístupového podpisu (SAS). Tento podpis projde jako parametr dotazu a musí se ověřit předtím, než se aplikace logiky může aktivovat. Azure vygeneruje signaturu pomocí jedinečné kombinace tajného klíče na aplikaci logiky, názvu triggeru a prováděné operace. Takže pokud nikdo nemá přístup k klíči tajné aplikace logiky, nemůže vygenerovat platný podpis.

   > [!IMPORTANT]
   > Pro produkční a zabezpečené systémy důrazně doporučujeme před voláním aplikace logiky přímo z prohlížeče, protože:
   > 
   > * V adrese URL se zobrazí sdílený přístupový klíč.
   > * Nemůžete spravovat zásady zabezpečeného obsahu kvůli sdíleným doménám napříč zákazníky logiky aplikace.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Otázka: Můžu dál nakonfigurovat koncové body HTTP?

Odpověď: Ano, koncové body HTTP podporují pokročilejší konfiguraci prostřednictvím [Azure API Management](../api-management/api-management-key-concepts.md). Tato služba také nabízí možnost konzistentně spravovat všechna vaše rozhraní API, včetně aplikací logiky, nastavení vlastních názvů domén, používání dalších metod ověřování a dalších, například:

* [Změna metody Request](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Změna segmentů adres URL žádosti](../api-management/api-management-transformation-policies.md#RewriteURL)
* Nastavení domén API Management v [Azure Portal](https://portal.azure.com/)
* Nastavte zásady pro kontrolu základního ověřování.

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Otázka: co se změnilo, když se schéma migruje z 1. prosince 2014 Preview?

Odpověď: Zde je souhrn těchto změn:

| 1\. prosince 2014 Preview | 1\. června 2016 |
| --- | --- |
| Kliknout na aplikace API **naslouchacího procesu http** |Klikněte na **Ruční aktivační událost** (není vyžadována aplikace API). |
| Nastavení naslouchacího procesu HTTP "*odesílá odpověď automaticky*" |Buď do definice pracovního postupu zahrňte akci **odpovědi** , nebo ne. |
| Konfigurace základního ověřování nebo ověřování OAuth |prostřednictvím API Management |
| Konfigurace metody HTTP |V části **Zobrazit upřesňující možnosti**vyberte metodu HTTP. |
| Konfigurovat relativní cestu |V části **Zobrazit upřesňující možnosti**přidejte relativní cestu. |
| Odkazování na příchozí text prostřednictvím `@triggerOutputs().body.Content` |Odkaz prostřednictvím `@triggerOutputs().body` |
| Akce **odeslání odpovědi HTTP** u NASLOUCHACÍHO procesu http |Klikněte na **odpovědět na požadavek HTTP** (není vyžadována aplikace API). |

## <a name="next-steps"></a>Další kroky

* [Vytváření definic aplikací logiky](logic-apps-author-definitions.md)
* [Zpracování chyb a výjimek](logic-apps-exception-handling.md)