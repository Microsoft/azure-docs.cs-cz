---
title: Reakce na požadavky HTTP – Azure Logic Apps
description: Reakce na události v reálném čase přes protokol HTTP pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewers: klam, LADocs
manager: carmonm
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.topic: article
ms.date: 09/06/2019
tags: connectors
ms.openlocfilehash: 07f143b261d0cff9eba0d4b1803753446c311818
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914339"
---
# <a name="respond-to-http-requests-by-using-azure-logic-apps"></a>Reakce na požadavky HTTP pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované akce triggeru a odpovědi na žádost můžete vytvářet automatizované úlohy a pracovní postupy, které přijmou a reagují na požadavky HTTP v reálném čase. Můžete mít například aplikaci logiky:

* Odpověď na požadavek HTTP na data v místní databázi.
* Aktivuje pracovní postup, když dojde k externí události Webhooku.
* Volání aplikace logiky v jiné aplikaci logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [Logic Apps](../logic-apps/logic-apps-overview.md) Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-a-request-trigger"></a>Přidat aktivační událost žádosti

Tato integrovaná aktivační událost vytvoří ručně koncový bod, který může přijmout příchozí požadavek HTTP. Když dojde k této události, Trigger se aktivuje a spustí aplikaci logiky. Další informace o základní definici JSON triggeru a o tom, jak zavolat tuto aktivační událost, najdete v tématu [typ triggeru žádosti](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a [pracovní postupy volání, triggeru nebo vnoření pracovních postupů pomocí koncových bodů HTTP v Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření prázdné aplikace logiky

1. Po otevření návrháře aplikace logiky zadejte do vyhledávacího pole "požadavek HTTP" jako filtr. V seznamu triggery vyberte aktivační událost **při přijetí požadavku HTTP** , což je první krok v pracovním postupu aplikace logiky.

   ![Výběr triggeru požadavku HTTP](./media/connectors-native-reqres/select-request-trigger.png)

   Aktivační událost žádosti zobrazuje tyto vlastnosti:

   ![Aktivační událost žádosti](./media/connectors-native-reqres/request-trigger.png)

   | Název vlastnosti | Název vlastnosti JSON | Požadováno | Popis |
   |---------------|--------------------|----------|-------------|
   | **ADRESA URL PRO POST HTTP** | nTato | Ano | Adresa URL koncového bodu, která se generuje po uložení aplikace logiky a která se používá pro volání aplikace logiky |
   | **Schéma JSON pro tělo požadavku** | `schema` | Ne | Schéma JSON, které popisuje vlastnosti a hodnoty v textu příchozí žádosti HTTP |
   |||||

1. V poli **schématu JSON textu žádosti** můžete volitelně zadat schéma JSON, které popisuje tělo požadavku HTTP v příchozím požadavku, například:

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

   Když zadáte schéma JSON, Návrhář zobrazí připomenutí, které zahrne `Content-Type` hlavičku do žádosti, a nastaví tuto hodnotu hlavičky na. `application/json` Další informace najdete v tématu [zpracování typů obsahu](../logic-apps/logic-apps-content-type.md).

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

   | Název vlastnosti | Název vlastnosti JSON | Požadováno | Popis |
   |---------------|--------------------|----------|-------------|
   | **– Metoda** | `method` | Ne | Metoda, kterou musí příchozí požadavek použít k volání aplikace logiky |
   | **Relativní cesta** | `relativePath` | Ne | Relativní cesta k parametru, který adresa URL koncového bodu aplikace logiky může přijmout |
   |||||

   Tento příklad přidá vlastnost **metody** :

   ![Přidat parametr metody](./media/connectors-native-reqres/add-parameters.png)

   Vlastnost **Method** se zobrazí v triggeru, takže můžete vybrat metodu ze seznamu.

   ![Vybrat metodu](./media/connectors-native-reqres/select-method.png)

1. Nyní přidejte další akci jako další krok v pracovním postupu. V části Trigger vyberte **Další krok** , abyste mohli najít akci, kterou chcete přidat.

   Například můžete na žádost reagovat [přidáním akce odpovědi](#add-response), kterou můžete použít k vrácení vlastní odpovědi a je popsáno dále v tomto tématu.

   Vaše aplikace logiky udržuje příchozí požadavek otevřené jenom za jednu minutu. Za předpokladu, že pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky `504 GATEWAY TIMEOUT` vrátí volajícímu. V opačném případě, pokud vaše aplikace logiky neobsahuje akci odpovědi, aplikace logiky okamžitě `202 ACCEPTED` vrátí odpověď volajícímu.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. 

   Tento krok vygeneruje adresu URL, která se má použít pro odeslání žádosti, která spouští aplikaci logiky. Tuto adresu URL můžete zkopírovat tak, že vyberete ikonu kopírování vedle adresy URL.

   ![Adresa URL pro použití aktivace aplikace logiky](./media/connectors-native-reqres/generated-url.png)

1. Pokud chcete aktivovat aplikaci logiky, odešlete příspěvek HTTP na vygenerovanou adresu URL. Například můžete použít nástroj, jako je například [post](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Výstupy triggeru

Zde jsou další informace o výstupech z triggeru požadavku:

| Název vlastnosti JSON | Datový typ | Popis |
|--------------------|-----------|-------------|
| `headers` | Object | Objekt JSON, který popisuje hlavičky z požadavku |
| `body` | Object | Objekt JSON, který popisuje obsah těla žádosti |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Přidat akci odpovědi

Akci reakce můžete použít ke reakci na datovou část (data) na příchozí požadavek HTTP, ale jenom v aplikaci logiky, která se aktivuje požadavkem HTTP. Akci odpovědi můžete přidat kdykoli ve svém pracovním postupu. Další informace o základní definici JSON pro tuto aktivační událost najdete v tématu [typ akce odpovědi](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Vaše aplikace logiky udržuje příchozí požadavek otevřené jenom za jednu minutu. Za předpokladu, že pracovní postup aplikace logiky obsahuje akci odpovědi, pokud aplikace logiky nevrátí odpověď po uplynutí této doby, aplikace logiky `504 GATEWAY TIMEOUT` vrátí volajícímu. V opačném případě, pokud vaše aplikace logiky neobsahuje akci odpovědi, aplikace logiky okamžitě `202 ACCEPTED` vrátí odpověď volajícímu.

1. V návrháři aplikace logiky v kroku, kam chcete přidat akci odpovědi, vyberte **Nový krok**.

   Například pomocí triggeru požadavku z výše uvedeného:

   ![Přidat nový krok](./media/connectors-native-reqres/add-response.png)

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole jako filtr "Response" a vyberte akci **odpovědi** .

   ![Vybrat akci odpovědi](./media/connectors-native-reqres/select-response-action.png)

   Aktivační událost žádosti je v tomto příkladu sbalená pro zjednodušení.

1. Přidejte všechny hodnoty, které jsou požadovány pro zprávu odpovědi. 

   V některých polích se kliknutím do příslušných polí otevře seznam dynamického obsahu. Pak můžete vybrat tokeny, které reprezentují dostupné výstupy z předchozích kroků v pracovním postupu. Vlastnosti ze schématu zadaného v předchozím příkladu se nyní zobrazí v seznamu dynamického obsahu.

   Například pro pole `Content-Type` **záhlaví** zadejte jako název klíče a nastavte hodnotu klíče na `application/json` , jak je uvedeno dříve v tomto tématu. V poli **tělo** můžete vybrat výstup těla aktivační události ze seznamu dynamického obsahu.

   ![Podrobnosti o akci odpovědi](./media/connectors-native-reqres/response-details.png)

   Chcete-li zobrazit záhlaví ve formátu JSON, vyberte možnost **Přepnout do textového zobrazení**.

   ![Záhlaví – přepnout na textové zobrazení](./media/connectors-native-reqres/switch-to-text-view.png)

   Zde jsou další informace o vlastnostech, které lze nastavit v akci reakce. 

   | Název vlastnosti | Název vlastnosti JSON | Požadováno | Popis |
   |---------------|--------------------|----------|-------------|
   | **Stavový kód** | `statusCode` | Ano | Stavový kód protokolu HTTP, který se má vrátit v odpovědi |
   | **Záhlaví** | `headers` | Ne | Objekt JSON, který popisuje jednu nebo více hlaviček, které mají být zahrnuty do odpovědi |
   | **Text** | `body` | Ne | Tělo odpovědi |
   |||||

1. Chcete-li zadat další vlastnosti, jako je například schéma JSON pro tělo odpovědi, otevřete seznam **Přidat nový parametr** a vyberte parametry, které chcete přidat.

1. Až budete hotovi, uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. 

## <a name="next-steps"></a>Další postup

* [Konektory pro Logic Apps](../connectors/apis-list.md)