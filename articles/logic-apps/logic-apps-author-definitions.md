---
title: Vytvoření, úprava nebo rozšiřování definic pracovních postupů JSON aplikace logiky
description: Jak psát, upravovat a roztáhnout definice pracovních postupů JSON vaší aplikace logiky v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014393"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Vytváření, úpravy nebo rozšiřování souborů JSON pro definice pracovních postupů aplikací logiky v Azure Logic Apps

Při vytváření podnikových integračních řešení pomocí automatizovaných pracovních postupů v [Azure Logic Apps](../logic-apps/logic-apps-overview.md)používají definice aplikace logiky jednoduché a deklarativní JavaScript Object Notation (JSON) spolu se [schématem WDL (Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md) ) pro jejich popis a ověření. Tyto formáty usnadňují čtení definic aplikace logiky a jejich pochopení bez znalosti kódu.
Pokud chcete automatizovat vytváření a nasazování aplikací logiky, můžete do [šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)zahrnout definice aplikace logiky jako [prostředky Azure](../azure-resource-manager/management/overview.md) .
Pokud chcete vytvářet, spravovat a nasazovat Logic Apps, můžete použít [Azure PowerShell](/powershell/module/az.logicapp), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)nebo [rozhraní API služby Azure Logic Apps REST](/rest/api/logic/).

Chcete-li pracovat s definicemi aplikace logiky ve formátu JSON, otevřete editor zobrazení kódu při práci v Azure Portal nebo v aplikaci Visual Studio nebo zkopírujte definici do libovolného editoru, který chcete.
Pokud s Logic Apps začínáte, přečtěte si, [jak vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Některé funkce Azure Logic Apps, jako je definování parametrů a vícenásobné triggery v definicích aplikace logiky, jsou k dispozici pouze ve formátu JSON, nikoli v Návrháři Logic Apps.
> Takže pro tyto úlohy je nutné pracovat v zobrazení kódu nebo jiném editoru.

## <a name="edit-json---azure-portal"></a>Upravit Azure Portal JSON

1. Přihlaste se na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. V nabídce vlevo vyberte **všechny služby**.
Do vyhledávacího pole Najděte "Logic Apps" a potom z výsledků vyberte svou aplikaci logiky.

3. V nabídce aplikace logiky v části **vývojové nástroje** vyberte **zobrazení kód aplikace logiky**.

   Otevře se editor zobrazení kódu a zobrazí se definice aplikace logiky ve formátu JSON.

## <a name="edit-json---visual-studio"></a>Upravit JSON – Visual Studio

Než budete moct v aplikaci Visual Studio pracovat s definicí aplikace logiky, ujistěte se, že jste [nainstalovali požadované nástroje](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Pokud chcete vytvořit aplikaci logiky pomocí sady Visual Studio, přečtěte si [rychlý Start: automatizace úloh a procesů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

V aplikaci Visual Studio můžete otevřít aplikace logiky, které byly vytvořeny a nasazeny přímo z Azure Portal nebo jako Azure Resource Manager projektů ze sady Visual Studio.

1. Otevřete řešení sady Visual Studio nebo projekt [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) , který obsahuje vaši aplikaci logiky.

2. Najděte a otevřete definici aplikace logiky, která se ve výchozím nastavení zobrazuje v [šabloně správce prostředků](../azure-resource-manager/templates/overview.md)s názvem **LogicApp.js**.
Tuto šablonu můžete použít a přizpůsobit pro nasazení v různých prostředích.

3. Otevřete místní nabídku pro definici a šablonu aplikace logiky.
Vyberte **Otevřít pomocí Návrháře aplikace logiky**.

   ![Otevření aplikace logiky v řešení sady Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

4. V dolní části návrháře vyberte možnost **zobrazení kódu**.

   Otevře se editor zobrazení kódu a zobrazí se definice aplikace logiky ve formátu JSON.

5. Chcete-li se vrátit do zobrazení návrháře, v dolní části editoru zobrazení kódu klikněte na tlačítko **Návrh**.

## <a name="parameters"></a>Parametry

Životní cyklus nasazení má obvykle různá prostředí pro vývoj, testování, přípravu a produkci. Pokud máte hodnoty, které chcete znovu použít v rámci aplikace logiky bez zakódujeme nebo které se liší v závislosti na potřebách nasazení, můžete vytvořit [šablonu Azure Resource Manager](../azure-resource-manager/management/overview.md) pro definici pracovního postupu, abyste mohli také automatizovat nasazení aplikace logiky.

Pomocí těchto obecných *kroků můžete místo toho definovat a používat* parametry pro, tyto hodnoty. Hodnoty pak můžete zadat do samostatného souboru parametrů, který tyto hodnoty předá vaší šabloně. Tímto způsobem můžete tyto hodnoty snadněji změnit, aniž byste museli aktualizovat a znovu nasazovat aplikaci logiky. Úplné podrobnosti najdete v tématu [Přehled: Automatizace nasazení pro Logic Apps pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. V šabloně definujte parametry šablony a parametry definice pracovního postupu pro přijetí hodnot, které se mají použít při nasazení a modulu runtime v uvedeném pořadí.

   Parametry šablony jsou definované v oddílu Parameters, který je mimo vaši definici pracovního postupu, zatímco parametry definice pracovního postupu jsou definované v oddílu Parameters, který je uvnitř definice pracovního postupu.

1. Nahraďte hodnoty pevně zakódované výrazy, které na tyto parametry odkazují. Výrazy šablon používají syntaxi, která se liší od výrazů definice pracovního postupu.

   Vyhněte se komplikaci kódu tím, že nepoužíváte výrazy šablony, které jsou vyhodnocovány při nasazení, uvnitř výrazů definice pracovního postupu, které jsou vyhodnocovány za běhu. Použijte pouze výrazy šablony mimo definici pracovního postupu. V rámci definice pracovního postupu používejte jenom výrazy definice pracovního postupu.

   Když zadáte hodnoty pro parametry definice pracovního postupu, můžete odkazovat na parametry šablony pomocí oddílu Parameters, který je mimo vaši definici pracovního postupu, ale ještě v definici prostředků pro vaši aplikaci logiky. Tímto způsobem můžete hodnoty parametrů šablony předat do parametrů definice pracovního postupu.

1. Uložte hodnoty pro parametry do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md) a zahrňte tento soubor do nasazení.

## <a name="process-strings-with-functions"></a>Zpracování řetězců pomocí funkcí

Logic Apps má různé funkce pro práci s řetězci.
Předpokládejme například, že chcete předat název společnosti z objednávky do jiného systému.
Nejste si ale jisti o správném zpracování pro kódování znaků.
U tohoto řetězce můžete pro tento řetězec použít kódování Base64, ale chcete-li zabránit únikovým znakům v adrese URL, můžete místo toho nahradit několik znaků. Pro název společnosti potřebujete také podřetězec, protože prvních pět znaků se nepoužívá.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Tento postup popisuje, jak tento příklad zpracovává tento řetězec a pracuje zevnitř k vnějšímu:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Získejte [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) název společnosti, abyste získali celkový počet znaků.

2. Chcete-li získat kratší řetězec, odčítání `5` .

3. Nyní získáte [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) .
Začněte na indexu `5` a přejít na zbytek řetězce.

4. Převede tento podřetězec na [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) řetězec.

5. [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)Všechny znaky jsou nyní znaky `+` `-` .

6. Nakonec [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) všechny `/` znaky se `_` znaky.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapování položek seznamu na hodnoty vlastností a následné použití map jako parametrů

Chcete-li získat různé výsledky na základě hodnoty vlastnosti, můžete vytvořit mapu, která bude odpovídat hodnotě jednotlivých vlastností, a pak použít tuto mapu jako parametr.

Například tento pracovní postup definuje některé kategorie jako parametry a mapu, která se shoduje s těmito kategoriemi s konkrétní adresou URL.
Nejprve pracovní postup získá seznam článků. Pracovní postup potom používá mapu k nalezení adresy URL, která odpovídá kategorii pro každý článek.

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md)Funkce zkontroluje, jestli kategorie odpovídá známé definované kategorii.

*   Po získání vyhovující kategorie se v příkladu položka z mapy načte pomocí hranatých závorek: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Získat data s funkcemi data

Pokud chcete získat data ze zdroje dat, který nativně nepodporuje *triggery*, můžete místo toho použít funkce Date pro práci s časy a kalendářními daty.
Tento výraz například vyhledá, jak dlouho trvá tento pracovní postup, a pracuje zevnitř s vnějším:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` Akce rozbalte `startTime` .
2. Získá aktuální čas pomocí `utcNow()` .
3. Odečíst jednu sekundu:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Můžete použít jiné jednotky času, například `minutes` nebo `hours` .

3. Nyní můžete porovnat tyto dvě hodnoty.

   Pokud je první hodnota menší než druhá hodnota, pak více než jedna sekunda uplynula od prvního umístění objednávky.

Chcete-li formátovat data, můžete použít formátovací moduly řetězců. Například pro získání RFC1123 použijte [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md) .
Přečtěte si další informace o [formátování kalendářních dat](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy Switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit nebo sloučit paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spuštění kroků na základě seskupeného stavu akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Další informace o [schématu jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Další informace o [akcích a triggerech pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
