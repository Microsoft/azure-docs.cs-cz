---
title: Prostředky šablon Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje části zdroje šablony Azure Resource Manageru pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: 01aacf8815ce4150eb1c243d4337f52c4e0b03e9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697034"
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Oddíl prostředků šablon Azure Resource Manageru

V části prostředky definovat prostředky, které jsou nasazené a aktualizovat. V této části můžete získat složité, protože musíte porozumět typům, které nasazení provádíte do zadejte správné hodnoty.

## <a name="available-properties"></a>Dostupné vlastnosti

Můžete definovat prostředky s následující strukturou:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| condition | Ne | Logická hodnota, která určuje, zda prostředek se zřídí během tohoto nasazení. Když `true`, je prostředek vytvořený během nasazení. Když `false`, prostředek se přeskočí pro toto nasazení. |
| apiVersion |Ano |Verze rozhraní REST API pro použití při vytváření prostředku. |
| type |Ano |Typ prostředku. Tato hodnota je kombinací obor názvů zprostředkovatele prostředků a typ prostředku (například **Microsoft.Storage/storageAccounts**). |
| jméno |Ano |Název prostředku Název musí následovat identifikátor URI součásti omezení RFC3986. Kromě toho služby Azure, které zpřístupňují název prostředku se třetími stranami ověřit název, který má ujistit, že není pokus zfalšovat jiné identity. |
| location |Různé |Podporované geografické umístění zadaného prostředku. Můžete vybrat některý z dostupných umístění, ale obvykle je vhodné vybrat ten, který je blízko vašim uživatelům. Obvykle je také vhodné umístit prostředky, které spolu interagují ve stejné oblasti. Většina typů prostředků vyžaduje umístění, ale některé typy (jako je například přiřazení role) nevyžadují umístění. |
| tags |Ne |Značky, které jsou spojeny s prostředkem. Použití značek logicky tak uspořádat prostředky napříč vašeho předplatného. |
| Komentáře |Ne |Poznámky pro dokumentaci prostředků ve vaší šabloně. Další informace najdete v tématu [komentáře v šablonách](resource-group-authoring-templates.md#comments). |
| Kopírovat |Ne |V případě potřeby je více než jednu instanci, kolik prostředků k vytvoření. Paralelní je výchozí režim. Zadejte sériové režim, když nechcete, aby všechny nebo prostředky k nasazení ve stejnou dobu. Další informace najdete v tématu [vytvořit několik instancí prostředků v Azure Resource Manageru](resource-group-create-multiple.md). |
| dependsOn |Ne |Prostředky, které musí být nasazený před nasazením tento prostředek. Resource Manager vyhodnotí závislosti mezi prostředky a nasadí ve správném pořadí. Pokud nejsou na sobě navzájem závislé prostředky, kde jsou nasazeny současně. Hodnota může být čárkou oddělený seznam prostředek názvy nebo jedinečné identifikátory prostředků. Pouze výpis prostředků, které jsou nasazené v této šabloně. Prostředky, které nejsou definovány v této šabloně už musí existovat. Vyhněte se přidává zbytečné závislostí může zpomalit vaše nasazení a vytvoření cyklické závislosti. Pokyny k nastavení závislostí v tématu [definování závislostí v šablonách Azure Resource Manageru](resource-group-define-dependencies.md). |
| properties |Ne |Nastavení konfigurace specifických pro prostředky. Hodnoty pro tyto vlastnosti jsou stejné jako hodnoty, které zadáte v textu požadavku pro operaci rozhraní REST API (metodu PUT) a vytvoří prostředek. Můžete také zadat pole kopie vytvořit několik instancí vlastnosti. |
| SKU | Ne | Některé prostředky povolit hodnoty, které definují skladová položka pro nasazení. Můžete například zadat typ redundance účtu úložiště. |
| Typ | Ne | Některé prostředky povolit hodnotu, která definuje typ prostředku, který nasadíte. Můžete například zadat typ služby Cosmos DB k vytvoření. |
| plán | Ne | Některé prostředky povolit hodnoty, které definují plán pro nasazení. Můžete například zadat image marketplace pro virtuální počítač. | 
| zdroje |Ne |Podřízené prostředky, které jsou závislé na prostředku definuje. Zadejte pouze typy prostředků, které jsou povoleny ve schématu nadřazený prostředek. Plně kvalifikovaný typ podřízený prostředek obsahuje nadřazený typ prostředku, jako například **Microsoft.Web/sites/extensions**. Závislost na nadřazený prostředek není zahrnuta. Je nutné explicitně definovat dané závislosti. |

## <a name="condition"></a>Podmínka

Pokud během nasazení musíte rozhodnout, jestli se mají vytvořit prostředek, použijte `condition` elementu. Hodnota pro tento element se přeloží na hodnotu true nebo false. Pokud je hodnota true, je prostředek vytvořený. Pokud je hodnota false, nevytvoří se prostředek. Hodnota dá používat jedině pro celý prostředek.

Obvykle tuto hodnotu použijete, pokud chcete vytvořit nový prostředek, nebo použijte již existující. Například, chcete-li určit, jestli je nasazená nový účet úložiště nebo existující účet úložiště se používá, použijte:

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

Kompletní příklad šablony, která se používá `condition` prvku, naleznete v tématu [virtuálního počítače s novou nebo existující virtuální sítě, úložiště a veřejnou IP adresu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="resource-specific-values"></a>Hodnoty specifické podle prostředků

**ApiVersion**, **typ**, a **vlastnosti** prvky se liší pro každý typ prostředku. **Sku**, **druh**, a **plán** prvky jsou k dispozici pro některé typy prostředků, ale ne všechny. Chcete-li zjistit hodnoty těchto vlastností najdete v článku [referenčními informacemi k šablonám](/azure/templates/).

## <a name="resource-names"></a>Názvy prostředků

Obecně platí práci s tři typy názvů prostředků v Resource Manageru:

* Názvy prostředků, které musí být jedinečný.
* Názvy prostředků, které nemusí být jedinečný, ale můžete rozhodnout pro poskytnutí název, který vám pomůže identifikovat prostředek.
* Názvy prostředků, které mohou být obecný.

### <a name="unique-resource-names"></a>Názvy jedinečný prostředek

Zadejte název jedinečné prostředků pro libovolný typ prostředku, který má koncový bod data access. Některé běžné typy prostředků, které vyžadují jedinečný název patří:

* Azure Storage<sup>1</sup> 
* Funkce Web Apps ve službě Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> názvy účtů úložiště také musí obsahovat malá písmena, 24 znaků nebo méně, a není nutné žádné pomlčky.

Při nastavování názvu, můžete ručně vytvořit jedinečný název nebo použít [uniqueString()](resource-group-template-functions-string.md#uniquestring) funkci generování názvu. Můžete také chtít přidat předponu nebo příponu k **uniqueString** výsledek. Úprava jedinečný název můžete vám umožní snadno identifikovat typ prostředku z názvu. Můžete například vygenerovat jedinečný název pro účet úložiště pomocí následující proměnnou:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Názvy prostředků pro identifikaci
Některé typy prostředků, které můžete chtít název, ale jejich názvy nemusí být jedinečný. Pro tyto typy prostředků můžete zadat název, který identifikuje prostředek kontextu a typ prostředku.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Názvy obecný prostředek
Pro typy prostředků, které většinou přistupujete prostřednictvím různých prostředků můžete použít obecný název, který je pevně zakódované v šabloně. Můžete třeba nastavit standardní, obecný název pravidla brány firewall na serveru SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Umístění
Při nasazování šablony, je nutné zadat umístění každého prostředku. Různé typy prostředků jsou podporovány v různých umístěních. Podporovaná umístění pro typ prostředku, získáte [poskytovatelé a typy prostředků Azure](resource-manager-supported-services.md).

Parametr použít k určení umístění pro prostředky a nastavení výchozí hodnoty `resourceGroup().location`.

Následující příklad ukazuje účet úložiště, která je nasazena do umístění zadaného jako parametr:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

Pokud potřebujete používat pevné kódování umístění ve vaší šabloně, zadejte název jedné z podporovaných oblastí. Následující příklad ukazuje účet úložiště, který je vždy nasazen na střed USA – sever:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
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

## <a name="tags"></a>Značky
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Přidání značek do šablony

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Podřízené prostředky

V rámci některé typy prostředků můžete také definovat pole podřízené prostředky. Podřízené prostředky jsou prostředky, které existují pouze v rámci kontextu jiný prostředek. Například databáze SQL nemůže existovat bez serveru SQL server, databázi je podřízený server. Můžete definovat databáze v rámci definice pro server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Pokud vnořený, typ je nastavený na `databases` , ale jeho typ úplné prostředku je `Microsoft.Sql/servers/databases`. Nezadáte `Microsoft.Sql/servers/` se předpokládá z nadřazeného typu prostředku. Název prostředku podřízené nastavený na `exampledatabase` ale úplný název obsahuje název nadřazené. Nezadáte `exampleserver` se předpokládá z nadřazeného zdroje.

Formát typu podřízeného prostředku je: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Formát názvu podřízených prostředků je: `{parent-resource-name}/{child-resource-name}`

Ale není nutné definovat databáze v rámci serveru. Můžete definovat podřízený prostředek na nejvyšší úrovni. Tento přístup může použít, pokud se nadřazený prostředek není nasazený ve stejné šabloně, nebo pokud chcete použít `copy` vytvořit více než jeden podřízený prostředek. S tímto přístupem poskytují úplnou prostředků a zahrnout název nadřazeného prostředku v názvu prostředku podřízeného.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Při vytváření plně kvalifikovaný odkaz na prostředek, není pořadí zkombinovat segmenty z typu a název jednoduše zřetězení těchto dvou. Místo toho za názvovým prostorem, použijte sekvenci *typ nebo název* dvojice z nejméně specifická, které budou nejvíce specifické:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Příklad:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` správnost `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` není správná



## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Doporučení o vytváření šablon naleznete v tématu [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md).
* Budete muset použít prostředky, které existují v rámci jiné skupiny prostředků. Tento postup je běžný při práci s účty úložiště nebo virtuální sítě, které jsou sdíleny napříč několika skupin prostředků. Další informace najdete v tématu [funkce resourceId](resource-group-template-functions-resource.md#resourceid).
* Informace o omezení názvů prostředků najdete v tématu [doporučené zásady vytváření názvů pro prostředky Azure](../guidance/guidance-naming-conventions.md).