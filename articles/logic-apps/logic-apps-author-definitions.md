---
title: Vytvoření, úprava nebo rozšíření definic pracovních postupů aplikace JSON aplikace logiky
description: Jak psát, upravovat a rozšiřovat definice pracovních postupů aplikace logiky json v Aplikacích Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979393"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Vytvoření, úprava nebo rozšíření json pro definice pracovních postupů aplikace logiky v Aplikacích Logika Azure

Při vytváření řešení podnikové integrace s automatizovanými pracovními postupy v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), základní definice aplikace logiky použít jednoduchý a deklarativní JavaScript zápis objektu (JSON) spolu s [jazykem definice pracovního postupu (WDL) schéma](../logic-apps/logic-apps-workflow-definition-language.md) pro jejich popis a ověření. Tyto formáty usnadňují čtení a pochopení definic aplikací logiky, aniž by o kódu věděly mnoho.
Pokud chcete automatizovat vytváření a nasazování aplikací [logiky,](../azure-resource-manager/templates/overview.md)můžete zahrnout definice aplikací logiky jako [prostředky Azure](../azure-resource-manager/management/overview.md) do šablon Azure Resource Manager .
Chcete-li vytvářet, spravovat a nasazovat aplikace logiky, pak můžete použít [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)nebo [Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/).

Chcete-li pracovat s definicemi aplikací logiky v JSON, otevřete editor zobrazení kódu při práci na portálu Azure nebo ve Visual Studiu nebo zkopírujte definici do libovolného editoru, který chcete.
Pokud s aplikacemi logiky teprve začínáte, přečtěte si, [jak vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Některé funkce Azure Logic Apps, jako je definování parametrů a více aktivačních událostí v definicích aplikací logiky, jsou k dispozici jenom v JSON, ne návrháře logických aplikací.
> Takže pro tyto úkoly, musíte pracovat v zobrazení kódu nebo jiný editor.

## <a name="edit-json---azure-portal"></a>Upravit JSON – portál Azure

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">portálu Azure</a>.

2. V levé nabídce zvolte **Všechny služby**.
Ve vyhledávacím poli najděte "aplikace logiky" a pak z výsledků vyberte aplikaci logiky.

3. V nabídce aplikace logiky vyberte v části **Nástroje pro vývoj** **položku Zobrazení kódu aplikace logiky**.

   Editor zobrazení kódu se otevře a zobrazí definici aplikace logiky ve formátu JSON.

## <a name="edit-json---visual-studio"></a>Upravit JSON - Visual Studio

Než budete moci pracovat na definici aplikace logiky v sadě Visual Studio, ujistěte se, že jste [nainstalovali požadované nástroje](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Chcete-li vytvořit aplikaci logiky s Visual Studio, přečtěte si [úvodní příručku: Automatizace úloh a procesů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Ve Visual Studiu můžete otevřít aplikace logiky, které byly vytvořeny a nasazeny buď přímo z portálu Azure, nebo jako projekty Azure Resource Manager u Visual Studia.

1. Otevřete řešení Visual Studio nebo projekt [Skupiny prostředků Azure,](../azure-resource-manager/management/overview.md) který obsahuje vaši aplikaci logiky.

2. Vyhledejte a otevřete definici aplikace logiky, která se ve výchozím nastavení zobrazí v [šabloně Správce prostředků](../azure-resource-manager/templates/overview.md)s názvem **LogicApp.json**.
Tuto šablonu můžete použít a přizpůsobit pro nasazení do různých prostředí.

3. Otevřete místní nabídku pro definici a šablonu aplikace logiky.
Vyberte **Otevřít pomocí Návrháře aplikace logiky**.

   ![Otevření aplikace logiky v řešení Visual Studia](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

4. V dolní části návrháře zvolte **Zobrazení kódu**.

   Editor zobrazení kódu se otevře a zobrazí definici aplikace logiky ve formátu JSON.

5. Chcete-li se vrátit do zobrazení návrháře, v dolní části editoru zobrazení kódu zvolte **Návrh**.

## <a name="parameters"></a>Parametry

Životní cyklus nasazení má obvykle různá prostředí pro vývoj, testování, přípravu a produkční prostředí. Pokud máte hodnoty, které chcete znovu použít v celé aplikaci logiky bez hardcoding nebo které se liší v závislosti na potřebách nasazení, můžete vytvořit [šablonu Azure Resource Manager](../azure-resource-manager/management/overview.md) pro definici pracovního postupu, takže můžete také automatizovat nasazení aplikace logiky.

Pomocí těchto obecných kroků *můžete parametrizovat*nebo definovat a použít parametry pro tyto hodnoty. Hodnoty pak můžete zadat v samostatném souboru parametrů, který tyto hodnoty předá šabloně. Tímto způsobem můžete změnit tyto hodnoty snadněji bez nutnosti aktualizovat a znovu nasadit aplikaci logiky. Podrobné informace najdete v [tématu Přehled: Automatizace nasazení pro aplikace logiky pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. V šabloně definujte parametry šablony a parametry definice pracovního postupu pro přijetí hodnot, které mají být používány při nasazení a běhu.

   Parametry šablony jsou definovány v části parametrů, která je mimo definici pracovního postupu, zatímco parametry definice pracovního postupu jsou definovány v části parametrů, která je uvnitř definice pracovního postupu.

1. Nahraďte pevně zakódované hodnoty výrazy, které odkazují na tyto parametry. Výrazy šablony používají syntaxi, která se liší od výrazů definice pracovního postupu.

   Vyhněte se komplikuje kód tím, že pomocí výrazy šablony, které jsou vyhodnocovány při nasazení, uvnitř výrazy definice pracovního postupu, které jsou vyhodnocovány za běhu. Používejte pouze výrazy šablony mimo definici pracovního postupu. V definici pracovního postupu používejte pouze výrazy definice pracovního postupu.

   Když zadáte hodnoty parametrů definice pracovního postupu, můžete odkazovat na parametry šablony pomocí části parametry, která je mimo definici pracovního postupu, ale stále uvnitř definice prostředku pro aplikaci logiky. Tímto způsobem můžete předat hodnoty parametrů šablony do parametrů definice pracovního postupu.

1. Uložte hodnoty pro vaše parametry do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md) a zahrňte tento soubor s nasazením.

## <a name="process-strings-with-functions"></a>Procesní řetězce s funkcemi

Logic Apps má různé funkce pro práci s řetězci.
Předpokládejme například, že chcete předat název společnosti z objednávky do jiného systému.
Však nejste jisti správné zpracování pro kódování znaků.
Můžete provést base64 kódování na tento řetězec, ale aby se zabránilo úniky v adrese URL, můžete nahradit několik znaků místo. Také potřebujete pouze podřetězec pro název společnosti, protože prvních pět znaků se nepoužívá.

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

Tyto kroky popisují, jak tento příklad zpracovává tento řetězec, pracující zevnitř ven:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Získejte [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) název společnosti, abyste získali celkový počet znaků.

2. Chcete-li získat kratší `5`řetězec, odečtěte .

3. Nyní si [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md).
Začněte `5`na indexu a přejděte na zbytek řetězce.

4. Převeďte tento [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) podřetězec na řetězec.

5. Nyní [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) všechny `+` znaky `-` s postavami.

6. Nakonec [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) všechny `/` znaky `_` s postavami.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapování položek seznamu na hodnoty vlastností a potom použijte mapy jako parametry

Chcete-li získat různé výsledky na základě hodnoty vlastnosti, můžete vytvořit mapu, která odpovídá každé hodnotě vlastnosti s výsledkem, a pak použít tuto mapu jako parametr.

Tento pracovní postup například definuje některé kategorie jako parametry a mapu, která odpovídá těmto kategoriím s konkrétní adresou URL.
Nejprve pracovní postup získá seznam článků. Pracovní postup pak pomocí mapy vyhledá adresu URL odpovídající kategorii pro každý článek.

*   Funkce [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) zkontroluje, zda kategorie odpovídá známé definované kategorii.

*   Po získání odpovídající kategorie, příklad vytáhne položku z mapy pomocí hranatých závorek:`parameters[...]`

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

## <a name="get-data-with-date-functions"></a>Získání dat pomocí funkcí Data

Chcete-li získat data ze zdroje dat, který nativně nepodporuje *aktivační události*, můžete místo toho použít funkce Date pro práci s časy a daty.
Tento výraz například zjistí, jak dlouho postupujte tak dlouho, jak probíhají kroky tohoto pracovního postupu, práce zevnitř ven:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` akce extrahujte `startTime`.
2. Získejte aktuální `utcNow()`čas s .
3. Odečíst jednu sekundu:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Můžete použít jiné jednotky času, `hours`jako nebo `minutes` .

3. Nyní můžete porovnat tyto dvě hodnoty.

   Pokud je první hodnota menší než druhá hodnota, uplynula od prvního umístění objednávky více než jedna sekunda.

Chcete-li formátovat data, můžete použít řetězec formatters. Chcete-li například získat RFC1123, použijte [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md).
Další informace o [formátování data](../logic-apps/logic-apps-workflow-definition-language.md).

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
* [Spuštění kroků na základě různých hodnot (příkazy switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (smyček)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelních kroků (větví)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Další informace o [schématu jazyka definice pracovního postupu pro aplikace Logika Azure](../logic-apps/logic-apps-workflow-definition-language.md)
* Další informace o [akcích a aktivačních událostech pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
