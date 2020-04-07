---
title: Podřízené prostředky v šablonách
description: Popisuje, jak nastavit název a typ pro podřízené prostředky v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743832"
---
# <a name="set-name-and-type-for-child-resources"></a>Nastavení názvu a typu podřízených prostředků

Podřízené prostředky jsou prostředky, které existují pouze v kontextu jiného prostředku. Například [rozšíření virtuálního počítače](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) nemůže existovat bez [virtuálního počítače](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). Prostředek rozšíření je podřízený virtuální počítač.

V šabloně Správce prostředků můžete zadat podřízený prostředek v rámci nadřazeného prostředku nebo mimo nadřazený prostředek. Následující příklad ukazuje podřízený prostředek zahrnutý do vlastnosti resources nadřazeného prostředku.

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

Následující příklad ukazuje podřízený prostředek mimo nadřazený prostředek. Tento přístup můžete použít, pokud nadřazený prostředek není nasazen ve stejné šabloně nebo pokud chcete použít [kopii](copy-resources.md) k vytvoření více než jednoho podřízeného prostředku.

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

Hodnoty, které zadáte pro název a typ prostředku, se liší v závislosti na tom, zda je podřízený prostředek definován uvnitř nebo vně nadřazeného prostředku.

## <a name="within-parent-resource"></a>V rámci nadřazeného prostředku

Pokud je definována v rámci nadřazeného typu prostředku, naformátujte hodnoty typu a názvu jako jedno slovo bez lomítka.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Následující příklad ukazuje virtuální síť a podsíť. Všimněte si, že podsíť je součástí pole prostředků pro virtuální síť. Název je nastaven na **Podsíť1** a typ je nastaven na **podsítě**. Podřízený prostředek je označen jako závislý na nadřazeném prostředku, protože nadřazený prostředek musí existovat před nasazením podřízeného prostředku.

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

Úplný typ prostředku je stále **Microsoft.Network/virtualNetworks/podsítě**. Neposkytujete **Microsoft.Network/virtualNetworks/** protože se předpokládá z nadřazeného typu prostředku.

Podřízený název prostředku je nastaven na **Podsíť1,** ale celý název obsahuje nadřazený název. Neposkytujete **VNet1,** protože se předpokládá z nadřazeného prostředku.

## <a name="outside-parent-resource"></a>Vnější nadřazený zdroj

Pokud je definován mimo nadřazený prostředek, naformátujte typ a lomítka tak, aby zahrnovala nadřazený typ a název.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Následující příklad ukazuje virtuální síť a podsíť, které jsou definovány na kořenové úrovni. Všimněte si, že podsíť není zahrnuta v poli prostředků pro virtuální síť. Název je nastaven na **VNet1/Subnet1** a typ je nastaven na **Microsoft.Network/virtualNetworks/podsítě**. Podřízený prostředek je označen jako závislý na nadřazeném prostředku, protože nadřazený prostředek musí existovat před nasazením podřízeného prostředku.

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

* Další informace o vytváření šablon Azure Resource Manageru najdete v [tématu Vytváření šablon](template-syntax.md).

* Informace o formátu názvu prostředku při odkazování na prostředek naleznete v [referenční funkci](template-functions-resource.md#reference).
