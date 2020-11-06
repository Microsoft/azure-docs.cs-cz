---
title: Volání, triggery nebo vnořování Logic Apps pomocí triggerů požadavků
description: Nastavení koncových bodů HTTPS pro volání, spouštění nebo vnoření pracovních postupů aplikací logiky v Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 08/27/2020
ms.openlocfilehash: 8a59b47dadd845f1a522854c503af11c8fff72fd
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331970"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Volání, triggery nebo vnořování aplikací logiky pomocí koncových bodů HTTPS v Azure Logic Apps

Aby se aplikace logiky dokázala volat přes adresu URL a mohla přijímat příchozí žádosti z jiných služeb, můžete nativně vystavit synchronní koncový bod HTTPS pomocí triggeru na základě požadavku ve vaší aplikaci logiky. Díky této funkci můžete svou aplikaci logiky volat z jiných aplikací logiky a vytvořit vzor koncových bodů, které lze volat. K nastavení možného koncového bodu pro zpracování příchozích volání můžete použít některý z těchto typů triggerů:

* [Žádost](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Aktivační události spravovaného konektoru, které mají [typ vstupech apiconnectionwebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a můžou přijímat příchozí požadavky HTTPS

Tento článek ukazuje, jak vytvořit volající koncový bod ve vaší aplikaci logiky pomocí triggeru žádosti a zavolat tento koncový bod z jiné aplikace logiky. Všechny zásady se vztahují stejným způsobem na jiné typy triggerů, které můžete použít pro příjem příchozích požadavků.


Další informace o zabezpečení autorizace a šifrování příchozích volání do vaší aplikace logiky, jako je například TLS ( [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), dříve označované jako SSL (Secure SOCKETS Layer) (SSL), [Azure Active Directory otevřené ověřování (Azure AD OAuth)](../active-directory/develop/index.yml), vystavení vaší aplikace logiky pomocí služby Azure API Management nebo omezení IP adres, které pocházejí z příchozích volání, najdete v tématu [zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ve které chcete aktivační proceduru použít k vytvoření možného koncového bodu. Můžete začít buď s prázdnou aplikací logiky, nebo s existující aplikací logiky, kde můžete nahradit aktuální Trigger. Tento příklad začíná prázdnou aplikací logiky. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-callable-endpoint"></a>Vytvořit volatelné koncové body

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vytvořte a otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

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

1. Uložte aplikaci logiky.

   V poli **Adresa URL příspěvku http** se teď zobrazuje VYGENEROVANÁ adresa URL pro zpětné volání, kterou můžou další služby použít k volání a aktivaci vaší aplikace logiky. Tato adresa URL obsahuje parametry dotazu, které určují klíč sdíleného přístupového podpisu (SAS), který se používá k ověřování.

   ![Vygenerovaná adresa URL zpětného volání pro koncový bod](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Chcete-li zkopírovat adresu URL zpětného volání, máte tyto možnosti:

   * Napravo od pole **Adresa URL http post** vyberte **Kopírovat adresu URL** (ikona zkopírovat soubory).

   * Provést toto volání příspěvku:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Zkopírujte adresu URL zpětného volání z podokna **přehledu** vaší aplikace logiky.

     1. V nabídce aplikace logiky zvolte **Přehled**.

     1. V části **Souhrn** vyberte **Zobrazit historii aktivačních událostí**.

        ![Získat adresu URL koncového bodu z Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. V části **Adresa URL pro zpětné volání [post]** zkopírujte adresu URL:

        ![Zkopírovat adresu URL koncového bodu z Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Vybrat očekávanou metodu žádosti

Ve výchozím nastavení očekává aktivační událost požadavek POST. Můžete zadat jinou metodu, která má být očekávána, ale pouze jediná metoda.

1. V aktivační události žádosti otevřete seznam **Přidat nový parametr** a vyberte **metodu** , která do triggeru přidá tuto vlastnost.

   ![Přidat vlastnost Method k triggeru](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. V seznamu **Metoda** vyberte metodu, kterou by měla aktivační událost očekávat místo toho. Nebo můžete zadat vlastní metodu.

   Vyberte například metodu **Get** , abyste mohli otestovat adresu URL koncového bodu později.

   ![Vyberte metodu žádosti očekávanou triggerem.](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Předání parametrů prostřednictvím adresy URL koncového bodu

Pokud chcete přijmout hodnoty parametrů prostřednictvím adresy URL koncového bodu, máte tyto možnosti:

* [Přijímá hodnoty prostřednictvím parametrů GET](#get-parameters) nebo parametrů adresy URL.

  Tyto hodnoty se předávají jako páry název-hodnota v adrese URL koncového bodu. Pro tuto možnost musíte použít metodu GET v triggeru žádosti. V následné akci můžete získat hodnoty parametrů jako aktivační výstupy pomocí `triggerOutputs()` funkce ve výrazu.

* [Přijměte hodnoty pomocí relativní cesty](#relative-path) pro parametry v triggeru žádosti.

  Tyto hodnoty se předávají pomocí relativní cesty v adrese URL koncového bodu. Musíte také explicitně [Vybrat metodu](#select-method) , kterou aktivační procedura očekává. V následné akci můžete získat hodnoty parametrů jako aktivační výstupy odkazem přímo na tyto výstupy.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Přijmout hodnoty prostřednictvím parametrů GET

1. V triggeru žádosti otevřete **seznam přidat nový parametr** , přidejte do triggeru vlastnost **Method** a vyberte metodu **Get** .

   Další informace najdete v tématu [Výběr očekávané metody žádosti](#select-method).

1. V aktivační události žádosti přidejte akci, u které chcete použít hodnotu parametru. V tomto příkladu přidejte akci **odpovědi** .

   1. V části Trigger žádosti vyberte **Nový krok**  >  **přidat akci**.
   
   1. V části **zvolit akci** zadejte do vyhledávacího pole `response` jako filtr. V seznamu akce vyberte akci **reakce** .

1. Chcete-li vytvořit `triggerOutputs()` výraz, který načte hodnotu parametru, postupujte takto:

   1. Klikněte do vlastnosti **tělo** akce odpovědi, aby se zobrazil seznam dynamického obsahu a **výraz** SELECT.

   1. Do pole **výraz** zadejte tento výraz, nahraďte `parameter-name` názvem parametru a vyberte **OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Přidat výraz "triggerOutputs ()" k triggeru](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      Ve vlastnosti **tělo** se výraz přeloží na `triggerOutputs()` token.

      ![Vyřešený výraz "triggerOutputs ()"](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Pokud aplikaci logiky uložíte, opustíte návrháře a vrátíte se do návrháře, token zobrazí název parametru, který jste zadali, například:

      ![Vyřešený výraz pro název parametru](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      V zobrazení kódu se vlastnost **text** zobrazuje v definici akce odpovědi následujícím způsobem:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Předpokládejme například, že chcete předat hodnotu parametru s názvem `postalCode` . Vlastnost **body** určuje řetězec `Postal Code: ` s koncovým mezerou následovaný odpovídajícím výrazem:

      ![Přidat vzorový výraz "triggerOutputs ()" k triggeru](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Chcete-li otestovat vyvolaný koncový bod, zkopírujte adresu URL zpětného volání z triggeru žádosti a vložte adresu URL do jiného okna prohlížeče. Do adresy URL přidejte název parametru a hodnotu za otazníkem ( `?` ) k adrese URL v následujícím formátu a stiskněte klávesu ENTER.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   Prohlížeč vrátí odpověď s tímto textem: `Postal Code: 123456`

   ![Odpověď odeslání požadavku na adresu URL zpětného volání](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Chcete-li zadat název parametru a hodnotu do jiné pozice v rámci adresy URL, nezapomeňte použít ampersand ( `&` ) jako předponu, například:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Tento příklad ukazuje adresu URL zpětného volání s názvem a hodnotou ukázkového parametru `postalCode=123456` v rámci adresy URL v různých pozicích:

   * 1. pozice: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * druhá pozice: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Pokud chcete do identifikátoru URI zahrnout symbol hash nebo symbol křížku ( **#** ), použijte místo toho tuto kódovanou verzi: `%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Přijmout hodnoty pomocí relativní cesty

1. V triggeru žádosti otevřete seznam **Přidat nový parametr** a vyberte **relativní cesta** , která přidá tuto vlastnost triggeru.

   ![Přidat vlastnost relativní cesty k triggeru](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Do vlastnosti **relativní cesta** zadejte relativní cestu k parametru ve schématu JSON, který má vaše adresa URL přijmout, například `/address/{postalCode}` .

   ![Zadejte relativní cestu pro parametr.](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. V aktivační události žádosti přidejte akci, u které chcete použít hodnotu parametru. V tomto příkladu přidejte akci **odpovědi** .

   1. V části Trigger žádosti vyberte **Nový krok**  >  **přidat akci**.

   1. V části **zvolit akci** zadejte do vyhledávacího pole `response` jako filtr. V seznamu akce vyberte akci **reakce** .

1. Do vlastnosti **tělo** akce odpovědi přidejte token, který představuje parametr, který jste zadali v relativní cestě triggeru.

   Předpokládejme například, že chcete vrátit akci odpovědi `Postal Code: {postalCode}` .

   1. Do vlastnosti **tělo** zadejte mezeru `Postal Code: ` . Umístěte kurzor do pole pro úpravy tak, aby seznam dynamického obsahu zůstal otevřený.

   1. V seznamu dynamického obsahu vyberte v části **při přijetí požadavku HTTP** možnost token **PSČ** .

      ![Přidat zadaný parametr do těla odpovědi](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      Vlastnost **text** teď obsahuje vybraný parametr:

      ![Příklad těla odpovědi s parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Uložte aplikaci logiky.

   V aktivační události požadavku se adresa URL zpětného volání aktualizuje a teď obsahuje relativní cestu, například:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Pokud chcete otestovat vyvolaný koncový bod, zkopírujte aktualizovanou adresu URL zpětného volání z triggeru žádosti, vložte adresu URL do jiného okna prohlížeče, nahraďte adresu `{postalCode}` URL řetězcem `123456` a stiskněte klávesu ENTER.

   Prohlížeč vrátí odpověď s tímto textem: `Postal Code: 123456`

   ![Odpověď odeslání požadavku na adresu URL zpětného volání](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Pokud chcete do identifikátoru URI zahrnout symbol hash nebo symbol křížku ( **#** ), použijte místo toho tuto kódovanou verzi: `%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>Volání aplikace logiky prostřednictvím adresy URL koncového bodu

Po vytvoření koncového bodu můžete aktivovat aplikaci logiky odesláním `POST` požadavku https na úplnou adresu URL koncového bodu. Logic Apps mají integrovanou podporu koncových bodů s přímým přístupem.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokeny generované ze schématu

Když v triggeru požadavku zadáte schéma JSON, návrhář aplikace logiky vygeneruje tokeny pro vlastnosti v tomto schématu. Tyto tokeny pak můžete použít k předávání dat prostřednictvím pracovního postupu aplikace logiky.

Například pokud přidáte další vlastnosti, například `"suite"` , do schématu JSON, budou tokeny pro tyto vlastnosti k dispozici pro použití v pozdějších krocích pro vaši aplikaci logiky. Toto je kompletní schéma JSON:

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

Pracovní postupy můžete do aplikace logiky vnořovat přidáním dalších aplikací logiky, které můžou přijímat požadavky. Pokud chcete zahrnout tyto Logic Apps, postupujte takto:

1. V kroku, kde chcete zavolat jinou aplikaci logiky, vyberte **Nový krok**  >  **přidat akci**.

1. V části **zvolit akci** vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `logic apps` jako filtr. V seznamu akce vyberte **možnost zvolit pracovní postup Logic Apps**.

   ![Vnoření aplikace logiky do aktuální aplikace logiky](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Návrhář zobrazí opravňující aplikace logiky, které můžete vybrat.

1. Vyberte aplikaci logiky, kterou chcete zavolat z aktuální aplikace logiky.

   ![Vyberte aplikaci logiky, která se má zavolat z aktuální aplikace logiky.](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Odkazování na obsah z příchozího požadavku

Pokud je typ obsahu příchozího požadavku `application/json` , můžete odkazovat na vlastnosti v příchozím požadavku. V opačném případě se tento obsah považuje za jednu binární jednotku, kterou můžete předat jiným rozhraním API. Chcete-li odkazovat na tento obsah v rámci pracovního postupu vaší aplikace logiky, je nutné nejprve tento obsah převést.

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

Někdy budete chtít reagovat na určité požadavky, které aktivují aplikaci logiky vrácením obsahu volajícímu. Chcete-li vytvořit stavový kód, hlavičku a text pro odpověď, použijte akci reakce. Tato akce se může objevit kdekoli ve vaší aplikaci logiky, ne jen na konci pracovního postupu. Pokud vaše aplikace logiky neobsahuje akci odpovědi, koncový bod odpoví *hned* stavem **přijato 202** .

Aby původní volající úspěšně dostal odpověď, všechny požadované kroky pro odpověď musí být dokončeny v rámci [časového limitu požadavku](./logic-apps-limits-and-config.md) , pokud se aktivovaná aplikace logiky nevolá jako vnořená aplikace logiky. Pokud se v rámci tohoto limitu nevrátí žádná odpověď, příchozí požadavek vyprší a obdrží odpověď na **časový limit klienta 408** .

U vnořených logických aplikací pokračuje nadřazená aplikace logiky na odpověď, dokud se všechny kroky nedokončí, a to bez ohledu na to, kolik času se vyžaduje.

### <a name="construct-the-response"></a>Sestavit odpověď

V těle odpovědi můžete zahrnout více hlaviček a libovolného typu obsahu. Například hlavička této odpovědi určuje, že typ obsahu odpovědi je `application/json` a že tělo obsahuje hodnoty `town` vlastností a, které jsou `postalCode` založené na schématu JSON popsané dříve v tomto tématu pro aktivační událost požadavku.

![Poskytnout obsah odpovědi pro akci odpovědi HTTPS](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpovědi mají tyto vlastnosti:

| Property (zobrazení) | Property (JSON) | Popis |
|--------------------|-----------------|-------------|
| **Stavový kód** | `statusCode` | Stavový kód HTTPS, který se má použít v odpovědi na příchozí požadavek. Tento kód může být libovolný platný stavový kód, který začíná na 2xx, 4xx nebo 5xx. Nicméně stavové kódy 3xx nejsou povoleny. |
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

Odpověď **: Azure** bezpečně generuje adresy URL zpětného volání aplikace logiky pomocí [sdíleného přístupového podpisu (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature). Tento podpis projde jako parametr dotazu a musí být ověřený předtím, než bude možné spustit aplikaci logiky. Azure vygeneruje signaturu pomocí jedinečné kombinace tajného klíče na aplikaci logiky, názvu triggeru a prováděné operace. Takže pokud nikdo nemá přístup k klíči tajné aplikace logiky, nemůže vygenerovat platný podpis.

> [!IMPORTANT]
> Pro produkční a vyšší systémy zabezpečení důrazně doporučujeme volat aplikaci logiky přímo z prohlížeče z těchto důvodů:
>
> * V adrese URL se zobrazí sdílený přístupový klíč.
> * Nemůžete spravovat zásady obsahu zabezpečení kvůli sdíleným doménám mezi Azure Logic Apps zákazníky.

Další informace o zabezpečení autorizace a šifrování příchozích volání do vaší aplikace logiky, jako je například TLS ( [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), dříve označované jako SSL (Secure SOCKETS Layer) (SSL), [Azure Active Directory otevřené ověřování (Azure AD OAuth)](../active-directory/develop/index.yml), vystavení vaší aplikace logiky pomocí služby Azure API Management nebo omezení IP adres, které pocházejí z příchozích volání, najdete v tématu [zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

#### <a name="q-can-i-configure-callable-endpoints-further"></a>Otázka: můžu dále konfigurovat nastavované koncové body?

Odpověď **: Ano** , koncové body https podporují pokročilejší konfiguraci prostřednictvím [Azure API Management](../api-management/api-management-key-concepts.md). Tato služba také nabízí možnost konzistentně spravovat všechna vaše rozhraní API, včetně aplikací logiky, nastavení vlastních názvů domén, používání dalších metod ověřování a dalších, například:

* [Změna metody Request](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Změna segmentů adres URL žádosti](../api-management/api-management-transformation-policies.md#RewriteURL)
* Nastavení domén API Management v [Azure Portal](https://portal.azure.com/)
* Nastavte zásady pro kontrolu základního ověřování.

## <a name="next-steps"></a>Další kroky

* [Příjem příchozích volání HTTPS a jejich reakce pomocí Azure Logic Apps](../connectors/connectors-native-reqres.md)
* [Zabezpečený přístup a data v Azure Logic Apps – přístup pro příchozí volání na triggery na základě požadavků](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
