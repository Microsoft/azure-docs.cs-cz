---
title: Převod dat JSON pomocí kapalinových transformací – Azure Logic Apps | Microsoft Docs
description: Vytváření transformací nebo mapování pro pokročilé transformace JSON pomocí Logic Apps a kapalné šablony
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 203c57a2755a3287566a774e2878a87b847337b9
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900663"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Provádění pokročilých transformací JSON pomocí tekutých šablon v Azure Logic Apps

V Logic Apps můžete provádět základní transformace JSON s akcemi nativních operací s daty, jako je například **Vytvoření** nebo **Analýza JSON**. K provádění pokročilých transformací JSON můžete vytvářet šablony nebo mapy pomocí [tekutého](https://shopify.github.io/liquid/), což je open source jazyk šablony pro flexibilní webové aplikace. Šablona Liquid definuje, jak transformovat výstup JSON a podporuje složitější transformace JSON, jako jsou iterace, řídicí toky, proměnné a tak dále. 

Předtím, než můžete v aplikaci logiky provést transformaci kapalin, je nutné nejprve definovat mapování JSON na JSON s šablonou Liquid a uložit tuto mapu v účtu integrace. V tomto článku se dozvíte, jak vytvořit a použít tuto šablonu nebo mapu kapalné. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Případně si [můžete zaregistrovat předplatné s](https://azure.microsoft.com/pricing/purchase-options/)průběžnými platbami.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Účet Basic [Integration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Základní znalosti o [jazyce kapalné šablony.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vytvořit šablonu nebo mapu pro svůj účet pro integraci

1. V tomto příkladu vytvořte ukázkovou kapalinovou šablonu popsanou v tomto kroku. V šabloně kapalin můžete použít [kapalné filtry](https://shopify.github.io/liquid/basics/introduction/#filters), které používají [DotLiquid](https://dotliquidmarkup.org/) a C# konvence pojmenování. 

   > [!NOTE]
   > Ujistěte se, že názvy filtrů používají pro šablonu *velká písmena ve větě* . V opačném případě nebudou filtry fungovat.

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

2. Přihlaste se k webu [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure vyberte **všechny prostředky**. Ve vyhledávacím poli vyhledejte a vyberte účet pro integraci.

   ![Vybrat účet pro integraci](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  V části **součásti**vyberte **mapy**.

    ![Vybrat mapy](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Klikněte na tlačítko **Přidat** a zadejte následující údaje pro mapu:

   | Vlastnost | Value | Popis | 
   |----------|-------|-------------|
   | **Název** | JsonToJsonTemplate | Název pro mapu, která je v tomto příkladu "JsonToJsonTemplate" | 
   | **Typ mapy** | **ukazuje** | Typ pro mapu. Pro transformaci JSON na JSON musíte vybrat možnost **Liquid**. | 
   | **Mapy** | "SimpleJsonToJsonTemplate. Liquid" | Existující šablona nebo soubor mapování v kapalném formátu, který je použit pro transformaci, která je v tomto příkladu "SimpleJsonToJsonTemplate. Liquid". K vyhledání tohoto souboru můžete použít nástroj pro výběr souborů. |
   ||| 

   ![Přidat kapalinovou šablonu](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Přidat kapalinovou akci pro transformaci JSON

1. V Azure Portal postupujte podle těchto kroků a [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. V návrháři aplikace logiky přidejte [Trigger žádosti](../connectors/connectors-native-reqres.md#add-request) do vaší aplikace logiky.

3. V části Trigger vyberte **Nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "Liquid" a vyberte tuto akci: **Transformovat JSON na JSON – Liquid**

   ![Najít a vybrat kapalinovou akci](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klikněte do pole **obsah** , aby se zobrazil seznam dynamického obsahu, a vyberte token **textu** .
  
   ![Vybrat tělo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. V seznamu **Mapa** vyberte v tomto příkladu šablonu pro kapalinu, která je "JsonToJsonTemplate".

   ![Vybrat mapu](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Pokud je seznam mapování prázdný, pravděpodobně není vaše aplikace logiky propojena s vaším účtem integrace. 
   Pokud chcete propojit aplikaci logiky s účtem pro integraci, který má šablonu nebo mapu kapalin, postupujte podle těchto kroků:

   1. V nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

   2. V seznamu **Vyberte účet pro integraci** vyberte účet pro integraci a zvolte **Uložit**.

      ![Propojit aplikaci logiky s účtem pro integraci](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testování aplikace logiky

Odeslání vstupu JSON do aplikace logiky z [post](https://www.getpostman.com/postman) nebo podobného nástroje. Transformovaný výstup JSON z aplikace logiky vypadá jako v tomto příkladu:
  
![Příklad výstupu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Příklady dalších kapalných akcí
Kapalina není omezena pouze na transformace JSON. Tady jsou další dostupné akce transformace, které používají kapalná.

* Transformovat JSON na text
  
  Zde je šablona kapalin použitá v tomto příkladu:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Tady jsou ukázkové vstupy a výstupy:
  
   ![Příklad výstupu JSON na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Převést XML na JSON
  
  Zde je šablona kapalin použitá v tomto příkladu:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Tady jsou ukázkové vstupy a výstupy:

   ![Příklad výstupního souboru XML do formátu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformovat XML na text
  
  Zde je šablona kapalin použitá v tomto příkladu:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Tady jsou ukázkové vstupy a výstupy:

   ![Příklad výstupního souboru XML na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o Enterprise Integration Pack] (../logic-apps/logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack")  
* [Další informace o mapách] (../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o službě Enterprise Integration Maps")  

