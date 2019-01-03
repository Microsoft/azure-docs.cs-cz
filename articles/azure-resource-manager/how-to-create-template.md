---
title: Postup vytvoření šablony Azure Resource Manageru
description: Popisuje postup vytváření šablony Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: abfc7ce78e8676e9560621be1ec9a81717d958e5
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994279"
---
# <a name="create-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Tento článek popisuje proces a rozhodnutí, která učiníte při vytváření šablony Azure Resource Manageru. Poskytuje přehled o příklady a funkce, které vám mohou pomoci při vytváření šablony. Tento článek předpokládá, že nasazujete prostředky do skupiny prostředků. Pokud je potřeba nasadit prostředky ke svému předplatnému Azure, jako je například vytváření zásady Azure nebo přiřazení řízení přístupu na základě rolí naleznete v tématu [vytvoření skupiny prostředků a prostředků pro předplatné Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Vyberte JSON editor

Šablona Resource Manageru je soubor JSON. Je nutné nástroj vhodný pro vytváření obsahu pro práci na souboru JSON. Máte celou řadu možností, ale pokud ještě nemáte editor, který preferujete, nainstalujte [Visual Studio Code (VS Code)](https://code.visualstudio.com/). 

Po instalaci VS Code, přidejte [rozšíření nástroje Azure Resource Manageru](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Toto rozšíření přidává řadu funkcí, které zjednodušují vytváření šablony.

![Šablony sady Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

Na snímku obrazovky vidíte šablonu Resource Manageru otevřít ve Visual Studio Code. 

Kurz instalace rozšíření nástroje Resource Manageru a jak pomocí VS Code, najdete v tématu [rychlý start: Vytváření šablon Azure Resource Manageru pomocí Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Seznamte se se strukturou šablony

Pojďme se podívat na části šablonu, kterou chcete pochopit, jak funguje šablony. Vaše šablona nemusí mít každý oddíl. Oddíly, které chcete se soustředit na jsou:

* [Parametry](resource-manager-templates-parameters.md) části, která zobrazuje hodnoty můžete zadat během nasazování přizpůsobit infrastrukturu, která je nasazená. 

* [Proměnné](resource-manager-templates-variables.md) oddíl, který obsahuje hodnoty, které se používají v šabloně.

* [Funkce](resource-group-authoring-templates.md#functions) oddílu, který ukazuje přizpůsobené výrazy šablony, které se používají ve vaší šabloně.

* [Prostředky](resource-manager-templates-resources.md) oddílu, který ukazuje prostředky Azure, které jsou nasazené do vašeho předplatného.

* [Výstupy](resource-manager-templates-outputs.md) oddílu, který zobrazuje hodnoty, které se vrátí po dokončení nasazení.

## <a name="look-for-similar-templates"></a>Hledat podobné šablony

Často můžete najít existující šablony, který se nasazuje řešení, které se podobá přesně podle potřeby. [Šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/) má stovky šablon od komunity uživatelů.

![Úložiště šablon rychlý start](./media/how-to-create-template/template-quickstart-repo.png)

Prohledejte daného úložiště pro šablonu, která se podobá přesně podle potřeby. Je to v pořádku, že pokud šablony neumí přesně to, co potřebujete, můžete ho upravit.

Po nalezení šablony, vyberte **Procházet na Githubu**a zkopírujte **azuredeploy.json** soubor z úložiště. V nástroji VS Code, vytvořte nový soubor s názvem **azuredeploy.json** a přidat obsah souboru šablony, který jste zkopírovali z úložiště rychlý start.

## <a name="add-resources"></a>Přidat prostředky

Pravděpodobně budete chtít přizpůsobit šablonu, abyste měli jistotu, že dělá přesně to, co chcete. Nejprve si prohlédněte prostředky, které jsou nasazené. Budete muset přidat, odebrat nebo změnit prostředky v šabloně. Popisy a syntaxe prostředků najdete v tématu [referenčními informacemi k šablonám Azure Resource Manageru](/azure/templates/).

![Referenční informace k šablonám](./media/how-to-create-template/template-reference.png)

Po zkontrolování těchto vlastností, proveďte požadované změny. Doporučení o tom, jak definovat prostředky, najdete v tématu [prostředků – doporučené postupy pro](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Přidání nebo odebrání parametrů

Potřebujete také upravit parametry pro šablonu. Můžete přidávat nebo odebírat parametry založené na tom, kolik přizpůsobení, které chcete povolit během nasazení. Doporučení, jak používat parametry, naleznete v tématu [parametry – doporučené postupy pro](template-best-practices.md#parameters).

## <a name="add-tags"></a>Přidání značek

Můžete přidat značky k vašim prostředkům logicky tak uspořádat podle kategorií a rozdělení fakturační náklady. Přidání značek je jednoduché, použít ve formátu JSON pro prostředek. Například následující účet úložiště obsahuje dvě značky:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Můžete také použít značky dynamicky z parametrů. Další informace najdete v tématu [značky v šabloně](resource-manager-templates-resources.md#tags).

## <a name="review-template-functions"></a>Kontrola funkce šablon

Můžete si všimnout výrazů v šabloně, které jsou uzavřeny v závorkách, například `"[some-expression]"`. Tyto výrazy používají funkce šablony k vytvoření hodnot během nasazení.

Například vidíte často výraz jako:

```json
"name": "[parameters('siteName')]"
```

Tento výraz získá hodnotu parametru. Hodnota je přiřazená k vlastnosti name.

Nebo, může se zobrazit složitější výraz, který používá několik funkcí, jako jsou:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Tento výraz získá objekt s vlastnostmi účtu úložiště.

Abyste zjistili, co tak učiníte, zkontrolujte funkce [referenčními informacemi k šablonám funkce](resource-group-template-functions.md) dokumentaci.

## <a name="create-more-than-one-instance"></a>Vytvořit více než jednu instanci

Někdy budete chtít vytvořit více než jednu instanci zdroje. Například můžete potřebovat několik účtů úložiště. Spíše než opakovat prostředků pomocí šablony, můžete použít `copy` syntaxe pro určení více než jednu instanci.

Následující příklad vytvoří tři účty úložiště:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Můžete také určit počet instancí dynamicky z parametru. Další informace najdete v tématu [nasadit více než jednu instanci zdroje nebo vlastnosti v šablonách Azure Resource Manageru](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Podmíněné nasazení prostředku

V některých případech budete muset zadat během nasazování, jestli je nasazený prostředek v šabloně. Například můžete flexibilně nasadit nový prostředek nebo použijte existující prostředek. `condition` Element vám dává možnost zapnout nebo vypnout nasazení pro prostředek. Při splnění výrazu v elementu podmínka se prostředek nasazuje. V případě hodnoty false se během nasazení přeskočí prostředku.

Následující příklad podmíněně nasadí účet úložiště:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Další informace najdete v tématu [podmínky](resource-manager-templates-resources.md#condition).

## <a name="review-dependencies"></a>Kontrola závislosti

Některé prostředky v šabloně je potřeba nasadit před dalším prostředkům. Například SQL server musí existovat před vytvořením databáze SQL. Resource Manager implicitně určuje pořadí nasazení pro prostředky při [odkazu funkci](resource-group-template-functions-resource.md#reference) se používá. Nicméně v některých případech musíte explicitně definovat závislosti pomocí `dependsOn` elementu. Zkontrolujte šablonu chcete zobrazit, pokud je potřeba přidat všechny závislosti. Buďte opatrní není přidání zbytečné závislosti, protože mohou zpomalit nasazení nebo vytvoření cyklické odkazy.

Následující obrázek ukazuje pak pořadí závislostí pro různé prostředky App Service:

![Závislosti webové aplikace](./media/how-to-create-template/web-dependencies.png)

Následující příklad ukazuje část šablony, která definuje závislosti.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Další informace najdete v článku [Určení pořadí pro nasazení prostředků v šablonách Azure Resource Manageru](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Přečtěte si doporučené postupy

Před nasazením šablony, zkontrolujte [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md) zobrazíte, pokud tam nějaké jsou doporučené postupy, které chcete implementovat v šabloně.

Pokud budete muset použít šablony v prostředích různých cloudu Azure, přečtěte si téma [šablon vývoj Azure Resource Manageru pro cloud konzistence](templates-cloud-consistency.md).

## <a name="next-steps"></a>Další postup

* Nasazení šablony najdete v tématu [nasadit pomocí Azure CLI](resource-group-template-deploy-cli.md) nebo [nasazení pomocí Powershellu](resource-group-template-deploy.md).
* Krok za krokem rychlý start k vytvoření šablony najdete v části [šablony vytvořit Azure Resource Manageru pomocí Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Seznam dostupných funkcí v šabloně najdete v tématu [šablony funkce](resource-group-template-functions.md).
