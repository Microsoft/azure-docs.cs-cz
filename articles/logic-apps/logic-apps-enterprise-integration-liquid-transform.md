---
title: Převádí data JSON s Liquid transformace – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření mapy pro pokročilé transformacích JSON pomocí Logic Apps a Liquid šablony nebo transformací
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
manager: jeconnoc
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 140c92d260ac6423127e478e304cbebcf9c42124
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054430"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Proveďte pokročilé transformacích JSON pomocí Liquid šablon v Azure Logic Apps

Základní transformacích JSON ve svých aplikacích logiky s nativní datové operace akce můžete provádět například **Compose** nebo **Parsovat JSON**. K provádění pokročilých transformací JSON, můžete vytvořit šablony nebo aplikace mapy s [Liquid](https://shopify.github.io/liquid/), což je jazyk open source šablony pro flexibilní webové aplikace. Liquid šablony umožňují definovat, jak transformovat výstup ve formátu JSON a podporuje složitější transformace, JSON, jako je počet iterací, řízení toků, proměnné a tak dále. 

Tedy před provedením Liquid transformace ve vaší aplikaci logiky, nejprve definujete ve formátu JSON pro mapování JSON s Liquid šablony a úložiště, které se mapují v účtu integrace. V tomto článku se dozvíte, jak vytvořit a použít tuto šablonu Liquid nebo mapy. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Nebo, [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Základní [účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vytvořit šablonu Liquid nebo mapu účtu integrace

1. V tomto příkladu vytvořte Ukázková šablona Liquid popsané v tomto kroku.
Pokud chcete použít v šabloně Liquid všechny filtry, ujistěte se, že se že tyto filtry začínat velká písmena. Další informace o [kapaliny filtruje](https://shopify.github.io/liquid/basics/introduction/#filters). 

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

2. Přihlaste se k webu [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure zvolte **všechny prostředky**. Do vyhledávacího pole vyhledejte a vyberte svůj účet integrace.

   ![Vyberte účet pro integraci](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  V části **součásti**vyberte **mapy**.

    ![Vyberte mapování](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Zvolte **přidat** a poskytněte tyto údaje pro mapy:

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | JsonToJsonTemplate | Název pro mapu, která je v tomto příkladu "JsonToJsonTemplate" | 
   | **Typ mapování** | **kapaliny** | Typ pro mapu. JSON na JSON transformace, musíte vybrat **liquid**. | 
   | **Mapa** | "SimpleJsonToJsonTemplate.liquid" | Existující Liquid šablony nebo mapy soubor určený pro transformaci, která je "SimpleJsonToJsonTemplate.liquid" v tomto příkladu. Tento soubor můžete použít nástroje pro výběr souborů. |
   ||| 

   ![Přidat šablonu liquid.](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Přidání Liquid akce pro transformace JSON

1. Na webu Azure Portal, postupujte podle těchto kroků [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. V návrháři aplikace logiky, přidejte [triggeru požadavku](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) do aplikace logiky.

3. Pod triggerem zvolte **nový krok**. Do vyhledávacího pole zadejte jako filtr "kapaliny" a vyberte tuto akci: **transformace JSON na JSON - kapaliny**

   ![Vyhledejte a vyberte akci Liquid.](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klikněte do **obsah** tak, aby zobrazil seznam dynamického obsahu a vyberte **tělo** token.
  
   ![Výběr textu](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Z **mapy** vyberte váš Liquid šablonu, která je "JsonToJsonTemplate" v tomto příkladu.

   ![Vyberte mapování](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Pokud je prázdný seznam mapování, pravděpodobně vaše aplikace logiky není propojený s vaším účtem integrace. 
   Odkaz na tento účet, který má Liquid šablony nebo mapy aplikace logiky, postupujte takto:

   1. V nabídce aplikace logiky, vyberte **nastavení pracovního postupu**.

   2. Z **vyberte účtu pro integraci** seznamu, vyberte svůj účet integrace a zvolte **Uložit**.

     ![Odkaz na účet integrace aplikace logiky](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Publikovat vstupu JSON do aplikace logiky z [Postman](https://www.getpostman.com/postman) nebo něco podobného. Transformovaný výstup JSON vaší aplikace logiky bude vypadat jako v tomto příkladu:
  
![Příklad výstupu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Další příklady akcí Liquid.
Kapaliny není omezena pouze na pouze transformacích JSON. Tady jsou další dostupné transformace akce, které používají kapaliny.

* Převede JSON na text
  
  Tady je Liquid Šablona použitá v tomto příkladu:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Tady je ukázkový vstup a výstup:
  
   ![Příklad výstupu JSON na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Převést XML na JSON
  
  Tady je Liquid Šablona použitá v tomto příkladu:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Tady je ukázkový vstup a výstup:

   ![Ukázkový výstup XML na JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformujte XML na text
  
  Tady je Liquid Šablona použitá v tomto příkladu:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Tady je ukázkový vstup a výstup:

   ![Ukázkový výstup XML na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Další postup

* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")  
* [Další informace o mapování](../logic-apps/logic-apps-enterprise-integration-maps.md "přečtěte si víc o podnikové integrace map")  

