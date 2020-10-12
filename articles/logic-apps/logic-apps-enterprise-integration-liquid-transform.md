---
title: Převod JSON a XML pomocí šablon kapalin
description: Transformujte JSON a XML pomocí šablon kapalné jako mapy v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: b3919cbbe0ba7a796a21ae566afb8e2d9fa784db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88716669"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Transformujte JSON a XML pomocí šablon Liquid jako mapy v Azure Logic Apps

Pokud chcete v aplikacích logiky provádět základní transformace JSON, můžete použít operace nativních [dat](../logic-apps/logic-apps-perform-data-operations.md) , jako je například **Vytvoření** nebo **Analýza JSON**. Pro rozšířené a komplexní transformace JSON na JSON, které obsahují prvky, jako jsou iterace, kontrolní toky a proměnné, vytvořte a použijte šablony, které popisují tyto transformace pomocí Open Source šablony typu [Liquid](https://shopify.github.io/liquid/) . Můžete také [provádět další transformace](#other-transformations), například JSON na text, XML na JSON a XML na text.

Předtím, než můžete provést transformaci v aplikaci logiky, musíte nejprve vytvořit kapalinovou šablonu, která definuje mapování, které chcete. Pak [šablonu nahrajte jako mapu](../logic-apps/logic-apps-enterprise-integration-maps.md) do svého účtu pro [integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Když do aplikace logiky přidáte akci **transformace JSON do formátu JSON** , můžete vybrat šablonu Liquid jako mapu pro akci, která se má použít.

V tomto článku se dozvíte, jak tyto úlohy provést:

* Vytvořte šablonu kapalin.
* Přidejte šablonu do účtu pro integraci.
* Přidejte akci pro kapalinovou transformaci do aplikace logiky.
* Vyberte šablonu jako mapu, kterou chcete použít.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Základní znalosti o [jazyce kapalné šablony](https://shopify.github.io/liquid/)

  > [!NOTE]
  > Akce **transformace JSON na formát JSON** následuje po [implementaci DotLiquid pro kapalinu](https://github.com/dotliquid/dotliquid), která se liší v určitých případech od [implementace Shopify pro kapalinu](https://shopify.github.io/liquid). Další informace najdete v tématu [požadavky na šablonu pro Liquid](#liquid-template-considerations).

## <a name="create-the-template"></a>Vytvoření šablony

1. Vytvořte šablonu kapalin, kterou použijete jako mapu pro transformaci JSON. Můžete použít libovolný nástroj pro úpravy, který chcete.

   V tomto příkladu vytvořte ukázkovou kapalinovou šablonu, jak je popsáno v této části:

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

1. Uložte šablonu pomocí `.liquid` rozšíření. Tento příklad používá `SimpleJsonToJsonTemplate.liquid` .

## <a name="upload-the-template"></a>Odeslat šablonu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do vyhledávacího pole Azure Portal zadejte `integration accounts` a vyberte účty pro **integraci**.

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
   | **Mapy** | `SimpleJsonToJsonTemplate.liquid` | Existující šablona nebo soubor mapování v kapalném formátu, který je použit pro transformaci, která je v tomto příkladu "SimpleJsonToJsonTemplate. Liquid". K vyhledání tohoto souboru můžete použít nástroj pro výběr souborů. Omezení velikosti mapování najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Přidat kapalinovou šablonu](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Přidat akci transformace kapalin

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

Pomocí [post](https://www.getpostman.com/postman) nebo podobného nástroje odešlete vstup JSON do aplikace logiky. Transformovaný výstup JSON z aplikace logiky vypadá jako v tomto příkladu:

![Příklad výstupu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Požadavky na šablonu kapalin

* Kapalné šablony následují [omezení velikosti souborů pro mapy](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) v Azure Logic Apps.

* Akce **transformace JSON na JSON** se řídí [implementací DotLiquid pro kapalinu](https://github.com/dotliquid/dotliquid). Tato implementace je port pro .NET Framework z [implementace Shopify pro kapalinu](https://shopify.github.io/liquid/) a v [určitých případech](https://github.com/dotliquid/dotliquid/issues)se liší.

  Tady jsou známé rozdíly:

  * Akce **Transform JSON pro transformaci** JSON nativně výstupuje řetězec, který může zahrnovat JSON, XML, HTML a tak dále. Kapalinová akce znamená, že očekávaný textový výstup z šablony kapaliny je řetězec JSON. Akce nastaví aplikaci logiky k analýze vstupu jako objektu JSON a použije obálku, aby kapalina mohla interpretovat strukturu JSON. Po transformaci akce instruuje vaši aplikaci logiky, aby analyzovala textový výstup z tekutiny zpět do formátu JSON.

    DotLiquid nativně nerozumí formátu JSON, takže se ujistěte, že jste nastavili znak zpětného lomítka ( `\` ) a všechny jiné vyhrazené znaky JSON.

  * Pokud vaše šablona používá [kapalné filtry](https://shopify.github.io/liquid/basics/introduction/#filters), ujistěte se, že dodržujete [konvence vytváření názvů pro DotLiquid a C#](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), které používají *velká a malá písmena*. Pro všechny transformace v kapalině se ujistěte, že názvy filtru ve vaší šabloně používají také velká a malá písmena. V opačném případě nebudou filtry fungovat.

    Například při použití `replace` filtru, použijte `Replace` , nikoli `replace` . Stejné pravidlo platí, pokud si vyzkoušíte příklady na [webu DotLiquid online](http://dotliquidmarkup.org/try-online). Další informace naleznete v tématu [Shopify Liquid Filters](https://shopify.dev/docs/themes/liquid/reference/filters) a [DotLiquid Liquid Filters](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). Specifikace Shopify obsahuje příklady pro každý filtr, takže pro porovnání můžete vyzkoušet tyto příklady na [DotLiquid-try online](https://dotliquidmarkup.org/try-online).

  * `json`Filtr z filtrů rozšíření Shopify není [v současnosti implementován v DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). Tento filtr obvykle můžete použít k přípravě textového výstupu pro analýzu řetězců JSON, ale místo toho je potřeba použít `Replace` Filtr.

  * Standardní `Replace` Filtr v [implementaci DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) používá [porovnání regulárního výrazu (Regex)](/dotnet/standard/base-types/regular-expression-language-quick-reference), zatímco [implementace Shopify](https://shopify.github.io/liquid/filters/replace/) používá [jednoduché řetězcové porovnání](https://github.com/Shopify/liquid/issues/202). Obě implementace se zdají fungovat stejným způsobem, dokud nepoužijete znak vyhrazený regulárním výrazem nebo řídicí znak v parametru Match.

    Například pro řídicí znak zpětného lomítka (), který je vyhrazen pro regulární výrazy `\` , použijte `| Replace: '\\', '\\'` , a ne `| Replace: '\', '\\'` . Tyto příklady ukazují, jak se `Replace` Filtr chová odlišně, když se pokusíte řídicí znak zpětného lomítka použít. I když tato verze funguje úspěšně:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    S tímto výsledkem:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Tato verze se nezdařila:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    S touto chybou:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Další informace naleznete v tématu [Replace Filter Standard používá porovnávání vzorů regulárního výrazu...](https://github.com/dotliquid/dotliquid/issues/385).

  * `Sort`Filtr v [implementaci DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) Seřadí položky v poli nebo kolekci podle vlastnosti, ale s těmito rozdíly:<p>

    * Sleduje chování [Shopify sort_natural](https://shopify.github.io/liquid/filters/sort_natural/), ale chování při [řazení Shopify](https://shopify.github.io/liquid/filters/sort/).

    * Seřadí pouze v řetězcové abecedě. Další informace najdete v tématu [číselné řazení](https://github.com/Shopify/liquid/issues/980).

    * Používá řazení bez *rozlišení* velkých a malých písmen, nerozlišuje velká a malá písmena. Další informace najdete v tématu [filtr řazení nedodržuje chování velkých a malých písmen ze specifikace Shopify]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Další transformace pomocí Liquid

Kapalina není omezena pouze na transformace JSON. Můžete také použít kapalinu k provádění dalších transformací, například:

* [JSON na text](#json-text)
* [XML do formátu JSON](#xml-json)
* [XML na text](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Transformovat JSON na text

Tady je šablona Liquid, která se používá v tomto příkladu:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Tady jsou ukázkové vstupy a výstupy:

![Příklad výstupu JSON na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Transformovat XML na JSON

Tady je šablona Liquid, která se používá v tomto příkladu:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`Smyčka je vlastní mechanismus smyčky pro vstup XML, takže můžete vytvořit datové části JSON, které zabraňují koncové čárkě. `where`Podmínka pro tento mechanismus vlastního smyčky také používá název elementu XML pro porovnání, nikoli hodnotu prvku jako jiné kapalné filtry. Další informace najdete v tématu podrobné [podrobně o zásadách pro nastavení těla – kolekce věcí](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Tady jsou ukázkové vstupy a výstupy:

![Příklad výstupního souboru XML do formátu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Transformovat XML na text

Tady je šablona Liquid, která se používá v tomto příkladu:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Tady jsou ukázkové vstupy a výstupy:

![Příklad výstupního souboru XML na text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Další kroky

* [Jazyk a příklady Shopify Liquid](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid – vyzkoušet online](https://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [Problémy s DotLiquid GitHubem](https://github.com/dotliquid/dotliquid/issues/)
* Další informace o [mapách](../logic-apps/logic-apps-enterprise-integration-maps.md)
