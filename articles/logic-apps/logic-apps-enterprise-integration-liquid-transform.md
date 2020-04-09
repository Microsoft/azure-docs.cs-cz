---
title: Převod dat JSON pomocí tekutých transformací
description: Vytváření transformací nebo map pro pokročilé transformace JSON pomocí logic aplikací a tekuté šablony
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879169"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Provádění pokročilých transformací JSON pomocí tekutých šablon v aplikacích Azure Logic Apps

Základní transformace JSON můžete provádět ve vašich aplikacích logiky s akcemi operací nativních dat, jako je **například Compose** nebo **Parse JSON**. Chcete-li provádět pokročilé transformace JSON, můžete vytvářet šablony nebo mapy pomocí [aplikace Liquid](https://shopify.github.io/liquid/), což je jazyk šablony s otevřeným zdrojovým kódem pro flexibilní webové aplikace. Šablona Liquid definuje, jak transformovat výstup JSON a podporuje složitější transformace JSON, jako jsou iterace, řídicí toky, proměnné a tak dále.

Před provedením transformace Liquid v aplikaci logiky, musíte nejprve definovat JSON na JSON mapování s liquid šablony a uložit, že mapa ve vašem účtu integrace. Tento článek ukazuje, jak vytvořit a používat tuto tekutou šablonu nebo mapu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Základní [integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Základní znalosti o [jazyce tekuté šablony](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vytvoření tekuté šablony nebo mapy pro váš integrační účet

1. V tomto příkladu vytvořte ukázkovou šablonu Liquid popsanou v tomto kroku. V šabloně Liquid můžete použít [tekuté filtry](https://shopify.github.io/liquid/basics/introduction/#filters), které používají konvence pojmenování [DotLiquid](https://github.com/dotliquid/dotliquid) a C#.

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

1. Na [webu Azure Portal](https://portal.azure.com)zadejte `integration accounts`a vyberte z vyhledávacího pole Azure účty **integrace**.

   ![Najít "Integrační účty"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Najděte a vyberte svůj účet integrace.

   ![Vybrat účet integrace](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. V podokně **Přehled** vyberte v části **Komponenty položku** **Mapy**.

    ![Vybrat dlaždici Mapy](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. V podokně **Mapy** vyberte **Přidat** a uveďte pro mapu tyto podrobnosti:

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | `JsonToJsonTemplate` | Název mapy, která je "JsonToJsonTemplate" v tomto příkladu | 
   | **Typ mapy** | **Kapaliny** | Typ mapy. Pro transformaci JSON na JSON musíte vybrat **kapalinu**. | 
   | **Mapa** | `SimpleJsonToJsonTemplate.liquid` | Existující tekutá šablona nebo mapový soubor, který se má použít pro transformaci, což je "SimpleJsonToJsonTemplate.liquid" v tomto příkladu. Chcete-li najít tento soubor, můžete použít výběr souborů. Omezení velikosti mapy najdete v tématu [Omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Přidat tekutou šablonu](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Přidání akce Kapalina pro transformaci JSON

1. Na webu Azure Portal vytvořte [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)pomocí těchto kroků .

1. V Návrháři aplikace logiky přidejte [aktivační událost požadavku](../connectors/connectors-native-reqres.md#add-request) do aplikace logiky.

1. Pod aktivační událostí zvolte **Nový krok**. Do vyhledávacího pole `liquid` zadejte jako filtr a vyberte tuto akci: **Transformovat JSON na JSON - Liquid**

   ![Najít a vybrat akci Kapalina](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Otevřete seznam **Mapa** a vyberte šablonu Liquid, která je v tomto příkladu "JsonToJsonTemplate".

   ![Vybrat mapu](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Pokud je seznam map prázdný, s největší pravděpodobností vaše aplikace logiky není propojena s účtem integrace. 
   Chcete-li propojit aplikaci logiky s účtem integrace, který má šablonu nebo mapu Liquid, postupujte takto:

   1. V nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

   1. V seznamu **Vybrat účet integrace** vyberte účet integrace a vyberte **Uložit**.

      ![Propojit aplikaci logiky s účtem integrace](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Nyní přidejte vlastnost **Content** do této akce. Otevřete seznam **Přidat nový parametr** a vyberte **Obsah**.

   ![Přidání vlastnosti Obsah do akce](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Chcete-li nastavit hodnotu vlastnosti **Obsah,** klepněte do pole **Obsah,** aby se zobrazil seznam dynamického obsahu. Vyberte **body** token, který představuje výstup obsahu těla z aktivační události.

   ![Vyberte token "Body" pro hodnotu vlastnosti "Obsah".](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Po dokončení bude akce vypadat jako v tomto příkladu:

   ![Dokončena akce "Transformace JSON na JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

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

