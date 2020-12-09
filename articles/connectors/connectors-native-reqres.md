---
title: Příjem a reakce na volání pomocí protokolu HTTPS
description: Zpracování příchozích požadavků HTTPS z externích služeb pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 4997853fea97d14491bd9e9101f79f324807a6a1
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920827"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Příjem a odpověď na příchozí požadavky HTTPS v Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované akce triggeru požadavku a odpovědi můžete vytvářet automatizované úlohy a pracovní postupy, které mohou přijímat příchozí požadavky přes protokol HTTPS. Chcete-li místo toho odesílat odchozí požadavky, použijte vestavěný [Trigger http nebo akci HTTP](../connectors/connectors-native-http.md).

Můžete mít například aplikaci logiky:

* Přijetí a odpověď na požadavek HTTPS na data v místní databázi.

* Aktivuje pracovní postup, když dojde k externí události Webhooku.

* Přijímat a reagovat na volání HTTPS z jiné aplikace logiky.

V tomto článku se dozvíte, jak použít akci triggeru a odpovědi žádosti, aby vaše aplikace logiky mohla přijímat příchozí volání a reagovat na ně.

Další informace o zabezpečení autorizace a šifrování příchozích volání do vaší aplikace logiky, jako je například TLS ( [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), dříve označované jako SSL (Secure SOCKETS Layer) (SSL), [Azure Active Directory otevřené ověřování (Azure AD OAuth)](../active-directory/develop/index.yml), vystavení vaší aplikace logiky pomocí služby Azure API Management nebo omezení IP adres, které pocházejí z příchozích volání, najdete v tématu [zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Předpoklady

* Účet a předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Přidat aktivační událost žádosti

Tato integrovaná aktivační událost vytvoří ručně koncový bod, který může zpracovávat *pouze* příchozí požadavky přes protokol HTTPS. Když volající pošle požadavek do tohoto koncového bodu, aktivuje se [Trigger žádosti](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a spustí se aplikace logiky. Další informace o tom, jak zavolat tuto aktivační událost, najdete v tématu [pracovní postupy volání, triggeru nebo vnořování s koncovými body HTTPS v Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Vaše aplikace logiky udržuje příchozí požadavek otevřený pouze po dobu [omezeného času](../logic-apps/logic-apps-limits-and-config.md#http-limits). Za předpokladu, že vaše aplikace logiky zahrnuje [akci odpovědi](#add-response), pokud vaše aplikace logiky po uplynutí této doby neodešle odpověď zpět volajícímu, vrátí vaše aplikace logiky `504 GATEWAY TIMEOUT` stav volajícímu. Pokud vaše aplikace logiky neobsahuje akci odpovědi, aplikace logiky okamžitě vrátí `202 ACCEPTED` stav volajícímu.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po otevření návrháře aplikace logiky zadejte do vyhledávacího pole `http request` jako filtr. V seznamu triggery vyberte, **když se přijme požadavek HTTP** .

   ![Vybrat aktivační událost žádosti](./media/connectors-native-reqres/select-request-trigger.png)

   Aktivační událost žádosti zobrazuje tyto vlastnosti:

   ![Aktivační událost žádosti](./media/connectors-native-reqres/request-trigger.png)

   | Název vlastnosti | Název vlastnosti JSON | Povinné | Popis |
   |---------------|--------------------|----------|-------------|
   | **ADRESA URL PRO POST HTTP** | nTato | Ano | Adresa URL koncového bodu, která se generuje po uložení aplikace logiky a která se používá pro volání aplikace logiky |
   | **Schéma JSON pro tělo požadavku** | `schema` | Ne | Schéma JSON, které popisuje vlastnosti a hodnoty v textu příchozí žádosti |
   |||||

1. V poli **schématu JSON textu žádosti** můžete volitelně zadat schéma JSON, které popisuje tělo v příchozím požadavku, například:

   ![Příklad schématu JSON](./media/connectors-native-reqres/provide-json-schema.png)

   Návrhář používá toto schéma k vygenerování tokenů pro vlastnosti v žádosti. Aplikace logiky tak může analyzovat, spotřebovávat a předávat data z požadavku prostřednictvím triggeru do pracovního postupu.

   Tady je ukázkové schéma:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Když zadáte schéma JSON, Návrhář zobrazí připomenutí, které zahrne `Content-Type` hlavičku do žádosti, a nastaví tuto hodnotu hlavičky na `application/json` . Další informace najdete v tématu [zpracování typů obsahu](../logic-apps/logic-apps-content-type.md).

   ![Připomenutí, které zahrnuje hlavičku "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Toto záhlaví vypadá jako ve formátu JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Chcete-li vygenerovat schéma JSON založené na očekávané datové části (data), můžete použít nástroj, například [JSONSchema.NET](https://jsonschema.net), nebo můžete provést následující kroky:

   1. V triggeru žádosti vyberte **použít ukázkovou datovou část k vygenerování schématu**.

      ![Snímek obrazovky s vybranou možnost použít ukázkovou datovou část k vygenerování schématu](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Zadejte ukázkovou datovou část a vyberte **Hotovo**.

      ![Zadat ukázkovou datovou část k vygenerování schématu](./media/connectors-native-reqres/enter-payload.png)

      Tady je ukázková datová část:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Chcete-li ověřit, zda příchozí volání obsahuje text žádosti, který odpovídá zadanému schématu, postupujte podle těchto kroků:

   1. V záhlaví triggeru žádosti vyberte tlačítko se třemi tečkami (**...**).

   1. V nastavení triggeru zapněte **ověřování schématu** a vyberte **Hotovo**.

      Pokud text požadavku na příchozí volání neodpovídá vašemu schématu, aktivační událost vrátí `HTTP 400 Bad Request` chybu.

1. Chcete-li zadat další vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte parametry, které chcete přidat.

   | Název vlastnosti | Název vlastnosti JSON | Povinné | Popis |
   |---------------|--------------------|----------|-------------|
   | **Metoda** | `method` | Ne | Metoda, kterou musí příchozí požadavek použít k volání aplikace logiky |
   | **Relativní cesta** | `relativePath` | Ne | Relativní cesta k parametru, který adresa URL koncového bodu aplikace logiky může přijmout |
   |||||

   Tento příklad přidá vlastnost **metody** :

   ![Přidat parametr metody](./media/connectors-native-reqres/add-parameters.png)

   Vlastnost **Method** se zobrazí v triggeru, takže můžete vybrat metodu ze seznamu.

   ![Vybrat metodu](./media/connectors-native-reqres/select-method.png)

1. Nyní přidejte další akci jako další krok v pracovním postupu. V části Trigger vyberte **Další krok** , abyste mohli najít akci, kterou chcete přidat.

   Například můžete na žádost reagovat [přidáním akce odpovědi](#add-response), kterou můžete použít k vrácení vlastní odpovědi a je popsáno dále v tomto tématu.

   Vaše aplikace logiky udržuje příchozí požadavek otevřené jenom po dobu [omezeného času](../logic-apps/logic-apps-limits-and-config.md#http-limits). Za předpokladu, že pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky vrátí `504 GATEWAY TIMEOUT` volajícímu. V opačném případě, pokud vaše aplikace logiky neobsahuje akci odpovědi, aplikace logiky okamžitě vrátí `202 ACCEPTED` odpověď volajícímu.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok vygeneruje adresu URL, která se má použít pro odeslání žádosti, která spouští aplikaci logiky. Tuto adresu URL můžete zkopírovat tak, že vyberete ikonu kopírování vedle adresy URL.

   ![Adresa URL pro použití aktivace aplikace logiky](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Pokud chcete při volání triggeru požadavku do identifikátoru URI zahrnout symbol hash nebo symbol křížku ( **#** ), použijte místo toho tuto kódovanou verzi: `%25%23`

1. K otestování aplikace logiky odešlete požadavek HTTP na vygenerovanou adresu URL.

   Pomocí nástroje, jako je například [post](https://www.getpostman.com/) , můžete například poslat požadavek HTTP. Další informace o základní definici JSON triggeru a o tom, jak zavolat tuto aktivační událost, najdete v těchto tématech: [typ triggeru žádosti](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a [volání, Trigger nebo vnoření pracovních postupů pomocí koncových bodů HTTP v Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Další informace o zabezpečení autorizace a šifrování příchozích volání do vaší aplikace logiky, jako je například TLS ( [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), dříve označované jako SSL (Secure SOCKETS Layer) (SSL), [Azure Active Directory otevřené ověřování (Azure AD OAuth)](../active-directory/develop/index.yml), vystavení vaší aplikace logiky pomocí služby Azure API Management nebo omezení IP adres, které pocházejí z příchozích volání, najdete v tématu [zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="trigger-outputs"></a>Výstupy triggeru

Zde jsou další informace o výstupech z triggeru požadavku:

| Název vlastnosti JSON | Datový typ | Popis |
|--------------------|-----------|-------------|
| `headers` | Objekt | Objekt JSON, který popisuje hlavičky z požadavku |
| `body` | Objekt | Objekt JSON, který popisuje obsah těla žádosti |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Přidat akci odpovědi

Když použijete Trigger žádosti pro zpracování příchozích požadavků, můžete vytvořit model odpovědi a odeslat výsledky datové části zpět volajícímu pomocí předdefinované [Akce odpovědi](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action). Akci odpovědi můžete použít *jenom* s triggerem požadavku. Tato kombinace s akcí triggeru a odpovědi žádosti vytvoří [vzor požadavků a odpovědí](https://en.wikipedia.org/wiki/Request%E2%80%93response). S výjimkou uvnitř smyček foreach a do smyček a paralelních větví můžete přidat akci odpovědi kdekoli v pracovním postupu.

> [!IMPORTANT]
> Pokud akce odpovědi zahrnuje tyto hlavičky, Logic Apps tyto hlavičky z vygenerované zprávy odpovědi odebrat bez zobrazení upozornění nebo chyby:
>
> * `Allow`
> * `Content-*` s těmito výjimkami: `Content-Disposition` , `Content-Encoding` a `Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> I když Logic Apps nebrání v ukládání aplikací logiky, které mají akci s odpovědí s těmito záhlavími, Logic Apps tyto hlavičky ignorují.

1. V návrháři aplikace logiky v kroku, kam chcete přidat akci odpovědi, vyberte **Nový krok**.

   Například pomocí triggeru požadavku z výše uvedeného:

   ![Přidat nový krok](./media/connectors-native-reqres/add-response.png)

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V části **zvolit akci** zadejte do vyhledávacího pole `response` jako filtr a vyberte akci **odpovědi** .

   ![Vybrat akci odpovědi](./media/connectors-native-reqres/select-response-action.png)

   Aktivační událost žádosti je v tomto příkladu sbalená pro zjednodušení.

1. Přidejte všechny hodnoty, které jsou požadovány pro zprávu odpovědi.

   V některých polích se kliknutím do příslušných polí otevře seznam dynamického obsahu. Pak můžete vybrat tokeny, které reprezentují dostupné výstupy z předchozích kroků v pracovním postupu. Vlastnosti ze schématu zadaného v předchozím příkladu se nyní zobrazí v seznamu dynamického obsahu.

   Například pro pole **záhlaví** zadejte `Content-Type` jako název klíče a nastavte hodnotu klíče na, `application/json` jak je uvedeno dříve v tomto tématu. V poli **tělo** můžete vybrat výstup těla aktivační události ze seznamu dynamického obsahu.

   ![Podrobnosti o akci odpovědi](./media/connectors-native-reqres/response-details.png)

   Chcete-li zobrazit záhlaví ve formátu JSON, vyberte možnost **Přepnout do textového zobrazení**.

   ![Záhlaví – přepnout na textové zobrazení](./media/connectors-native-reqres/switch-to-text-view.png)

   Zde jsou další informace o vlastnostech, které lze nastavit v akci reakce.

   | Název vlastnosti | Název vlastnosti JSON | Povinné | Popis |
   |---------------|--------------------|----------|-------------|
   | **Stavový kód** | `statusCode` | Ano | Stavový kód, který se má vrátit v odpovědi |
   | **Hlavičky** | `headers` | Ne | Objekt JSON, který popisuje jednu nebo více hlaviček, které mají být zahrnuty do odpovědi |
   | **Text** | `body` | Ne | Tělo odpovědi |
   |||||

1. Chcete-li zadat další vlastnosti, jako je například schéma JSON pro tělo odpovědi, otevřete seznam **Přidat nový parametr** a vyberte parametry, které chcete přidat.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* [Zabezpečený přístup a přístup k datům pro příchozí volání aktivačních událostí na základě požadavků](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Konektory pro Logic Apps](../connectors/apis-list.md)
