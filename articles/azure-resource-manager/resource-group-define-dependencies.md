---
title: Nastavení pořadí nasazení pro prostředky
description: V této části najdete popis postupu při nastavování jednoho prostředku v závislosti na jiném prostředku během nasazování, aby bylo zajištěno nasazení prostředků ve správném pořadí.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f5990f099e8b91a4a075d2950f88aa83d34eef4a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806452"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definování pořadí nasazení prostředků v šablonách Azure Resource Manager

Při nasazování prostředku možná budete muset před jeho nasazením zajistit, aby existovaly další prostředky. Před nasazením databáze SQL potřebujete například SQL Server. Tuto relaci definujete tak, že označíte jeden prostředek jako závislý na jiném prostředku. Můžete definovat závislost pomocí elementu **dependsOn** nebo pomocí **referenční** funkce.

Resource Manager vyhodnocuje závislosti mezi prostředky a provádí nasazení v závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Stačí definovat závislosti pro prostředky, které jsou nasazeny ve stejné šabloně.

## <a name="dependson"></a>dependsOn

V rámci šablony umožňuje element dependsOn definovat jeden prostředek jako závislý na jednom nebo více prostředcích. Jeho hodnota je čárkami oddělený seznam názvů prostředků. Seznam může zahrnovat prostředky, které jsou [podmíněně nasazeny](conditional-resource-deployment.md). Pokud není podmíněný prostředek nasazený, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

Následující příklad ukazuje sadu škálování virtuálního počítače, která závisí na nástroji pro vyrovnávání zatížení, na virtuální síti a na smyčce, která vytváří více účtů úložiště. Tyto další prostředky nejsou uvedené v následujícím příkladu, ale musí existovat jinde v šabloně.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

V předchozím příkladu je závislost obsažena v prostředcích, které jsou vytvořeny pomocí smyčky kopírování s názvem **storageLoop**. Příklad najdete [v tématu Vytvoření více instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md).

Při definování závislostí můžete zahrnout obor názvů poskytovatele prostředků a typ prostředku, aby nedocházelo k nejednoznačnosti. Například pro objasnění nástroje pro vyrovnávání zatížení a virtuální sítě, které mohou mít stejné názvy jako jiné prostředky, použijte následující formát:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

I když může být pro mapování vztahů mezi prostředky použit dependsOn, je důležité pochopit, proč to děláte. Například pro dokumentaci, jak jsou prostředky propojeny, dependsOn není správným přístupem. Po nasazení nemůžete zadat dotaz na prostředky, které byly definovány v elementu dependsOn. Pomocí dependsOn potenciálně ovlivní dobu nasazení, protože Správce prostředků neprovádí nasazení v paralelních dvou prostředcích, které mají závislost.

## <a name="child-resources"></a>Podřízené prostředky

Vlastnost Resources umožňuje určit podřízené prostředky, které se vztahují k definovanému prostředku. Podřízené prostředky lze definovat pouze pěti úrovněmi. Je důležité si uvědomit, že implicitní závislost nasazení není vytvořená mezi podřízeným prostředkem a nadřazeným prostředkem. Pokud potřebujete, aby byl podřízený prostředek nasazen po nadřazeném prostředku, musíte tuto závislost explicitně uvést pomocí vlastnosti dependsOn.

Každý nadřazený prostředek přijímá pouze určité typy prostředků jako podřízené prostředky. Přijaté typy prostředků jsou zadány ve [schématu šablony](https://github.com/Azure/azure-resource-manager-schemas) nadřazeného prostředku. Název podřízeného prostředku obsahuje název nadřazeného typu prostředku, jako je například **Microsoft. Web/Sites/config** a **Microsoft. Web/Sites/Extensions** , jak podřízené prostředky **Microsoft. Web/Sites**.

Následující příklad ukazuje SQL Server a SQL Database. Všimněte si, že explicitní závislost je definovaná mezi databází SQL a SQL serverem, i když je databáze podřízená serveru.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>funkce odkazu a seznamu

[Odkazovaná funkce](resource-group-template-functions-resource.md#reference) umožňuje výrazu odvodit svoji hodnotu z jiných párů názvu a hodnoty JSON nebo prostředků modulu runtime. [Seznam *](resource-group-template-functions-resource.md#list) vrátí hodnoty pro prostředek ze seznamu operace.  Výrazy odkazu a seznamu implicitně deklaruje, že jeden prostředek závisí na jiném, pokud je odkazovaný prostředek nasazený ve stejné šabloně a odkazuje na jeho název (nikoli ID prostředku). Pokud předáte ID prostředku do funkce reference nebo list, implicitní odkaz se nevytvoří.

Obecný formát referenční funkce je:

```json
reference('resourceName').propertyPath
```

Obecný formát funkce klíče listkey je:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

V následujícím příkladu je koncový bod CDN explicitně závislý na profilu CDN a implicitně závisí na webové aplikaci.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

K určení závislostí můžete použít buď tento prvek, nebo element dependsOn, ale nemusíte ho používat pro stejný závislý prostředek. Kdykoli je to možné, použijte implicitní odkaz, abyste se vyhnuli přidání zbytečné závislosti.

Další informace najdete v tématu [referenční funkce](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Cyklické závislosti

Správce prostředků identifikuje cyklické závislosti během ověřování šablony. Pokud se zobrazí chyba s oznámením, že existuje cyklická závislost, vyhodnoťte šablonu, abyste viděli, jestli nepotřebujete nějaké závislosti, a můžete je odebrat. Pokud nefungují žádné závislosti, můžete se vyhnout cyklické závislosti přesunutím některých operací nasazení do podřízených prostředků, které jsou nasazeny po prostředcích, které mají cyklické závislosti. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte nastavit vlastnosti pro každý z nich, který odkazuje na druhý. Můžete je nasadit v tomto pořadí:

1. vm1
2. vm2
3. Přípona v VM1 závisí na VM1 a VM2. Rozšíření nastaví hodnoty na VM1, které získá z VM2.
4. Přípona v VM2 závisí na VM1 a VM2. Rozšíření nastaví hodnoty na VM2, které získá z VM1.

Informace o vyhodnocení pořadí nasazení a řešení chyb závislostí najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: vytváření Azure Resource Manager šablon se závislými prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Doporučení pro nastavení závislostí najdete v tématu [osvědčené postupy pro šablonu Azure Resource Manager](template-best-practices.md).
* Další informace o závislostech při řešení potíží při nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Další informace o vytváření šablon Azure Resource Manager najdete v tématu [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [funkce šablon](resource-group-template-functions.md).

