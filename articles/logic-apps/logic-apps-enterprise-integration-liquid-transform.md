---
title: Převést data JSON s kapaliny transformací - Azure Logic Apps | Microsoft Docs
description: Vytvořte transformací nebo mapy pro pokročilé transformace JSON pomocí Logic Apps a kapaliny šablony.
services: logic-apps
documentationcenter: ''
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 42a102c9b2663380a93d56cc5f8fb82abaa83b74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299507"
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Proveďte pokročilé transformace JSON s šablonu kapaliny

Azure Logic Apps podporuje základní transformace JSON prostřednictvím nativní data operace akce, jako **vytvářené** nebo **analyzovat JSON**. Pro pokročilé transformace JSON můžete použít kapaliny šablony s logic apps. 
[Kapaliny](https://shopify.github.io/liquid/) je šablona open-source jazyk pro flexibilní webové aplikace.
 
V tomto článku Další informace o použití kapaliny mapy nebo šablony, která podporuje složitější transformace JSON, jako je například iterací, řízení toky, proměnné a tak dále. Před provedením kapaliny transformaci v aplikaci logiky, je nutné zadat mapování z JSON do formátu JSON s kapaliny mapy a úložiště, které mapují ve vašem účtu integrace.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Jinak si můžete [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Základní [integrace účtu](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Vytvořit šablonu kapaliny ani mapy pro váš účet integrace

1. Vytvoření šablony kapaliny ukázka v tomto příkladu. Kapaliny Šablona definuje, jak k transformaci vstupu JSON podle postupu popsaného tady:

   ``` json
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
   > [!NOTE]
   > Pokud vaše kapaliny šablona používá [filtry](https://shopify.github.io/liquid/basics/introduction/#filters), tyto filtry musí začínat velká písmena. 

2. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

3. V hlavní nabídce Azure, zvolte **všechny prostředky**. 

4. Do vyhledávacího pole najděte a vyberte svůj účet integrace.

   ![Vyberte účet integrace](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Na dlaždici integrace účet vyberte **mapy**.

   ![Vyberte mapy](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Zvolte **přidat** a uveďte následující informace pro mapu:

   * **Název**: název pro mapu, která je v tomto příkladu "JsontoJsonTemplate"
   * **Typ mapování**: typ pro mapu. Pro formát JSON k transformaci JSON, je nutné vybrat **kapaliny**.
   * **Mapa**: existující kapaliny šablony nebo mapovat soubor pro transformaci, která je "SimpleJsonToJsonTemplate.liquid" v tomto příkladu. K vyhledání tohoto souboru, můžete použít nástroje pro výběr souborů.

   ![Přidání šablony kapaliny](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Přidat kapaliny akce pro transformaci JSON

1. [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Přidat [aktivační událost požadavku](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) do aplikace logiky.

3. Zvolte **+ nový krok > Přidat akci**. Do vyhledávacího pole zadejte *kapaliny*a vyberte **kapaliny - transformace JSON JSON**.

   ![Najděte a vyberte kapaliny akce](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. V **obsah** vyberte **textu** z dynamického obsahu seznamu, nebo seznam parametrů, podle toho, co se zobrazí.
  
   ![Vyberte textu](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Z **mapy** vyberte kapaliny šablony, což je "JsonToJsonTemplate" v tomto příkladu.

   ![Vyberte mapy](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Pokud je seznam prázdný, aplikace logiky není pravděpodobně propojený s vaším účtem integrace. 
   Odkaz svou aplikaci logiky integrace účet, který má kapaliny šablony nebo mapy, postupujte takto:

   1. V nabídce aplikace logiky, vyberte **nastavení pracovních postupů**.
   2. Z **vyberte účet, integrace** seznam, vyberte svůj účet integrace a zvolit **Uložit**.

   ![Aplikace logiky odkaz na účet integrace](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testování aplikace logiky

POST vstupu JSON do aplikace logiky z [Postman](https://www.getpostman.com/postman) nebo podobného nástroje. Transformovaný výstup JSON ze svou aplikaci logiky vypadá v tomto příkladu:
  
![Příklad výstupu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Další příklady kapaliny akce
Kapaliny není omezen na pouze JSON transformace. Zde jsou další akce k dispozici transformace, používající kapaliny.

* Transformace na text JSON
  
  Tady je kapaliny šablony použité v tomto příkladu:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Zde jsou ukázka vstup a výstup:
  
   ![Příklad výstupu JSON na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Převést XML na JSON
  
  Tady je kapaliny šablony použité v tomto příkladu:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Zde jsou ukázka vstup a výstup:

   ![Příklad výstupu XML do formátu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformace na text XML
  
  Tady je kapaliny šablony použité v tomto příkladu:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Zde jsou ukázka vstup a výstup:

   ![Příklad výstupu XML na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Další postup

* [Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")  
* [Další informace o mapování](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o enterprise integrace mapy")  

