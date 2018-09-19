---
title: Nastavení pořadí nasazení pro prostředky Azure | Dokumentace Microsoftu
description: Popisuje, jak nastavit jeden prostředek jako závislé na jiný prostředek během nasazování k zajištění, že se prostředky nasadí ve správném pořadí.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2018
ms.author: tomfitz
ms.openlocfilehash: 308ab9d35e07c8376fb183c794fcad77a74a1df9
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295559"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definovat pořadí pro nasazení prostředků do šablon Azure Resource Manageru
Pro daný prostředek může být další prostředky, které musí existovat předtím, než se prostředek nasazuje. Například SQL server, musí existovat před pokusem o nasazení databáze SQL. Můžete definovat tuto relaci označením jeden prostředek jako závislé na jiný prostředek. Definování závislostí s **dependsOn** element, nebo pomocí **odkaz** funkce. 

Resource Manager vyhodnocuje závislosti mezi prostředky a provádí nasazení v závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Stačí k definování závislostí pro prostředky, které jsou nasazené do stejné šablony. 

Podívejte se kurz [kurz: vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
Ve vaší šabloně dependsOn prvek umožňuje definovat jeden prostředek jako závislé na jeden nebo více prostředků. Jeho hodnota může být čárkami oddělený seznam názvů prostředků. 

Následující příklad ukazuje škálovací sadu virtuálních počítačů, která závisí na nástroj pro vyrovnávání zatížení, virtuální sítě a smyčku, která vytvoří více účtů úložiště. Tyto prostředky nejsou uvedené v následujícím příkladu, ale bude muset existovat někde jinde v šabloně.

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

V předchozím příkladu je zahrnuta závislost na prostředky, které jsou vytvořené prostřednictvím kopií smyčky s názvem **storageLoop**. Příklad najdete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).

Při definování závislostí, můžete použít obor názvů zprostředkovatele prostředků a typ prostředku, aby se zabránilo nejednoznačnosti. Například aby se vyjasnilo, nástroje pro vyrovnávání zatížení a virtuální síť, která může mít stejné názvy jako ostatní prostředky, použijte následující formát:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Když jste sklon nesmí být použití dependsOn mapovat vztahy mezi prostředky, je důležité pochopit, proč to děláte. Dokumentování způsob propojených prostředků, například dependsOn není správný přístup. Nemůže zadat dotaz, které prostředky byly definovány v elementu dependsOn po nasazení. Pomocí dependsOn vás to ovlivnit dobu nasazení protože Resource Manager nenasadí v paralelní dva prostředky, které jsou závislé. 

## <a name="child-resources"></a>Podřízené prostředky
Vlastnost prostředky můžete zadat podřízené prostředky, které se vztahují k prostředku je definována. Podřízené prostředky lze pouze definované pěti úrovněmi. Je důležité si uvědomit, že není vytvořena implicitní závislost mezi prostředkem podřízený a nadřazený prostředek. Pokud potřebujete podřízený prostředek k nasazení po nadřazeném prostředku, musí explicitně stavu dané závislosti s vlastností dependsOn. 

Každý nadřazený prostředek přijímá pouze určité typy prostředků jako podřízené prostředky. Typy přijaté prostředků jsou určené v [schéma šablony](https://github.com/Azure/azure-resource-manager-schemas) nadřazeného prostředku. Název typu podřízeného prostředku obsahuje název nadřazeného typu prostředku, jako například **Microsoft.Web/sites/config** a **Microsoft.Web/sites/extensions** jsou i podřízené prostředky **Microsoft.Web/sites**.

Následující příklad ukazuje systému SQL server a SQL database. Všimněte si, že je definován explicitní závislosti mezi SQL database a SQL server, i v případě, že databáze je podřízený server.

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

## <a name="reference-and-list-functions"></a>referenční dokumentace a seznam funkcí
[Odkazu funkci](resource-group-template-functions-resource.md#reference) umožní výrazu odvodit svoji hodnotu z jiných párů názvu a hodnoty JSON nebo prostředky modulu runtime. [Seznamu * funkce](resource-group-template-functions-resource.md#list) návratové hodnoty pro prostředek z operace seznamu.  Referenční dokumentace a seznam výrazů implicitně deklarovat, že jeden prostředek závisí na jiné, když oba odkazované prostředky se nasadí do stejné šablony a podle jeho název (není ID prostředku). Pokud předáte ID prostředku do odkaz nebo seznam funkcí, se nevytvoří implicitní odkaz.

Je obecný formát odkazu funkce:

```json
reference('resourceName').propertyPath
```

Je obecný formát klíče Listkey funkce:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

V následujícím příkladu koncový bod CDN explicitně závisí na profil CDN a implicitně závisí na webovou aplikaci.

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

Tento element nebo dependsOn element můžete použít k určení závislostí, ale nemusíte používat pro stejného závislého prostředku. Kdykoli je to možné, pomocí implicitní odkaz se vyhnout přidávání zbytečné závislostí.

Další informace najdete v tématu [odkazu funkci](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Doporučení pro nastavení závislostí

Při rozhodování o tom, jaké závislosti na nastavení, použijte následující pokyny:

* Nastavte jako několik závislostí nejvíce.
* Nastavte jako závisí na jeho nadřazený prostředek podřízený prostředek.
* Použití **odkaz** fungovat a předejte mu název prostředku implicitní závislosti mezi prostředky, které je potřeba sdílet vlastnost nastavit. Nepřidávejte explicitní závislosti (**dependsOn**) Pokud jste již definován implicitní závislostí. Tento přístup snižuje riziko toho, že zbytečné závislosti. 
* Pokud prostředek nelze nastavit závislost **vytvořili** bez funkce z jiného prostředku. Nenastavujte závislost, pokud prostředky pracovat pouze po nasazení.
* Umožněte závislosti cascade bez nastavení je explicitně. Například vašeho virtuálního počítače závisí na rozhraní virtuální sítě a virtuální síťové rozhraní závisí na virtuální sítě a veřejné IP adresy. Proto tento virtuální počítač je nasazený všechny tři prostředky, ale není explicitně nastavit virtuální počítač jako závislé na všechny tři zdroje. Tento postup vysvětluje pak pořadí závislostí a usnadňuje později změnit šablonu.
* Pokud hodnotu nelze určit před nasazením, zkuste nasazení prostředků bez závislosti. Například hodnota konfigurace musí název jiného prostředku, nemusí potřebovat závislost. Tyto pokyny vždycky nefunguje, protože některé prostředky ověřte existenci jiný prostředek. Pokud obdržíte chybu, přidejte závislosti. 

Během ověřování šablony Resource Manageru určí cyklické závislosti. Pokud obdržíte chybu s informacemi o tom, že existuje cyklická závislost, vyhodnoťte šablony nejsou potřeba žádné závislosti a je možné odebrat. Pokud odebrání závislostí nefunguje, můžete předejít cyklické závislosti přesunutím některých operací nasazení do podřízené prostředky, které jsou nasazeny za prostředky, které mají cyklickou závislost. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte zadat vlastnosti na každé z nich odkazovat na druhý. Můžete je nasadit v následujícím pořadí:

1. vm1
2. vm2
3. Rozšíření na vm1 závisí na vm1 a vm2. Rozšíření nastaví hodnoty pro vm1, který získá z vm2.
4. Rozšíření na vm2 závisí na vm1 a vm2. Rozšíření nastaví hodnoty pro vm2, který získá z vm1.

Informace o vyhodnocení pořadí nasazení a řešení chyb závislostí, naleznete v tématu [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Další postup

* Absolvovat kurz, naleznete v tématu [kurz: vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Další informace o řešení potíží s závislostí při nasazení najdete v tématu [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Další informace o vytváření šablon Azure Resource Manageru, najdete v článku [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [šablony funkce](resource-group-template-functions.md).

