---
title: Podřízené prostředky v šablonách
description: Popisuje, jak nastavit název a typ pro podřízené prostředky v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a950d72751b829c0a2aa3ba5ca27316a0544d9cc
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963908"
---
# <a name="set-name-and-type-for-child-resources"></a>Nastavení názvu a typu pro podřízené prostředky

Podřízené prostředky jsou prostředky, které existují pouze v kontextu jiného prostředku. Například [rozšíření virtuálního počítače](/azure/templates/microsoft.compute/virtualmachines/extensions) nemůže existovat bez [virtuálního počítače](/azure/templates/microsoft.compute/virtualmachines). Prostředek rozšíření je podřízený virtuálnímu počítači.

Každý nadřazený prostředek přijímá pouze určité typy prostředků jako podřízené prostředky. Typ prostředku pro podřízený prostředek zahrnuje typ prostředku nadřazeného prostředku. Například `Microsoft.Web/sites/config` a `Microsoft.Web/sites/extensions` jsou oba podřízené prostředky `Microsoft.Web/sites` . Přijaté typy prostředků jsou zadány ve [schématu šablony](https://github.com/Azure/azure-resource-manager-schemas) nadřazeného prostředku.

V šabloně Azure Resource Manager (šablona ARM) můžete zadat podřízený prostředek buď v nadřazeném prostředku, nebo mimo nadřazený prostředek. Následující příklad ukazuje podřízený prostředek zahrnutý do vlastnosti Resources nadřazeného prostředku.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Podřízené prostředky lze definovat pouze pěti úrovněmi.

Následující příklad ukazuje podřízený prostředek mimo nadřazený prostředek. Tento přístup můžete použít, pokud nadřazený prostředek není nasazený ve stejné šabloně nebo pokud chcete použít [kopírování](copy-resources.md) k vytvoření více než jednoho podřízeného prostředku.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Hodnoty, které zadáte pro název prostředku a typ, se liší v závislosti na tom, jestli je podřízený prostředek definovaný uvnitř nebo vně nadřazeného prostředku.

## <a name="within-parent-resource"></a>V rámci nadřazeného prostředku

Pokud je definována v rámci nadřazeného typu prostředku, formátujete hodnoty typ a název jako jedno slovo bez lomítek.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Následující příklad ukazuje virtuální síť a podsíť. Všimněte si, že podsíť je obsažená v poli prostředků pro virtuální síť. Název je nastavený na **Subnet1** a typ je nastavený na **podsítě**. Podřízený prostředek je v nadřazeném prostředku označený jako závislý, protože nadřazený prostředek musí existovat předtím, než bude možné nasadit podřízený prostředek.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Úplný typ prostředku je stále `Microsoft.Network/virtualNetworks/subnets` . Neposkytnete, `Microsoft.Network/virtualNetworks/` protože se předpokládá od nadřazeného typu prostředku.

Název podřízeného prostředku je nastavený na **Subnet1** , ale úplný název obsahuje název nadřazeného objektu. Neposkytujete **VNet1** , protože se předpokládá od nadřazeného prostředku.

## <a name="outside-parent-resource"></a>Vnější nadřazený prostředek

Při definování mimo nadřazený prostředek naformátujete typ a s lomítky, aby zahrnovaly nadřazený typ a název.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Následující příklad ukazuje virtuální síť a podsíť, které jsou definovány na kořenové úrovni. Všimněte si, že podsíť není obsažená v poli prostředků pro virtuální síť. Název je nastaven na **VNet1/Subnet1** a typ je nastaven na `Microsoft.Network/virtualNetworks/subnets` . Podřízený prostředek je v nadřazeném prostředku označený jako závislý, protože nadřazený prostředek musí existovat předtím, než bude možné nasadit podřízený prostředek.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření šablon ARM najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Další informace o formátu názvu prostředku při odkazování na prostředek naleznete v [referenční funkci](template-functions-resource.md#reference).
