---
title: Nastavení pořadí nasazení pro prostředky
description: Popisuje, jak nastavit jeden prostředek jako závislý na jiném prostředku během nasazení, aby bylo zajištěno, že prostředky jsou nasazeny ve správném pořadí.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f11f79df875492a568a76f494dfffb4a163f64cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153280"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definování pořadí pro nasazení prostředků v šablonách ARM

Při nasazování prostředku budete muset před nasazením zajistit, aby existovaly další prostředky. Například potřebujete sql server před nasazením databáze SQL. Tento vztah definujete označením jednoho prostředku jako závislého na druhém prostředku. Definujete závislost s elementem **dependsOn** nebo pomocí **referenční** funkce.

Resource Manager vyhodnocuje závislosti mezi prostředky a provádí nasazení v závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Stačí definovat závislosti pro prostředky, které jsou nasazeny ve stejné šabloně.

## <a name="dependson"></a>dependsOn

V rámci šablony dependsOn element umožňuje definovat jeden prostředek jako závislé na jednom nebo více prostředků. Jeho hodnota je seznam názvů prostředků oddělený čárkami. Seznam může obsahovat prostředky, které jsou [podmíněně nasazeny](conditional-resource-deployment.md). Když se podmíněný prostředek nenasadí, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

Následující příklad ukazuje škálovací sadu virtuálních počítačů, která závisí na nástrojpro vyrovnávání zatížení, virtuální síti a smyčce, která vytváří více účtů úložiště. Tyto další prostředky nejsou uvedeny v následujícím příkladu, ale musely by existovat jinde v šabloně.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
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

V předchozím příkladu je zahrnuta závislost na prostředcích, které jsou vytvořeny prostřednictvím smyčky kopírování s názvem **storageLoop**. Příklad najdete v tématu [Vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).

Při definování závislostí můžete zahrnout obor názvů poskytovatele prostředků a typ prostředku, abyste předešli nejednoznačnosti. Chcete-li například objasnit nástroj pro vyrovnávání zatížení a virtuální síť, které mohou mít stejné názvy jako jiné prostředky, použijte následující formát:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

I když můžete být nakloněni použití dependsOn mapovat vztahy mezi prostředky, je důležité pochopit, proč to děláte. Například dokumentovat, jak jsou prostředky propojeny, dependsOn není správný přístup. Nelze dotaz, které prostředky byly definovány v dependsOn prvek po nasazení. Pomocí dependsOn, můžete potenciálně ovlivnit čas nasazení, protože Resource Manager nenasadí paralelně dva prostředky, které mají závislost.

## <a name="child-resources"></a>Podřízené zdroje

Vlastnost Resources umožňuje zadat podřízené prostředky, které souvisejí s definovaným zdrojem. Podřízené prostředky lze definovat pouze pět úrovní hluboko. Je důležité si uvědomit, že implicitní závislost nasazení není vytvořena mezi podřízený prostředek a nadřazený prostředek. Pokud potřebujete podřízený prostředek, který má být nasazen po nadřazeném prostředku, musíte explicitně uvést, že závislost s dependsOn vlastnost.

Každý nadřazený prostředek přijímá jako podřízené prostředky pouze určité typy prostředků. Přijaté typy prostředků jsou určeny ve [schématu šablony nadřazeného](https://github.com/Azure/azure-resource-manager-schemas) prostředku. Název podřízeného typu prostředku zahrnuje název nadřazeného typu prostředku, například **Microsoft.Web/sites/config** a **Microsoft.Web/sites/extensions** jsou podřízené prostředky **webu Microsoft.Web/sites**.

Následující příklad ukazuje sql server a databázi SQL. Všimněte si, že explicitní závislost je definována mezi databází SQL a serverem SQL, i když je databáze podřízeným serverem.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
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

## <a name="reference-and-list-functions"></a>referenční a seznamové funkce

[Referenční funkce](template-functions-resource.md#reference) umožňuje výrazu odvodit jeho hodnotu z jiných párů názvů a hodnot JSON nebo prostředků za běhu. Funkce [seznamu*](template-functions-resource.md#list) vrátí hodnoty pro prostředek z operace seznamu.  Odkazy a seznam výrazy implicitně deklarovat, že jeden prostředek závisí na jiném, když odkazovaný prostředek je nasazen ve stejné šabloně a odkazoval se na jeho název (ne ID prostředku). Pokud předáte ID prostředku do funkce odkazu nebo seznamu, implicitní odkaz není vytvořen.

Obecný formát referenční funkce je:

```json
reference('resourceName').propertyPath
```

Obecný formát funkce listKeys je:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

V následujícím příkladu koncový bod CDN explicitně závisí na profilu CDN a implicitně závisí na webové aplikaci.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Můžete použít buď tento prvek nebo dependsOn prvek k určení závislostí, ale není nutné použít oba pro stejný závislý prostředek. Kdykoli je to možné, použijte implicitní odkaz, abyste se vyhnuli přidání zbytečné závislosti.

Další informace naleznete v [tématu referenční funkce](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Cyklické závislosti

Správce prostředků identifikuje cyklické závislosti během ověřování šablony. Pokud se zobrazí chyba oznamující, že cyklická závislost existuje, vyhodnoťte šablonu a zjistěte, zda nejsou potřeba žádné závislosti a lze je odebrat. Pokud odebrání závislostí nefunguje, můžete se vyhnout cyklické závislosti přesunutím některé operace nasazení do podřízených prostředků, které jsou nasazeny po prostředky, které mají cyklické závislosti. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte nastavit vlastnosti na každém z nich, které odkazují na druhé. Můžete je nasadit v následujícím pořadí:

1. vm1
2. vm2
3. Rozšíření na vm1 závisí na vm1 a vm2. Rozšíření nastaví hodnoty na vm1, které získá z vm2.
4. Rozšíření na vm2 závisí na vm1 a vm2. Rozšíření nastaví hodnoty na vm2, které získá z vm1.

Informace o posouzení pořadí nasazení a řešení chyb závislostí najdete [v tématu Řešení běžných chyb nasazení Azure pomocí Správce prostředků Azure](common-deployment-errors.md).

## <a name="next-steps"></a>Další kroky

* Chcete-li projít kurz, [najdete v tématu Kurz: vytvoření šablon Azure Resource Manager s závislými prostředky](template-tutorial-create-templates-with-dependent-resources.md).
* Doporučení při nastavování závislostí najdete v [tématu doporučené postupy šablony Správce prostředků Azure](template-best-practices.md).
* Další informace o řešení potíží se závislostmi během nasazení najdete v [tématu Poradce při potížích s běžnými chybami nasazení Azure ve Správci prostředků Azure](common-deployment-errors.md).
* Další informace o vytváření šablon Azure Resource Manageru najdete v [tématu Vytváření šablon](template-syntax.md).
* Seznam dostupných funkcí v šabloně naleznete v tématu [Šablony funkcí](template-functions.md).

