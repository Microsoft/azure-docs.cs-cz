---
title: Volání, triggery nebo vnořené aplikace logiky
description: Nastavení koncových bodů HTTP pro volání, aktivaci nebo vnoření pracovních postupů aplikace logiky v aplikacích Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191337"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Volání, aktivace nebo vnoření aplikací logiky pomocí koncových bodů HTTP v aplikacích Azure Logic Apps

Chcete-li, aby vaše aplikace logiky byla volatelná prostřednictvím adresy URL, aby vaše aplikace logiky mohla přijímat příchozí požadavky z jiných služeb, můžete nativně vystavit synchronní koncový bod HTTP jako aktivační událost v této aplikaci logiky. Když nastavíte tuto možnost, můžete také vnořit aplikaci logiky do jiných aplikací logiky, což umožňuje vytvořit vzorek volatelných koncových bodů.

Chcete-li nastavit koncový bod HTTP, můžete použít některý z těchto typů aktivačních událostí, které umožňují aplikacím logiky přijímat příchozí požadavky:

* [Požadavek](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Aktivační události spravovaného konektoru, které mají [typ ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a mohou přijímat příchozí požadavky HTTP

> [!NOTE]
> Tyto příklady používají aktivační událost Požadavek, ale můžete použít libovolnou aktivační událost založenou na požadavcích HTTP, která je v předchozím seznamu. Všechny zásady se identicky vztahují na tyto další typy aktivačních událostí.

Pokud s aplikacemi logiky teprve začínáte, přečtěte si informace o tom, [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, kde chcete nastavit koncový bod HTTP jako aktivační událost. Můžete začít buď s prázdnou aplikaci logiky nebo existující aplikace logiky, kde chcete nahradit aktuální aktivační událost. Tento příklad začíná prázdnou aplikací logiky.

## <a name="create-a-callable-endpoint"></a>Vytvoření volaného koncového bodu

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vytvořte a otevřete prázdnou aplikaci logiky v Návrháři aplikace logiky.

   Tento příklad používá aktivační událost Požadavku, ale můžete použít libovolnou aktivační událost, která může přijímat příchozí požadavky HTTP. Všechny zásady se identicky vztahují na tyto aktivační události. Další informace o aktivační události Požadavek najdete v tématu [Příjem a reakce na příchozí volání HTTPS pomocí Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. Pod vyhledávacím polem vyberte **Předdefinované**. Do vyhledávacího pole `request` zadejte jako filtr. V seznamu aktivačních událostí vyberte **při přijetí požadavku HTTP**.

   ![Vyhledání a výběr aktivační události Požadavku](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Volitelně můžete v poli **Schéma Tělo požadavku** zadat schéma JSON, které popisuje datovou část nebo data, která očekáváte, že aktivační událost obdržíte.

   Návrhář používá toto schéma ke generování tokenů, které představují výstupy aktivační události. Potom můžete snadno odkazovat na tyto výstupy v celém pracovním postupu aplikace logiky. Další informace o [tokenech generovaných ze schémat JSON](#generated-tokens).

   V tomto příkladu zadejte toto schéma:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Poskytnout schéma JSON pro akci Požadavek](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Nebo můžete vygenerovat schéma JSON poskytnutím ukázkové datové části:

   1. V aktivační události **Požadavek** vyberte **použít ukázkovou datovou část ke generování schématu**.

   1. Do pole **Enter nebo paste ukázkové datové části JSON** zadejte ukázkovou datovou část, například:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Až budete připraveni, vyberte **Hotovo**.

      Pole **Schéma Tělo požadavku JSON** nyní zobrazuje generované schéma.

1. Uložte svou aplikaci logiky.

   **Pole HTTP POST na tuto adresu URL** nyní zobrazuje vygenerovanou adresu URL zpětného volání, kterou mohou jiné služby použít k volání a aktivaci aplikace logiky. Tato adresa URL obsahuje klíč s sdíleným přístupovým podpisem (SAS), který se používá pro ověřování, v parametrech dotazu, například:

   ![Vygenerovaná adresa URL zpětného volání pro koncový bod](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Adresu URL koncového bodu HTTP můžete také získat z podokna **Přehled** aplikace logiky.

   1. V nabídce aplikace logiky vyberte **Přehled**.

   1. V části **Souhrn** vyberte **Zobrazit historii aktivačních událostí**.

      ![Získání adresy URL koncového bodu HTTP z webu Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. V části **Adresa URL pro zpětné volání [POST]** zkopírujte adresu URL:

      ![Kopírování adresy URL koncového bodu HTTP z webu Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Nebo můžete získat URL tím, že tento hovor:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Nastavit očekávanou metodu HTTP

Ve výchozím nastavení očekává aktivační událost Požadavku požadavek HTTP POST. Můžete však zadat jinou metodu, kterou chcete očekávat, ale pouze jednu metodu.

1. V aktivační události Požadavek otevřete seznam **Přidat nový parametr** a vyberte **Metodu**, která přidá tuto vlastnost do aktivační události.

   ![Přidání vlastnosti "Metoda" pro aktivaci](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Ze seznamu **Metoda** vyberte jinou metodu, kterou aktivační událost očekává. Nebo můžete zadat vlastní metodu.

   Vyberte například metodu **GET,** abyste později mohli otestovat adresu URL koncového bodu HTTP.

   ![Vyberte metodu HTTP, která se má použít pro aktivační událost.](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Přijmout parametry v adrese URL koncového bodu

Pokud chcete, aby adresa URL koncového bodu přijímala parametry, zadejte v aktivační události relativní cestu. Je také nutné explicitně [nastavit metodu,](#set-method) kterou váš požadavek HTTP očekává.

1. V aktivační události Požadavek otevřete seznam **Přidat nový parametr** a vyberte Relativní **cesta**, která přidá tuto vlastnost k aktivační události.

   ![Přidání vlastnosti Relativní cesta k aktivaci](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Ve vlastnosti **Relativní cesta** zadejte relativní cestu pro parametr ve schématu JSON, který má `address/{postalCode}`adresa URL přijmout, například .

   ![Určení relativní cesty pro parametr](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Chcete-li použít parametr, vyhledejte a přidejte akci **Odpověď** do aplikace logiky.

   1. V části Požadavek na aktivační událost vyberte **Nový krok** > **Přidat akci**.

   1. V části **Zvolte akci**zadejte `response` do vyhledávacího pole jako filtr.

   1. Ze seznamu akcí vyberte akci **Odpověď.**

1. Do vlastnosti **Body** akce odezvy zahrňte token, který představuje parametr, který jste zadali v relativní cestě aktivační události.

   Předpokládejme například, že chcete, `Postal Code: {postalCode}`aby se akce Odpověď vrátila .

   Do **Body** vlastnosti Body `Postal Code: ` zadejte koncový prostor. Ze seznamu dynamického obsahu, který se zobrazí, vyberte token **psEK.**

   ![Přidání zadaného parametru do těla odezvy](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   Vlastnost **Body** nyní obsahuje vybraný parametr:

   ![Ukázkové tělo odezvy s parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Uložte svou aplikaci logiky.

    Adresa URL koncového bodu HTTP nyní obsahuje relativní cestu, například:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Chcete-li otestovat koncový bod HTTP, zkopírujte a vložte `123456`aktualizovanou adresu URL do jiného okna prohlížeče, ale nahraďte klávesou `{postalCode}` a stiskněte Enter.

   Váš prohlížeč zobrazuje tento text:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Volání aplikace logiky prostřednictvím koncového bodu HTTP

Po vytvoření koncového bodu HTTP můžete aktivovat aplikaci `POST` logiky odesláním požadavku HTTP na úplnou adresu URL koncového bodu. Aplikace logiky mají integrovanou podporu pro koncové body s přímým přístupem.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokeny generované ze schématu

Když zadáte schéma JSON v request trigger, Návrhář aplikace logiky generuje tokeny pro vlastnosti v tomto schématu. Potom můžete použít tyto tokeny pro předávání dat prostřednictvím pracovního postupu aplikace logiky.

Například pokud přidáte další vlastnosti, například `"suite"`, do schématu JSON, tokeny pro tyto vlastnosti jsou k dispozici pro použití v pozdějších krocích pro aplikaci logiky. Zde je kompletní schéma JSON:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Vytváření vnořených aplikací logiky

Pracovní postupy můžete vnořit do aplikace logiky přidáním dalších aplikací logiky, které mohou přijímat požadavky. Chcete-li zahrnout tyto aplikace logiky, postupujte takto:

1. Pod krokem, kde chcete volat jinou aplikaci logiky, vyberte **Nový krok** > **Přidat akci**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `logic apps` zadejte jako filtr. Ze seznamu akcí vyberte **Zvolit pracovní postup Logic Apps**.

   ![Aplikace logiky Vnoření uvnitř aktuální aplikace logiky](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Návrhář zobrazí způsobilé aplikace logiky, které můžete vybrat.

1. Vyberte aplikaci logiky, kterou chcete volat z aktuální aplikace logiky.

   ![Vyberte aplikaci logiky, která má být volána z aktuální aplikace logiky.](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Referenční obsah z příchozí žádosti

Pokud je `application/json`typ obsahu příchozí žádosti , můžete odkazovat na vlastnosti příchozí žádosti. V opačném případě je tento obsah považován za jednu binární jednotku, kterou můžete předat jiným možnostem nastavení API. Chcete-li odkazovat na tento obsah v pracovním postupu aplikace logiky, musíte nejprve převést tento obsah.

Pokud například předáváte obsah, `application/xml` který má typ, můžete tento [ `@xpath()` výraz](../logic-apps/workflow-definition-language-functions-reference.md#xpath) použít k provedení extrakce XPath nebo použít [ `@json()` výraz](../logic-apps/workflow-definition-language-functions-reference.md#json) pro převod XML na JSON. Přečtěte si další informace o práci s podporovanými [typy obsahu](../logic-apps/logic-apps-content-type.md).

Chcete-li získat výstup z příchozí hopožadavku, můžete použít [ `@triggerOutputs` výraz](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Předpokládejme například, že máte výstup, který vypadá jako tento příklad:

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

Chcete-li `body` získat přístup konkrétně k vlastnosti, můžete použít [ `@triggerBody()` výraz](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) jako zástupce.

## <a name="respond-to-requests"></a>Odpovědět na požadavky

Někdy chcete reagovat na určité požadavky, které aktivují aplikaci logiky vrácením obsahu volajícímu. Chcete-li vytvořit stavový kód, záhlaví a text pro vaši odpověď, použijte akci Odpověď. Tato akce se může zobrazit kdekoli v aplikaci logiky, nejen na konci pracovního postupu. Pokud vaše aplikace logiky neobsahuje akci odpovědi, koncový bod HTTP *okamžitě* odpoví stavem **202 Accepted.**

Pro původní volající úspěšně získat odpověď, všechny požadované kroky pro odpověď musí dokončit v rámci [limitu časového limitu požadavku,](./logic-apps-limits-and-config.md) pokud se aktivuje aplikace logiky je volána jako vnořené aplikace logiky. Pokud žádná odpověď je vrácena v rámci tohoto limitu, příchozí požadavek časový limit a obdrží **408 Klient časový limit** odpověď.

Pro vnořené aplikace logiky nadřazené aplikace logiky i nadále čekat na odpověď, dokud nejsou dokončeny všechny kroky, bez ohledu na to, kolik času je potřeba.

### <a name="construct-the-response"></a>Konstrukce odpovědi

V těle odpovědi můžete zahrnout více záhlaví a jakýkoli typ obsahu. Například záhlaví této odpovědi určuje, že typ obsahu `application/json` odpovědi je a že `town` tělo `postalCode` obsahuje hodnoty pro vlastnosti a založené na schématu JSON popsaném výše v tomto tématu pro aktivační událost Požadavku.

![Poskytnout obsah odpovědi pro akci odpovědi HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpovědi mají tyto vlastnosti:

| Vlastnost (displej) | Vlastnost (JSON) | Popis |
|--------------------|-----------------|-------------|
| **Stavový kód** | `statusCode` | Stavový kód HTTP, který se má použít v odpovědi pro příchozí požadavek. Tento kód může být libovolný platný stavový kód, který začíná 2xx, 4xx nebo 5xx. Stavové kódy 3xx však nejsou povoleny. |
| **Hlavičky** | `headers` | Jedno nebo více záhlaví, která mají být zahrnuta do odpovědi |
| **Text** | `body` | Objekt těla, který může být řetězec, objekt JSON nebo dokonce binární obsah odkazovaný z předchozího kroku |
||||

Chcete-li zobrazit definici JSON pro akci Odpověď a úplnou definici JSON aplikace logiky, vyberte na panelu nástrojů Návrhář aplikace logiky **zobrazení kódu**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Otázky a odpovědi

#### <a name="q-what-about-url-security"></a>Otázka: A co zabezpečení adresy URL?

**A**: Azure bezpečně generuje adresy URL zpětného volání aplikace logiky pomocí [sdíleného přístupového podpisu (SAS).](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature) Tento podpis prochází jako parametr dotazu a musí být ověřena před spuštěním aplikace logiky. Azure generuje podpis pomocí jedinečné kombinace tajného klíče na aplikaci logiky, název aktivační události a operace, která se provádí. Takže pokud někdo má přístup ke klíči tajné aplikace logiky, nemohou generovat platný podpis.

> [!IMPORTANT]
> Pro produkční a vyšší bezpečnostní systémy důrazně nedoporučujeme volat aplikaci logiky přímo z prohlížeče z těchto důvodů:
>
> * Sdílený přístupový klíč se zobrazí v adrese URL.
> * Zásady obsahu zabezpečení nelze spravovat kvůli sdíleným doménám napříč zákazníky Azure Logic Apps.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Otázka: Lze dále konfigurovat koncové body PROTOKOLU HTTP?

**A:** Ano, http koncové body podporují pokročilejší konfiguraci prostřednictvím [Azure API Management](../api-management/api-management-key-concepts.md). Tato služba také nabízí možnost konzistentně spravovat všechna vaše api, včetně aplikací logiky, nastavit vlastní názvy domén, používat více metod ověřování a další, například:

* [Změnit metodu požadavku](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Změna segmentů adresy URL požadavku](../api-management/api-management-transformation-policies.md#RewriteURL)
* Nastavení domén správy rozhraní API na [webu Azure Portal](https://portal.azure.com/)
* Nastavení zásad pro kontrolu základního ověřování

## <a name="next-steps"></a>Další kroky

* [Příjem a reakce na příchozí volání HTTPS pomocí Azure Logic Apps](../connectors/connectors-native-reqres.md)