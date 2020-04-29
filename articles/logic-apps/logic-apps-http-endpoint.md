---
title: Volání, triggery nebo vnořené aplikace logiky
description: Nastavte koncové body HTTP na volání, triggery nebo vnořené pracovní postupy aplikace logiky v Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77191337"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Volání, triggery nebo vnořování aplikací logiky pomocí koncových bodů HTTP v Azure Logic Apps

Pokud chcete, aby aplikace logiky mohla přijímat příchozí žádosti z jiných služeb, můžete nativně vystavit synchronní koncový bod HTTP jako Trigger v této aplikaci logiky. Když nastavíte tuto možnost, můžete aplikaci logiky vnořit do jiných Logic Apps, které vám umožní vytvořit vzor koncových bodů, které lze volat.

Pokud chcete nastavit koncový bod HTTP, můžete použít některý z těchto typů triggerů, které umožňují Logic Apps přijímat příchozí požadavky:

* [Request](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Aktivační události spravovaného konektoru, které mají [typ vstupech apiconnectionwebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a můžou přijímat příchozí požadavky http

> [!NOTE]
> V těchto příkladech se používá aktivační událost žádosti, ale můžete použít libovolný Trigger založený na požadavku HTTP, který je v předchozím seznamu. Všechny zásady se stejným způsobem vztahují na tyto další typy triggerů.

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ve které chcete nastavit koncový bod HTTP jako Trigger. Můžete začít buď s prázdnou aplikací logiky, nebo s existující aplikací logiky, kde chcete nahradit aktuální Trigger. Tento příklad začíná prázdnou aplikací logiky.

## <a name="create-a-callable-endpoint"></a>Vytvořit volatelné koncové body

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvořte a otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá aktivační událost žádosti, ale můžete použít libovolný Trigger, který může přijímat příchozí požadavky HTTP. Všechny zásady se na tyto triggery vztahují stejně. Další informace o triggeru žádosti najdete v tématu [příjem a odpověď na příchozí volání HTTPS pomocí Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. V poli hledání vyberte **předdefinované**. Do vyhledávacího pole zadejte `request` jako filtr. V seznamu triggery vyberte, **kdy se přijme požadavek HTTP**.

   ![Vyhledejte a vyberte Trigger žádosti.](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Volitelně můžete v poli **schéma JSON textu žádosti** zadat schéma JSON, které popisuje datovou část nebo data, které očekáváte, že aktivační událost obdrží.

   Návrhář pomocí tohoto schématu generuje tokeny, které reprezentují výstupy triggerů. Pak můžete snadno odkazovat na tyto výstupy v rámci pracovního postupu vaší aplikace logiky. Přečtěte si další informace o [tokenech generovaných ze schémat JSON](#generated-tokens).

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

   ![Zadat schéma JSON pro akci žádosti](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Nebo můžete vygenerovat schéma JSON zadáním ukázkové datové části:

   1. V triggeru **žádosti** vyberte **použít ukázkovou datovou část k vygenerování schématu**.

   1. Do pole vložit **nebo vložit ukázkovou datovou část JSON** zadejte ukázkovou datovou část, například:

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

      Pole **schématu JSON textu žádosti** teď zobrazuje vygenerované schéma.

1. Uložte svou aplikaci logiky.

   Pole **http post do této adresy URL** teď zobrazuje VYGENEROVANOU adresu URL zpětného volání, kterou můžou další služby použít k volání a aktivaci vaší aplikace logiky. Tato adresa URL zahrnuje klíč sdíleného přístupového podpisu (SAS), který se používá k ověřování, v parametrech dotazu, například:

   ![Vygenerovaná adresa URL zpětného volání pro koncový bod](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Adresu URL koncového bodu HTTP můžete taky získat z podokna **přehledu** vaší aplikace logiky.

   1. V nabídce aplikace logiky zvolte **Přehled**.

   1. V části **Souhrn** vyberte **Zobrazit historii aktivačních událostí**.

      ![Získat adresu URL koncového bodu HTTP z Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. V části **Adresa URL pro zpětné volání [post]** zkopírujte adresu URL:

      ![Zkopírovat adresu URL koncového bodu HTTP z Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Můžete také získat adresu URL tím, že zadáte toto volání:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Nastavit očekávanou metodu HTTP

Ve výchozím nastavení očekává aktivační událost požadavek HTTP POST. Můžete ale zadat jinou metodu, kterou chcete očekávat, ale jenom jednu metodu.

1. V aktivační události žádosti otevřete seznam **Přidat nový parametr** a vyberte **metodu**, která do triggeru přidá tuto vlastnost.

   ![Přidat vlastnost Method k triggeru](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. V seznamu **Metoda** vyberte jinou metodu, kterou aktivační procedura očekává místo toho. Nebo můžete zadat vlastní metodu.

   Vyberte například metodu **Get** , abyste mohli otestovat adresu URL koncového bodu http později.

   ![Vyberte metodu HTTP, která se má použít pro aktivační událost.](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Přijmout parametry v adrese URL koncového bodu

Pokud chcete, aby adresa URL koncového bodu přijímala parametry, zadejte relativní cestu v aktivační události. Musíte taky explicitně [nastavit metodu](#set-method) , kterou požadavek HTTP očekává.

1. V triggeru žádosti otevřete seznam **Přidat nový parametr** a vyberte **relativní cesta**, která přidá tuto vlastnost triggeru.

   ![Přidat vlastnost relativní cesty k triggeru](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Do vlastnosti **relativní cesta** zadejte relativní cestu k parametru ve schématu JSON, který má vaše adresa URL přijmout, například `address/{postalCode}`.

   ![Zadejte relativní cestu pro parametr.](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Pokud chcete použít parametr, vyhledejte a přidejte akci **odpovědi** do aplikace logiky.

   1. V části Trigger žádosti vyberte **Nový krok** > **přidat akci**.

   1. V části **zvolit akci**zadejte `response` do vyhledávacího pole jako filtr.

   1. V seznamu akce vyberte akci **reakce** .

1. Do vlastnosti **tělo** akce odpovědi přidejte token, který představuje parametr, který jste zadali v relativní cestě triggeru.

   Předpokládejme například, že chcete vrátit `Postal Code: {postalCode}`akci odpovědi.

   Do vlastnosti **tělo** zadejte `Postal Code: ` mezeru. V seznamu dynamický obsah, který se zobrazí, vyberte token **PSČ** .

   ![Přidat zadaný parametr do těla odpovědi](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   Vlastnost **text** teď obsahuje vybraný parametr:

   ![Příklad těla odpovědi s parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Uložte svou aplikaci logiky.

    Adresa URL koncového bodu HTTP teď obsahuje relativní cestu, například:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Chcete-li otestovat koncový bod HTTP, zkopírujte a vložte aktualizovanou adresu URL do jiného okna prohlížeče `{postalCode}` , `123456`nahraďte ji a stiskněte klávesu ENTER.

   V prohlížeči se zobrazí tento text:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Volání aplikace logiky prostřednictvím koncového bodu HTTP

Po vytvoření koncového bodu HTTP můžete aktivovat aplikaci logiky odesláním požadavku HTTP `POST` na úplnou adresu URL koncového bodu. Logic Apps mají integrovanou podporu koncových bodů s přímým přístupem.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokeny generované ze schématu

Když v triggeru požadavku zadáte schéma JSON, návrhář aplikace logiky vygeneruje tokeny pro vlastnosti v tomto schématu. Tyto tokeny pak můžete použít k předávání dat prostřednictvím pracovního postupu aplikace logiky.

Například pokud přidáte další vlastnosti, například `"suite"`, do schématu JSON, budou tokeny pro tyto vlastnosti k dispozici pro použití v pozdějších krocích pro vaši aplikaci logiky. Toto je kompletní schéma JSON:

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

Pracovní postupy můžete v aplikaci logiky vnořovat přidáním dalších aplikací logiky, které můžou přijímat požadavky. Pokud chcete zahrnout tyto Logic Apps, postupujte takto:

1. V kroku, kde chcete zavolat jinou aplikaci logiky, vyberte **Nový krok** > **přidat akci**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `logic apps` jako filtr. V seznamu akce vyberte **možnost zvolit pracovní postup Logic Apps**.

   ![Vnoření aplikace logiky do aktuální aplikace logiky](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Návrhář zobrazí opravňující aplikace logiky, které můžete vybrat.

1. Vyberte aplikaci logiky, kterou chcete zavolat z aktuální aplikace logiky.

   ![Vyberte aplikaci logiky, která se má zavolat z aktuální aplikace logiky.](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Odkazování na obsah z příchozího požadavku

Pokud je `application/json`typ obsahu příchozího požadavku, můžete odkazovat na vlastnosti v příchozím požadavku. V opačném případě se tento obsah považuje za jednu binární jednotku, kterou můžete předat jiným rozhraním API. Chcete-li odkazovat na tento obsah v rámci pracovního postupu vaší aplikace logiky, je nutné nejprve tento obsah převést.

Například Pokud předáváte obsah, který je `application/xml` typu, můžete použít [ `@xpath()` výraz](../logic-apps/workflow-definition-language-functions-reference.md#xpath) k provedení extrakce XPath nebo použijte [ `@json()` výraz](../logic-apps/workflow-definition-language-functions-reference.md#json) pro převod XML na JSON. Přečtěte si další informace o práci s podporovanými [typy obsahu](../logic-apps/logic-apps-content-type.md).

Chcete-li získat výstup z příchozího požadavku, můžete použít [ `@triggerOutputs` výraz](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Předpokládejme například, že máte výstup, který vypadá jako v tomto příkladu:

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

Pro přístup k konkrétní `body` vlastnosti můžete použít [ `@triggerBody()` výraz](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) jako zástupce.

## <a name="respond-to-requests"></a>Reakce na žádosti

Někdy budete chtít reagovat na určité požadavky, které aktivují aplikaci logiky vrácením obsahu volajícímu. Chcete-li vytvořit stavový kód, hlavičku a text pro odpověď, použijte akci reakce. Tato akce se může objevit kdekoli ve vaší aplikaci logiky, ne jen na konci pracovního postupu. Pokud vaše aplikace logiky neobsahuje akci odpovědi, koncový bod HTTP *okamžitě* odpoví stavem **přijato 202** .

Aby původní volající úspěšně dostal odpověď, všechny požadované kroky pro odpověď musí být dokončeny v rámci [časového limitu požadavku](./logic-apps-limits-and-config.md) , pokud se aktivovaná aplikace logiky nevolá jako vnořená aplikace logiky. Pokud se v rámci tohoto limitu nevrátí žádná odpověď, příchozí požadavek vyprší a obdrží odpověď na **časový limit klienta 408** .

U vnořených logických aplikací pokračuje nadřazená aplikace logiky na odpověď, dokud se všechny kroky nedokončí, a to bez ohledu na to, kolik času se vyžaduje.

### <a name="construct-the-response"></a>Sestavit odpověď

V těle odpovědi můžete zahrnout více hlaviček a libovolného typu obsahu. Například hlavička této odpovědi určuje, že typ obsahu odpovědi je `application/json` a že tělo obsahuje hodnoty vlastností `town` a `postalCode` , které jsou založené na schématu JSON popsané dříve v tomto tématu pro aktivační událost požadavku.

![Poskytnout obsah odpovědi pro akci odpovědi HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpovědi mají tyto vlastnosti:

| Property (zobrazení) | Property (JSON) | Popis |
|--------------------|-----------------|-------------|
| **Stavový kód** | `statusCode` | Stavový kód protokolu HTTP, který se má použít v odpovědi na příchozí požadavek. Tento kód může být libovolný platný stavový kód, který začíná na 2xx, 4xx nebo 5xx. Nicméně stavové kódy 3xx nejsou povoleny. |
| **Hlavičky** | `headers` | Jedna nebo více hlaviček, které mají být zahrnuty do odpovědi |
| **Text** | `body` | Objekt textu, který může být řetězec, objekt JSON nebo i binární obsah, na který odkazuje předchozí krok |
||||

Pokud chcete zobrazit definici JSON pro akci odpovědi a kompletní definici JSON vaší aplikace logiky, klikněte na panelu nástrojů návrháře aplikací logiky na **zobrazení kódu**.

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

#### <a name="q-what-about-url-security"></a>Otázka: co je zabezpečení adresy URL?

Odpověď **: Azure**bezpečně generuje adresy URL zpětného volání aplikace logiky pomocí [sdíleného přístupového podpisu (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Tento podpis projde jako parametr dotazu a musí být ověřený předtím, než bude možné spustit aplikaci logiky. Azure vygeneruje signaturu pomocí jedinečné kombinace tajného klíče na aplikaci logiky, názvu triggeru a prováděné operace. Takže pokud nikdo nemá přístup k klíči tajné aplikace logiky, nemůže vygenerovat platný podpis.

> [!IMPORTANT]
> Pro produkční a vyšší systémy zabezpečení důrazně doporučujeme volat aplikaci logiky přímo z prohlížeče z těchto důvodů:
>
> * V adrese URL se zobrazí sdílený přístupový klíč.
> * Nemůžete spravovat zásady obsahu zabezpečení kvůli sdíleným doménám mezi Azure Logic Apps zákazníky.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Otázka: Můžu dál nakonfigurovat koncové body HTTP?

Odpověď **: Ano**, koncové body HTTP podporují pokročilejší konfiguraci prostřednictvím [Azure API Management](../api-management/api-management-key-concepts.md). Tato služba také nabízí možnost konzistentně spravovat všechna vaše rozhraní API, včetně aplikací logiky, nastavení vlastních názvů domén, používání dalších metod ověřování a dalších, například:

* [Změna metody Request](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Změna segmentů adres URL žádosti](../api-management/api-management-transformation-policies.md#RewriteURL)
* Nastavení domén API Management v [Azure Portal](https://portal.azure.com/)
* Nastavte zásady pro kontrolu základního ověřování.

## <a name="next-steps"></a>Další kroky

* [Příjem příchozích volání HTTPS a jejich reakce pomocí Azure Logic Apps](../connectors/connectors-native-reqres.md)