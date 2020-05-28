---
title: Příjem a reakce na volání pomocí protokolu HTTPS
description: Zpracování příchozích požadavků HTTPS z externích služeb pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
tags: connectors
ms.openlocfilehash: 0dea516ea6b938b91fc4b9b833979bcecc285339
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714963"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Příjem a odpověď na příchozí požadavky HTTPS v Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované akce triggeru požadavků a odpovědí můžete vytvářet automatizované úlohy a pracovní postupy, které přijímají příchozí požadavky HTTPS a reagují na ně. Můžete mít například aplikaci logiky:

* Přijetí a odpověď na požadavek HTTPS na data v místní databázi.
* Aktivuje pracovní postup, když dojde k externí události Webhooku.
* Přijímat a reagovat na volání HTTPS z jiné aplikace logiky.

Aktivační událost žádosti podporuje [Azure Active Directory otevřené ověřování](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) pro autorizaci příchozích volání do vaší aplikace logiky. Další informace o povolení tohoto ověřování najdete v tématu [zabezpečený přístup a data v Azure Logic Apps – povolení ověřování Azure AD OAuth](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth).

> [!NOTE]
> Aktivační událost žádosti podporuje pro příchozí volání *pouze* zabezpečení TLS (Transport Layer Security) 1,2. Odchozí hovory podporují TLS 1,0, 1,1 a 1,2. Další informace najdete v tématu [řešení problému s protokolem TLS 1,0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Pokud se zobrazí chyby handshake TLS, ujistěte se, že používáte TLS 1,2. 
> V případě příchozích volání jsou zde podporované šifrovací sady:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [Logic Apps](../logic-apps/logic-apps-overview.md) Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Přidat aktivační událost žádosti

Tato integrovaná aktivační událost vytvoří ručně koncový bod HTTPS, který může přijímat *jenom* příchozí požadavky HTTPS. Když dojde k této události, Trigger se aktivuje a spustí aplikaci logiky. Další informace o základní definici JSON triggeru a o tom, jak zavolat tuto aktivační událost, najdete v tématu [typ triggeru žádosti](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a [pracovní postupy volání, triggeru nebo vnoření do koncových bodů HTTPS v Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po otevření návrháře aplikace logiky zadejte do vyhledávacího pole `http request` jako filtr. V seznamu triggery vyberte aktivační událost **při přijetí požadavku HTTP** , což je první krok v pracovním postupu aplikace logiky.

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

      ![Generovat schéma z datové části](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Zadejte ukázkovou datovou část a vyberte **Hotovo**.

      ![Generovat schéma z datové části](./media/connectors-native-reqres/enter-payload.png)

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

   Vaše aplikace logiky udržuje příchozí požadavek otevřené jenom po dobu [omezeného času](../logic-apps/logic-apps-limits-and-config.md#request-limits). Za předpokladu, že pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky vrátí `504 GATEWAY TIMEOUT` volajícímu. V opačném případě, pokud vaše aplikace logiky neobsahuje akci odpovědi, aplikace logiky okamžitě vrátí `202 ACCEPTED` odpověď volajícímu.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok vygeneruje adresu URL, která se má použít pro odeslání žádosti, která spouští aplikaci logiky. Tuto adresu URL můžete zkopírovat tak, že vyberete ikonu kopírování vedle adresy URL.

   ![Adresa URL pro použití aktivace aplikace logiky](./media/connectors-native-reqres/generated-url.png)

1. Pokud chcete aktivovat aplikaci logiky, odešlete příspěvek HTTP na vygenerovanou adresu URL.

   Můžete například použít nástroj, jako je například [post](https://www.getpostman.com/) , k odeslání HTTP POST. Pokud jste [povolili Azure Active Directory otevřete ověřování](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) pro autorizaci příchozích volání triggeru žádosti, zavolejte Trigger pomocí [adresy URL sdíleného přístupového podpisu (SAS)](../logic-apps/logic-apps-securing-a-logic-app.md#sas) nebo pomocí ověřovacího tokenu, ale nemůžete použít obojí. Ověřovací token musí určovat `Bearer` typ v autorizační hlavičce. Další informace najdete v tématu [zabezpečený přístup a data v Azure Logic Apps – přístup k aktivačním událostem založeným na požadavku](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Další informace o základní definici JSON triggeru a o tom, jak zavolat tuto aktivační událost, najdete v těchto tématech: [typ triggeru žádosti](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a [volání, Trigger nebo vnoření pracovních postupů pomocí koncových bodů HTTP v Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

### <a name="trigger-outputs"></a>Výstupy triggeru

Zde jsou další informace o výstupech z triggeru požadavku:

| Název vlastnosti JSON | Datový typ | Description |
|--------------------|-----------|-------------|
| `headers` | Objekt | Objekt JSON, který popisuje hlavičky z požadavku |
| `body` | Objekt | Objekt JSON, který popisuje obsah těla žádosti |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Přidat akci odpovědi

Akci reakce můžete použít ke reakci na datovou část (data) na příchozí požadavek HTTPS, ale jenom v aplikaci logiky, která se aktivuje požadavkem HTTPS. Akci odpovědi můžete přidat kdykoli ve svém pracovním postupu. Další informace o základní definici JSON pro tuto aktivační událost najdete v tématu [typ akce odpovědi](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Vaše aplikace logiky udržuje příchozí požadavek otevřené jenom po dobu [omezeného času](../logic-apps/logic-apps-limits-and-config.md#request-limits). Za předpokladu, že pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky vrátí `504 GATEWAY TIMEOUT` volajícímu. V opačném případě, pokud vaše aplikace logiky neobsahuje akci odpovědi, aplikace logiky okamžitě vrátí `202 ACCEPTED` odpověď volajícímu.

> [!IMPORTANT]
> Pokud akce odpovědi zahrnuje tyto hlavičky, Logic Apps tyto hlavičky z vygenerované zprávy odpovědi odebrat bez zobrazení upozornění nebo chyby:
>
> * `Allow`
> * `Content-*`s těmito výjimkami: `Content-Disposition` , `Content-Encoding` a`Content-Type`
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

1. V části **zvolit akci**zadejte do vyhledávacího pole jako filtr "Response" a vyberte akci **odpovědi** .

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

* [Konektory pro Logic Apps](../connectors/apis-list.md)
