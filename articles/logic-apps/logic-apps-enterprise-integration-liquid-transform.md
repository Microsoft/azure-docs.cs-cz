---
title: Převod dat JSON pomocí kapalinových transformací
description: Vytváření transformací nebo mapování pro pokročilé transformace JSON pomocí Logic Apps a kapalné šablony
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879169"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Provádění pokročilých transformací JSON pomocí šablon Liquid v Azure Logic Apps

V Logic Apps můžete provádět základní transformace JSON s akcemi nativních operací s daty, jako je například **Vytvoření** nebo **Analýza JSON**. K provádění pokročilých transformací JSON můžete vytvářet šablony nebo mapy pomocí [tekutého](https://shopify.github.io/liquid/), což je open source jazyk šablony pro flexibilní webové aplikace. Šablona Liquid definuje, jak transformovat výstup JSON, a podporuje složitější transformace JSON, jako jsou iterace, toky řízení, proměnné a tak dále.

Předtím, než můžete v aplikaci logiky provést transformaci kapalin, je nutné nejprve definovat mapování JSON na JSON s šablonou Liquid a uložit tuto mapu v účtu integrace. V tomto článku se dozvíte, jak vytvořit a použít tuto šablonu nebo mapu kapalné.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Účet Basic [Integration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Základní znalosti o [jazyce kapalné šablony](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vytvořit šablonu nebo mapu pro svůj účet pro integraci

1. V tomto příkladu vytvořte ukázkovou kapalinovou šablonu popsanou v tomto kroku. V šabloně kapalin můžete použít [kapalné filtry](https://shopify.github.io/liquid/basics/introduction/#filters), které používají konvence vytváření názvů v [DotLiquid](https://github.com/dotliquid/dotliquid) a C#.

   > [!NOTE]
   > Ujistěte se, že názvy filtrů používají pro šablonu *velká písmena ve větě* . V opačném případě nebudou filtry fungovat. Mapy také mají [omezení velikosti souborů](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

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

1. V [Azure Portal](https://portal.azure.com)do pole Azure Search zadejte `integration accounts` a vyberte **účty pro integraci**.

   ![Vyhledání "účtů pro integraci"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Vyhledejte a vyberte účet pro integraci.

   ![Vybrat účet pro integraci](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. V podokně **Přehled** v části **komponenty**vyberte **mapy**.

    ![Výběr dlaždice Maps](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. V podokně **mapy** vyberte **Přidat** a zadejte podrobnosti pro mapu:

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | `JsonToJsonTemplate` | Název pro mapu, která je v tomto příkladu "JsonToJsonTemplate" | 
   | **Typ mapy** | **ukazuje** | Typ pro mapu. Pro transformaci JSON na JSON musíte vybrat možnost **Liquid**. | 
   | **Mapa** | `SimpleJsonToJsonTemplate.liquid` | Existující šablona nebo soubor mapování v kapalném formátu, který je použit pro transformaci, která je v tomto příkladu "SimpleJsonToJsonTemplate. Liquid". K vyhledání tohoto souboru můžete použít nástroj pro výběr souborů. Omezení velikosti mapování najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Přidat kapalinovou šablonu](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Přidat kapalinovou akci pro transformaci JSON

1. V Azure Portal postupujte podle těchto kroků a [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. V návrháři aplikace logiky přidejte [Trigger žádosti](../connectors/connectors-native-reqres.md#add-request) do vaší aplikace logiky.

1. V části Trigger vyberte **Nový krok**. Do vyhledávacího pole zadejte `liquid` jako filtr a vyberte tuto akci: **transformační JSON pro JSON – Liquid**

   ![Najít a vybrat kapalinovou akci](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Otevřete seznam **map** a v tomto příkladu vyberte šablonu Liquid, která je "JsonToJsonTemplate".

   ![Vybrat mapu](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Pokud je seznam mapování prázdný, pravděpodobně není vaše aplikace logiky propojena s vaším účtem integrace. 
   Pokud chcete propojit aplikaci logiky s účtem pro integraci, který má šablonu nebo mapu kapalin, postupujte podle těchto kroků:

   1. V nabídce aplikace logiky vyberte **Nastavení pracovního postupu**.

   1. V seznamu **Vyberte účet pro integraci** vyberte účet pro integraci a vyberte **Uložit**.

      ![Propojit aplikaci logiky s účtem pro integraci](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Nyní do této akce přidejte vlastnost **obsah** . Otevřete seznam **Přidat nový parametr** a vyberte **obsah**.

   ![Přidat vlastnost content do akce](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Chcete-li nastavit hodnotu vlastnosti **obsahu** , klikněte do pole **obsahu** , aby se zobrazil seznam dynamického obsahu. Vyberte token **textu** , který představuje výstup obsahu textu z triggeru.

   ![Vybrat "hlavní" token pro hodnotu vlastnosti Content](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Po dokončení bude akce vypadat jako v tomto příkladu:

   ![Akce "transformační JSON pro JSON" se dokončila.](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

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

* Transformovat XML na JSON
  
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

* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack")  
* [Další informace o mapách](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o službě Enterprise Integration Maps")  

