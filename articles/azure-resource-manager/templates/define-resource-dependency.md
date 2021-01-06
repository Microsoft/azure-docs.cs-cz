---
title: Nastavení pořadí nasazení pro prostředky
description: Popisuje, jak nastavit jeden prostředek Azure jako závislý na jiném prostředku během nasazování. Závislosti zajistí, že se prostředky nasadí ve správném pořadí.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934743"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definování pořadí nasazení prostředků v šablonách ARM

Při nasazování prostředků možná budete muset zajistit, aby existovaly některé prostředky před dalšími prostředky. Před nasazením databáze potřebujete například logický SQL Server. Tento vztah vytvoříte tak, že označíte jeden prostředek jako závislý na jiném prostředku. Použijte `dependsOn` element k definování explicitní závislosti. K definování implicitní závislosti použijte funkce **odkaz** nebo **list** .

Azure Resource Manager vyhodnocuje závislosti mezi prostředky a nasadí je v jejich závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Stačí definovat závislosti pro prostředky, které jsou nasazeny ve stejné šabloně.

## <a name="dependson"></a>dependsOn

V rámci šablony Azure Resource Manager (šablona ARM) `dependsOn` umožňuje element definovat jeden prostředek jako závislý na jednom nebo více prostředcích. Jeho hodnota je pole JavaScript Object Notation (JSON) řetězců, z nichž každý je název nebo ID prostředku. Pole může obsahovat prostředky, které jsou [podmíněně nasazeny](conditional-resource-deployment.md). Pokud není podmíněný prostředek nasazený, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

Následující příklad ukazuje síťové rozhraní, které závisí na virtuální síti, skupině zabezpečení sítě a veřejné IP adrese. Úplnou šablonu najdete v tématu [Šablona pro rychlý Start pro virtuální počítač se systémem Linux](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

I když může být pro `dependsOn` mapování vztahů mezi prostředky použit sklon, je důležité pochopit, proč to děláte. Například k dokumentaci, jak jsou prostředky propojeny, `dependsOn` není správným přístupem. Po nasazení nemůžete zadat dotaz na prostředky, které byly definovány v `dependsOn` elementu. Nastavení zbytečných závislostí zpomaluje dobu nasazení, protože Správce prostředků nemůže tyto prostředky paralelně nasadit.

## <a name="child-resources"></a>Podřízené prostředky

Implicitní závislost nasazení není automaticky vytvořena mezi [podřízeným prostředkem](child-resource-name-type.md) a nadřazeným prostředkem. Pokud potřebujete nasadit podřízený prostředek za nadřazeným prostředkem, nastavte `dependsOn` vlastnost.

Následující příklad ukazuje logický SQL Server a databázi. Všimněte si, že explicitní závislost je definovaná mezi databází a serverem, a to i v případě, že je databáze podřízená serveru.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Úplnou šablonu najdete v tématu [Šablona pro rychlý Start pro Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>funkce odkazu a seznamu

[Odkazovaná funkce](template-functions-resource.md#reference) umožňuje výrazu odvodit svoji hodnotu z jiných párů názvu a hodnoty JSON nebo prostředků modulu runtime. [Seznam *](template-functions-resource.md#list) vrátí hodnoty pro prostředek ze seznamu operace.

Výrazy odkazu a seznamu implicitně deklaruje, že jeden prostředek závisí na jiném. Kdykoli je to možné, použijte implicitní odkaz, abyste se vyhnuli přidání zbytečné závislosti.

Chcete-li vyhovět implicitní závislosti, přečtěte si prostředek podle názvu, ne podle ID prostředku. Pokud předáte ID prostředku do funkce reference nebo list, implicitní odkaz se nevytvoří.

Obecný formát `reference` funkce je:

```json
reference('resourceName').propertyPath
```

Obecný formát `listKeys` funkce je:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

V následujícím příkladu je koncový bod CDN explicitně závislý na profilu CDN a implicitně závisí na webové aplikaci.

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

Další informace najdete v tématu [referenční funkce](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Závislá na prostředcích ve smyčce

K nasazení prostředků závislých na prostředcích [kopírovací smyčky](copy-resources.md)máte dvě možnosti. Můžete buď nastavit závislost na jednotlivých prostředcích ve smyčce nebo na celé smyčce.

> [!NOTE]
> U většiny scénářů byste měli nastavit závislost na jednotlivých prostředcích v rámci smyčky kopírování. Závisí jenom na celé smyčce, pokud potřebujete, aby všechny prostředky ve smyčce existovaly před vytvořením dalšího prostředku. Nastavením závislosti na celé smyčce dojde k výraznému rozšíření grafu závislostí, zejména pokud tyto provedené prostředky závisí na jiných prostředcích. Rozšířené závislosti usnadňují efektivní dokončení nasazení.

Následující příklad ukazuje, jak nasadit více virtuálních počítačů. Šablona vytvoří stejný počet síťových rozhraní. Každý virtuální počítač je závislý na jednom síťovém rozhraní, nikoli na celé smyčce.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

Následující příklad ukazuje, jak nasadit tři účty úložiště před nasazením virtuálního počítače. Všimněte si, že `copy` element je `name` nastaven na `storagecopy` a `dependsOn` element pro virtuální počítač je také nastaven na `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Cyklické závislosti

Správce prostředků identifikuje cyklické závislosti během ověřování šablony. Pokud se zobrazí chyba cyklické závislosti, vyhodnoťte šablonu a zjistěte, jestli je možné odebrat nějaké závislosti. Pokud nefungují žádné závislosti, můžete se vyhnout cyklické závislosti přesunutím některých operací nasazení do podřízených prostředků. Podřízené prostředky nasaďte za prostředky, které mají cyklickou závislost. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte nastavit vlastnosti pro každý z nich, který odkazuje na druhý. Můžete je nasadit v tomto pořadí:

1. vm1
2. VM2
3. Přípona v VM1 závisí na VM1 a VM2. Rozšíření nastaví hodnoty na VM1, které získá z VM2.
4. Přípona v VM2 závisí na VM1 a VM2. Rozšíření nastaví hodnoty na VM2, které získá z VM1.

Informace o vyhodnocení pořadí nasazení a řešení chyb závislostí najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: vytvoření šablon ARM se závislými prostředky](template-tutorial-create-templates-with-dependent-resources.md).
* Microsoft Learn modul, který pokrývá závislosti prostředků, najdete v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Doporučení pro nastavení závislostí najdete v článku [osvědčené postupy pro šablonu ARM](template-best-practices.md).
* Další informace o závislostech při řešení potíží při nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
* Další informace o vytváření šablon Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Seznam dostupných funkcí v šabloně najdete v tématu [funkce šablon ARM](template-functions.md).
