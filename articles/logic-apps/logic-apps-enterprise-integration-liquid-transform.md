---
title: Převod dat JSON pomocí tekutých transformací
description: Vytváření transformací nebo map pro pokročilé transformace JSON pomocí logic aplikací a tekuté šablony
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: fb9f9cfdba07ebe0bc5800def6d93950869e9727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456640"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Provádění pokročilých transformací JSON pomocí tekutých šablon v aplikacích Azure Logic Apps

Základní transformace JSON můžete provádět ve vašich aplikacích logiky s akcemi operací nativních dat, jako je **například Compose** nebo **Parse JSON**. Chcete-li provádět pokročilé transformace JSON, můžete vytvářet šablony nebo mapy pomocí [aplikace Liquid](https://shopify.github.io/liquid/), což je jazyk šablony s otevřeným zdrojovým kódem pro flexibilní webové aplikace. Šablona Liquid definuje, jak transformovat výstup JSON a podporuje složitější transformace JSON, jako jsou iterace, řídicí toky, proměnné a tak dále. 

Před provedením transformace Liquid v aplikaci logiky, musíte nejprve definovat JSON na JSON mapování s liquid šablony a uložit, že mapa ve vašem účtu integrace. Tento článek ukazuje, jak vytvořit a používat tuto tekutou šablonu nebo mapu. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Nebo [si zaregistrujte předplatné s průběžným platbou](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Základní [integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Základní znalosti o [jazyce tekuté šablony](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vytvoření tekuté šablony nebo mapy pro váš integrační účet

1. V tomto příkladu vytvořte ukázkovou šablonu Liquid popsanou v tomto kroku. V šabloně Liquid můžete použít [tekuté filtry](https://shopify.github.io/liquid/basics/introduction/#filters), které používají konvence pojmenování [DotLiquid](https://dotliquidmarkup.org/) a C#. 

   > [!NOTE]
   > Ujistěte se, že názvy filtrů používají v šabloně *velká a malá písmena.* V opačném případě nebudou filtry fungovat. Mapy mají také [omezení velikosti souboru](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

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

2. Přihlaste se k [portálu Azure](https://portal.azure.com). V hlavní nabídce Azure vyberte **Všechny prostředky**. Ve vyhledávacím poli vyhledejte a vyberte svůj účet integrace.

   ![Vybrat účet integrace](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  V části **Komponenty**vyberte **Mapy**.

    ![Vybrat mapy](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Zvolte **Přidat** a uveďte pro mapu tyto podrobnosti:

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | Šablona JsonToJson | Název mapy, která je "JsonToJsonTemplate" v tomto příkladu | 
   | **Typ mapy** | **Kapaliny** | Typ mapy. Pro transformaci JSON na JSON musíte vybrat **kapalinu**. | 
   | **Mapa** | "SimpleJsonToJsonTemplate.liquid" | Existující tekutá šablona nebo mapový soubor, který se má použít pro transformaci, což je "SimpleJsonToJsonTemplate.liquid" v tomto příkladu. Chcete-li najít tento soubor, můžete použít výběr souborů. Omezení velikosti mapy najdete v tématu [Omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Přidat tekutou šablonu](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Přidání akce Kapalina pro transformaci JSON

1. Na webu Azure Portal vytvořte [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)pomocí těchto kroků .

2. V Návrháři aplikace logiky přidejte [aktivační událost požadavku](../connectors/connectors-native-reqres.md#add-request) do aplikace logiky.

3. Pod aktivační událostí zvolte **Nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "liquid" a vyberte tuto akci: **Transformujte JSON na JSON - Liquid**

   ![Najít a vybrat akci Kapalina](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klikněte do pole **Obsah,** aby se zobrazil seznam dynamického obsahu, a vyberte token **Body.**
  
   ![Vybrat tělo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Ze seznamu **Mapa** vyberte šablonu Liquid, která je v tomto příkladu "JsonToJsonTemplate".

   ![Vybrat mapu](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Pokud je seznam map prázdný, s největší pravděpodobností vaše aplikace logiky není propojena s účtem integrace. 
   Chcete-li propojit aplikaci logiky s účtem integrace, který má šablonu nebo mapu Liquid, postupujte takto:

   1. V nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

   2. V seznamu **Vybrat účet integrace** vyberte účet integrace a zvolte **Uložit**.

      ![Propojit aplikaci logiky s účtem integrace](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testování aplikace logiky

Zaúčtovat vstup JSON do aplikace logiky z [Postman](https://www.getpostman.com/postman) nebo podobný nástroj. Transformovaný výstup JSON z aplikace logiky vypadá takto:
  
![Příklad výstupu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Další příklady akcí Liquid
Kapalina není omezena pouze na transformace JSON. Zde jsou další dostupné transformační akce, které používají Liquid.

* Transformace json u textu
  
  Zde je šablona Liquid použitá pro tento příklad:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Zde jsou ukázkové vstupy a výstupy:
  
   ![Příklad výstupu JSON na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformace XML na JSON
  
  Zde je šablona Liquid použitá pro tento příklad:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Zde jsou ukázkové vstupy a výstupy:

   ![Příklad výstupního XML na JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformace XML na text
  
  Zde je šablona Liquid použitá pro tento příklad:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Zde jsou ukázkové vstupy a výstupy:

   ![Příklad výstupního XML na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o balíčku Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack")  
* [Další informace o mapách](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o mapách podnikové integrace")  

