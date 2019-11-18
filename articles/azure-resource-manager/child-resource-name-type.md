---
title: Podřízené prostředky v šablonách
description: Popisuje, jak nastavit název a typ pro podřízené prostředky v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 2ef20e7c046170a3f73821b878680b46e5a8afea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147397"
---
# <a name="set-name-and-type-for-child-resources"></a>Nastavení názvu a typu pro podřízené prostředky

Podřízené prostředky jsou prostředky, které existují pouze v kontextu jiného prostředku. Například [rozšíření virtuálního počítače](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) nemůže existovat bez [virtuálního počítače](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). Prostředek rozšíření je podřízený virtuálnímu počítači.

V šabloně správce prostředků můžete zadat podřízený prostředek buď v nadřazeném prostředku, nebo mimo nadřazený prostředek. Následující příklad ukazuje podřízený prostředek zahrnutý do vlastnosti Resources nadřazeného prostředku.

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

Následující příklad ukazuje podřízený prostředek mimo nadřazený prostředek. Tento přístup můžete použít, pokud nadřazený prostředek není nasazený ve stejné šabloně nebo pokud chcete použít [kopírování](resource-group-create-multiple.md) k vytvoření více než jednoho podřízeného prostředku.

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
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
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
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
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

Úplný typ prostředku je i **Microsoft. Network/virtualNetworks/podsítí**. Neposkytujeme **Microsoft. Network/virtualNetworks/** , protože se předpokládá od nadřazeného typu prostředku.

Název podřízeného prostředku je nastavený na **Subnet1** , ale úplný název obsahuje název nadřazeného objektu. Neposkytujete **VNet1** , protože se předpokládá od nadřazeného prostředku.

## <a name="outside-parent-resource"></a>Vnější nadřazený prostředek

Při definování mimo nadřazený prostředek naformátujete typ a s lomítky, aby zahrnovaly nadřazený typ a název.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Následující příklad ukazuje virtuální síť a podsíť, které jsou definovány na kořenové úrovni. Všimněte si, že podsíť není obsažená v poli prostředků pro virtuální síť. Název se nastaví na **VNet1/Subnet1** a typ se nastaví na **Microsoft. Network/virtualNetworks/subnets**. Podřízený prostředek je v nadřazeném prostředku označený jako závislý, protože nadřazený prostředek musí existovat předtím, než bude možné nasadit podřízený prostředek.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
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
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
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

* Další informace o vytváření šablon Azure Resource Manager najdete v tématu [vytváření šablon](resource-group-authoring-templates.md). 

* Další informace o formátu názvu prostředku při odkazování na prostředek naleznete v [referenční funkci](resource-group-template-functions-resource.md#reference).
