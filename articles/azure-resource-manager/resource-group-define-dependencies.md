---
title: Nastavení pořadí nasazení pro prostředky Azure | Microsoft Docs
description: Popisuje, jak nastavit jeden prostředek jako závislé na jiný prostředek během nasazení, aby se prostředky nasadí ve správném pořadí.
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
ms.date: 01/03/2017
ms.author: tomfitz
ms.openlocfilehash: d1bb3827036f0d8957ac0830f707da71dd4cd373
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definovat pořadí pro nasazení prostředků v šablonách Azure Resource Manager
Pro daný prostředek může být další prostředky, které musí existovat před nasazením prostředku. Například SQL server, musí existovat před pokusem o nasazení databáze SQL. Tento vztah definujete označením jeden prostředek jako závislý na jiných prostředku. Můžete definovat závislosti s **dependsOn** element, nebo pomocí **odkaz** funkce. 

Správce prostředků vyhodnotí závislosti mezi prostředky a nasadí je v pořadí podle jejich závislé. Pokud nejsou na sobě navzájem závislé prostředky, Resource Manager je nasadí současně. Stačí definování závislostí u prostředků, které jsou nasazeny do stejné šablony. 

## <a name="dependson"></a>dependsOn
V rámci vaší šablony dependsOn element umožňuje definovat jeden prostředek jako závisí na jeden nebo více prostředků. Jeho hodnota může být čárkami oddělený seznam názvy prostředků. 

Následující příklad ukazuje škálovací sadu virtuálních počítačů, které závisí na Vyrovnávání zatížení, virtuální sítě a smyčku, která vytváří více účtů úložiště. Tyto další prostředky nejsou vidět v následujícím příkladu, ale jejich by bylo potřeba existují na jiném místě v šabloně.

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

V předchozím příkladu je zahrnuta závislost na prostředcích, které jsou vytvořené pomocí kopírovací smyčkou s názvem **storageLoop**. Příklad, naleznete v části [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md).

Při definování závislostí, můžete použít obor názvů zprostředkovatele prostředků a typ prostředku, aby se zabránilo nejednoznačnosti. O vysvětlení, nástroj pro vyrovnávání zatížení a virtuální síť, která může mít stejné názvy jako jiné prostředky, například použijte následující formát:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Když jste sklon nesmí být používat dependsOn mapovat vztahy mezi prostředky, je důležité pochopit, proč vaše změny. Například k dokumentu, způsobu vzájemného propojení prostředků, dependsOn není správný přístup. Nemůže zadat dotaz, které prostředky byly definovány v elementu dependsOn po nasazení. Pomocí dependsOn můžete případně ovlivnit času nasazení protože Resource Manager není nasazen v paralelní dva prostředky, které jsou závislé. K dokumentu vztahy mezi prostředky, místo toho použít [propojování prostředků](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Podřízené prostředky
Vlastnost prostředků můžete zadat podřízené prostředky, které se vztahují k prostředku definovaný. Podřízené prostředky se dají jenom definované pěti úrovněmi. Je důležité si uvědomit, že nebyl vytvořen implicitní závislost mezi podřízených prostředků a nadřazený prostředek. Pokud potřebujete podřízených prostředků k nasazení po nadřazeném prostředku, musí explicitně stavu této závislosti s vlastností dependsOn. 

Každý nadřazený prostředek akceptuje pouze určité typy prostředků jako podřízené prostředky. Typy prostředků přijala jsou určené v [schéma šablony](https://github.com/Azure/azure-resource-manager-schemas) nadřazené prostředku. Název typu prostředku podřízené obsahuje název nadřazené typ prostředku, jako například **Microsoft.Web/sites/config** a **Microsoft.Web/sites/extensions** jsou obě podřízené prostředky **Microsoft.Web/sites**.

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

## <a name="reference-function"></a>Reference – funkce
[Odkazu funkci](resource-group-template-functions-resource.md#reference) umožňuje výrazu odvození svou hodnotu z dalších dvojice název a hodnota JSON nebo modul runtime prostředky. Odkaz na výrazy implicitně deklarovat, že jeden prostředek závisí na jiném. Obecný formát je:

```json
reference('resourceName').propertyPath
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

Tento element nebo dependsOn element můžete použít k určení závislostí, ale není potřeba použít pro stejné závislý prostředek. Kdykoli je to možné, vyhněte se přidávání nepotřebné závislostí pomocí implicitní odkaz.

Další informace najdete v tématu [odkazu funkci](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Doporučení pro nastavení závislostí

Při rozhodování, jakou závislosti nastavit, použijte následující pokyny:

* Nastavte jako několik závislosti míře.
* Nastavte podřízených prostředků jako závislý na prostředku jeho nadřazený.
* Použití **odkaz** funkce nastavit implicitní závislosti mezi prostředky, které je potřeba sdílet vlastnost. Nepřidávejte explicitní závislosti (**dependsOn**) Pokud jste již definována implicitní závislostí. Tento přístup snižuje riziko použití nepotřebné závislosti. 
* Nastaví závislost, pokud prostředek nemůže být **vytvořit** bez funkce z jiného prostředku. Nenastavujte závislost, pokud prostředky komunikovat po nasazení.
* Umožňují závislosti cascade bez nastavení je explicitně. Například virtuálního počítače závisí na rozhraní virtuální sítě a virtuální síťové rozhraní závisí na virtuální síť a veřejné IP adresy. Proto tento virtuální počítač je nasazené po všech tří prostředky, ale nenastavíte explicitně virtuální počítač jako závislé na prostředcích všechny tři. Tento postup vysvětluje, pak pořadí závislostí a usnadňuje později změnit šablonu.
* Pokud hodnota se dá určit před nasazením, zkuste nasazení prostředků bez závislosti. Například pokud hodnota konfigurace potřebuje název jiného prostředku, nemusí závislost. V tomto návodu nefunguje vždy vzhledem k tomu, že některé prostředky, ověřte existenci jiný prostředek. Pokud narazíte na chyby, přidejte závislosti. 

Správce prostředků identifikuje cyklické závislosti během ověřování šablony. Pokud se zobrazí chyba oznamující, že existuje cyklická závislost, vyhodnoťte šablony zda všechny závislosti nejsou potřebné, a může být odebrán. Pokud odebrání závislostí nefunguje, se můžete vyhnout cyklické závislosti přesunutím některé operace nasazení do podřízené prostředky, které jsou nasazeny po prostředky, které mají cyklickou závislost. Předpokládejme například, kterou nasazujete dva virtuální počítače, ale je nutné nastavit vlastnosti u každé z nich odkazovat na druhý. Je můžete nasadit v následujícím pořadí:

1. vm1
2. vm2
3. Rozšíření na vm1 závisí na vm1 a virtuálního počítače 2. Rozšíření nastaví hodnoty vm1, který získá z virtuálního počítače 2.
4. Rozšíření na virtuálního počítače 2 závisí na vm1 a virtuálního počítače 2. Rozšíření virtuálního počítače 2, který získá ze vm1 nastaví hodnoty.

Informace o vyhodnocování pořadí nasazení a řešení chyb při závislostí najdete v tématu [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Další postup
* Další informace o řešení potíží s závislosti při nasazení najdete v tématu [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Další informace o vytváření šablon Azure Resource Manageru, najdete v části [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [funkce šablon](resource-group-template-functions.md).

