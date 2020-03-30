---
title: Příjem hovorů a odpovídání na ně pomocí protokolu HTTPS
description: Zpracování příchozích požadavků HTTPS z externích služeb pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: d65b81f18d4dcb0ee97a21a7edec885e308bd8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297287"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Příjem a reakce na příchozí požadavky HTTPS v Aplikacích Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované akce aktivační události požadavku nebo odpovědi můžete vytvářet automatizované úlohy a pracovní postupy, které přijímají příchozí požadavky HTTPS a reagují na ně. Můžete mít například aplikaci logiky:

* Příjem a odpověď na požadavek HTTPS na data v místní databázi.
* Aktivuj pracovní postup, když dojde k externí události webhooku.
* Příjem a odpověď na volání HTTPS z jiné aplikace logiky.

> [!NOTE]
> Aktivační událost Požadavku podporuje *pouze* zabezpečení transportní vrstvy (TLS) 1.2 pro příchozí volání. Odchozí hovory nadále podporují TLS 1.0, 1.1 a 1.2. Další informace naleznete [v tématu Řešení problému TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Pokud se zobrazí chyby handshake SSL, ujistěte se, že používáte TLS 1.2. Pro příchozí hovory jsou zde podporované šifrovací sady:
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

* Předplatné Azure. Pokud nemáte předplatné, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [aplikacích logiky](../logic-apps/logic-apps-overview.md). Pokud s aplikacemi logiky teprve začínáte, přečtěte si, [jak vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Přidat aktivační událost požadavku

Tato vestavěná aktivační událost vytvoří ručně volatelný koncový bod HTTPS, který může přijímat *pouze* příchozí požadavky HTTPS. Když dojde k této události, aktivační událost spustí a spustí aplikaci logiky. Další informace o základní definici json aktivační události a o tom, jak tuto aktivační událost volat, najdete v tématu [Typ aktivační události požadavku](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a Pracovní postupy [volání, aktivace nebo vnoření s koncovými body HTTP v Logic Apps Azure](../logic-apps/logic-apps-http-endpoint.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po otevření aplikace logiky, ve vyhledávacím poli zadejte "http request" jako filtr. Ze seznamu aktivačních událostí vyberte aktivační **událost Při přijetí požadavku HTTP,** což je první krok v pracovním postupu aplikace logiky.

   ![Vybrat aktivační událost Požadavku](./media/connectors-native-reqres/select-request-trigger.png)

   Aktivační událost Požadavku zobrazuje tyto vlastnosti:

   ![Aktivační událost požadavku](./media/connectors-native-reqres/request-trigger.png)

   | Název vlastnosti | Název vlastnosti JSON | Požaduje se | Popis |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {none} | Ano | Adresa URL koncového bodu, která je generována po uložení aplikace logiky a používá se pro volání aplikace logiky |
   | **Požadavek tělo JSON schéma** | `schema` | Ne | Schéma JSON, které popisuje vlastnosti a hodnoty v těle příchozí požadavek |
   |||||

1. Do pole Schéma **JSON tělo požadavku** volitelně zadejte schéma JSON, které popisuje tělo v příchozí žádosti, například:

   ![Příklad schématu JSON](./media/connectors-native-reqres/provide-json-schema.png)

   Návrhář používá toto schéma ke generování tokenů pro vlastnosti v požadavku. Tímto způsobem aplikace logiky můžete analyzovat, využívat a předávat data z požadavku prostřednictvím aktivační události do pracovního postupu.

   Zde je ukázkové schéma:

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

   Když zadáte schéma JSON, návrhář zobrazí připomenutí, že `Content-Type` do požadavku zahrnete hlavičku, a nastaví tuto hodnotu záhlaví na `application/json`. Další informace naleznete v [tématu Zpracování typů obsahu](../logic-apps/logic-apps-content-type.md).

   ![Připomenutí zahrnout hlavičku Typu obsahu](./media/connectors-native-reqres/include-content-type.png)

   Zde je to, co toto záhlaví vypadá ve formátu JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Chcete-li vygenerovat schéma JSON založené na očekávané datové části (data), můžete použít nástroj, jako je [JSONSchema.net](https://jsonschema.net), nebo můžete postupovat takto:

   1. V aktivační události Požadavek vyberte **použít ukázkovou datovou část ke generování schématu**.

      ![Generovat schéma z datové části](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Zadejte datovou část vzorku a vyberte **Hotovo**.

      ![Generovat schéma z datové části](./media/connectors-native-reqres/enter-payload.png)

      Zde je ukázková datová část:

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

   | Název vlastnosti | Název vlastnosti JSON | Požaduje se | Popis |
   |---------------|--------------------|----------|-------------|
   | **Metoda** | `method` | Ne | Metoda, která příchozí požadavek musí použít k volání aplikace logiky |
   | **Relativní cesta** | `relativePath` | Ne | Relativní cesta pro parametr, který může přijmout adresa URL koncového bodu aplikace logiky |
   |||||

   Tento příklad přidá **vlastnost Method:**

   ![Přidat parametr Metody](./media/connectors-native-reqres/add-parameters.png)

   Vlastnost **Method** se zobrazí v aktivační události, takže můžete vybrat metodu ze seznamu.

   ![Vybrat metodu](./media/connectors-native-reqres/select-method.png)

1. Nyní přidejte další akci jako další krok v pracovním postupu. Pod aktivační událostí vyberte **Další krok,** abyste našli akci, kterou chcete přidat.

   Na požadavek můžete například odpovědět [přidáním akce odpovědi](#add-response), kterou můžete použít k vrácení vlastní odpovědi a je popsána dále v tomto tématu.

   Vaše aplikace logiky udržuje příchozí požadavek otevřený pouze po dobu jedné minuty. Za předpokladu, že váš pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky vrátí `504 GATEWAY TIMEOUT` volajícímu. V opačném případě pokud vaše aplikace logiky neobsahuje akci `202 ACCEPTED` odpovědi, aplikace logiky okamžitě vrátí odpověď volajícímu.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. 

   Tento krok generuje adresu URL pro odeslání požadavku, který aktivuje aplikaci logiky. Chcete-li tuto adresu URL zkopírovat, vyberte ikonu kopírování vedle adresy URL.

   ![Adresa URL, která se aktivuje spouštění aplikace logiky](./media/connectors-native-reqres/generated-url.png)

1. Chcete-li aktivovat aplikaci logiky, odešlete http post na vygenerovanou adresu URL. Můžete například použít nástroj, například [Pošťák](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Spouštěcí výstupy

Tady najdu k dalším informacím o výstupech ze spouště požadavku:

| Název vlastnosti JSON | Datový typ | Popis |
|--------------------|-----------|-------------|
| `headers` | Objekt | Objekt JSON, který popisuje záhlaví z požadavku |
| `body` | Objekt | Objekt JSON, který popisuje obsah těla z požadavku |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Přidat akci Odpověď

Akci Odpověď můžete použít k odpovědi s datovou částí (data) na příchozí požadavek HTTPS, ale pouze v aplikaci logiky, která se aktivuje požadavek HTTPS. Akci Odpověď můžete přidat kdykoli v pracovním postupu. Další informace o základní definici JSON pro tuto aktivační událost naleznete v [tématu Typ akce Odpověď](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Vaše aplikace logiky udržuje příchozí požadavek otevřený pouze po dobu jedné minuty. Za předpokladu, že váš pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky vrátí `504 GATEWAY TIMEOUT` volajícímu. V opačném případě pokud vaše aplikace logiky neobsahuje akci `202 ACCEPTED` odpovědi, aplikace logiky okamžitě vrátí odpověď volajícímu.

> [!IMPORTANT]
> Pokud akce odpověď obsahuje tato záhlaví, aplikace Logic Apps odebere tato záhlaví z generované zprávy odpovědi bez zobrazení upozornění nebo chyby:
>
> * `Allow`
> * `Content-*`s těmito `Content-Disposition`výjimkami: , `Content-Encoding`a`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Přestože aplikace Logika nezastaví ukládání aplikací logiky, které mají akci Odpověď s těmito záhlavími, Logic Apps ignoruje tyto záhlaví.

1. V Návrháři aplikace logiky vyberte v kroku, kam chcete přidat akci Odpověď, **nový krok**.

   Například pomocí request trigger z předchozí:

   ![Přidat nový krok](./media/connectors-native-reqres/add-response.png)

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. V části **Zvolte akci**zadejte do vyhledávacího pole jako filtr "odpověď" a vyberte akci **Odpověď.**

   ![Výběr akce Odpověď](./media/connectors-native-reqres/select-response-action.png)

   Požadavek aktivační událost je sbaleno v tomto příkladu pro jednoduchost.

1. Přidejte všechny hodnoty, které jsou požadovány pro zprávu odpovědi. 

   V některých polích se kliknutím na ně otevřete seznam dynamického obsahu. Potom můžete vybrat tokeny, které představují dostupné výstupy z předchozích kroků v pracovním postupu. Vlastnosti ze schématu zadaného v předchozím příkladu se nyní zobrazí v seznamu dynamického obsahu.

   Například pro **záhlaví** pole, `Content-Type` zahrnout jako název klíče a nastavte `application/json` hodnotu klíče, jak je uvedeno výše v tomto tématu. Pro pole **Tělo** můžete vybrat výstup těla aktivační události ze seznamu dynamického obsahu.

   ![Podrobnosti akce odpovědi](./media/connectors-native-reqres/response-details.png)

   Chcete-li zobrazit záhlaví ve formátu JSON, vyberte **Přepnout do textového zobrazení**.

   ![Záhlaví – přepnutí do textového zobrazení](./media/connectors-native-reqres/switch-to-text-view.png)

   Zde jsou další informace o vlastnostech, které můžete nastavit v akci Odpověď. 

   | Název vlastnosti | Název vlastnosti JSON | Požaduje se | Popis |
   |---------------|--------------------|----------|-------------|
   | **Stavový kód** | `statusCode` | Ano | Stavový kód, který se má vrátit v odpovědi |
   | **Hlavičky** | `headers` | Ne | Objekt JSON, který popisuje jedno nebo více záhlaví, které mají být zahrnuty do odpovědi |
   | **Text** | `body` | Ne | Orgán pro odezvu |
   |||||

1. Chcete-li určit další vlastnosti, například schéma JSON pro tělo odpovědi, otevřete seznam **Přidat nový parametr** a vyberte parametry, které chcete přidat.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. 

## <a name="next-steps"></a>Další kroky

* [Konektory pro Logic Apps](../connectors/apis-list.md)
