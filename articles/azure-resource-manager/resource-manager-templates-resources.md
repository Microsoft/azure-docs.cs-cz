---
title: Prostředky šablony Azure Resource Manager | Microsoft Docs
description: Popisuje části zdroje šablon Azure Resource Manager pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 12dc5921cc1977b53f0457d89537193eadded188
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Oddílu prostředků šablon Azure Resource Manager

V části prostředky definujete prostředky, které jsou nasazené a aktualizovat. V této části můžete získat složité, protože je potřeba pochopit, typy, které nasazujete zadejte správné hodnoty.

## <a name="available-properties"></a>Dostupné vlastnosti

Můžete definovat prostředky s následující strukturou:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
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
| podmínka | Ne | Logická hodnota, která určuje, jestli je nasazené prostředku. |
| apiVersion |Ano |Verze rozhraní REST API pro vytvoření prostředku. |
| type |Ano |Typ prostředku. Tato hodnota je kombinací obor názvů zprostředkovatele prostředků a typ prostředku (například **Microsoft.Storage/storageAccounts**). |
| jméno |Ano |Název prostředku. Název musí splňovat omezení součást URI definované v RFC3986. Kromě toho služby Azure, které zveřejňují názvu prostředku třetí stranou ověřit název, který má Ujistěte se, zda není pokus o zfalšovat jiné identity. |
| location |Je to různé. |Podporované geografické umístění zadaného prostředku. Můžete vybrat některý z dostupných umístění, ale obvykle má smysl vyberte ten, který je blízko vaši uživatelé. Obvykle také má smysl umístit prostředky, které vzájemně spolupracovat ve stejné oblasti. Většina typů prostředků vyžadují umístění, ale některé typy (například přiřazení role) nevyžadují umístění. |
| tags |Ne |Značky, které jsou přidružené k prostředku. Použití značek k logicky uspořádání prostředků vašeho předplatného. |
| Komentáře |Ne |Poznámky pro dokumentaci prostředky ve vaší šabloně |
| Kopírovat |Ne |V případě potřeby více než jednu instanci počet zdrojů pro vytvoření. Paralelní je výchozí režim. Zadejte sériové režim, když nechcete, aby všechny nebo prostředky do nasazení ve stejnou dobu. Další informace najdete v tématu [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md). |
| dependsOn |Ne |Prostředky, které musí být nasazené, než je nasazený tento prostředek. Správce prostředků vyhodnotí závislosti mezi prostředky a nasadí je ve správném pořadí. Pokud nejsou na sobě navzájem závislé prostředky, jsou nasazeny současně. Hodnota může být čárkami oddělený seznam prostředek názvy nebo jedinečné identifikátory prostředků. Zobrazit seznam pouze těch prostředků, které jsou nasazeny v této šabloně. Prostředky, které nejsou v této šabloně definovány již musí existovat. Vyhněte se přidání nepotřebné závislostí, jak mohou zpomalit nasazení a vytvoření cyklické závislosti. Pokyny v závislosti na nastavení najdete v tématu [definování závislostí v šablonách Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Ne |Nastavení konfigurace specifických prostředků. Hodnoty pro vlastnosti jsou stejné jako hodnoty, které zadáte v textu požadavku REST API operaci (metoda PUT) k vytvoření prostředku. Můžete také zadat pole kopie vytvořit více instancí vlastnosti. |
| SKU | Ne | Některé prostředky povolit hodnoty, které definují SKU pro nasazení. Například můžete zadat typ redundance pro účet úložiště. |
| Typ | Ne | Některé prostředky povolit hodnotu, která definuje typ prostředku, který nasazujete. Například můžete zadat typ Cosmos DB k vytvoření. |
| plán | Ne | Některé prostředky povolit hodnoty, které definují plán pro nasazení. Můžete například zadat pro marketplace bitovou kopii virtuálního počítače. | 
| zdroje |Ne |Podřízené prostředky, které jsou závislé na prostředku definovaný. Zadejte pouze typy prostředků, které jsou povoleny schématem nadřazený prostředek. Plně kvalifikovaný typ prostředku podřízené obsahuje nadřazený typ prostředku, jako například **Microsoft.Web/sites/extensions**. Závislost na nadřazeném prostředku není implicitní. Je nutné explicitně zadat tuto závislost. |

## <a name="resource-specific-values"></a>Hodnoty v závislosti na prostředek

**ApiVersion**, **typ**, a **vlastnosti** elementy se liší pro jednotlivé typy prostředků. **Sku**, **druhu**, a **plán** prvky jsou dostupné pro některé typy prostředků, ale ne všechny. Chcete-li určit hodnoty těchto vlastností, přečtěte si téma [odkaz na šablonu](/azure/templates/).

## <a name="resource-names"></a>Názvy prostředků
Obecně platí pracujete s tři typy názvy prostředků ve službě Správce prostředků:

* Názvy prostředků, které musí být jedinečný.
* Názvy prostředků, které nemusí být jedinečný, ale můžete rozhodnout pro poskytnutí název, který vám pomůže určit prostředku.
* Názvy prostředků, které mohou být obecný.

### <a name="unique-resource-names"></a>Názvy jedinečný prostředků
Je nutné zadat název jedinečný prostředek pro jakýkoli typ prostředku, který má přístup koncový bod data. Některé běžné typy prostředků, které vyžadují jedinečný název patří:

* Úložiště Azure<sup>1</sup> 
* Funkce Web Apps ve službě Azure App Service
* SQL Server
* Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> názvy účtů úložiště musí být také malými písmeny, 24 znaků nebo méně, a nemusí být všechny pomlčky.

Při nastavení názvu, můžete ručně vytvořit jedinečný název nebo používat [uniqueString()](resource-group-template-functions-string.md#uniquestring) funkce při generování názvu. Můžete také chtít přidat předponu nebo příponu k **uniqueString** výsledek. Úprava jedinečný název můžete další snadno identifikovat typ prostředku z názvu. Můžete například vygenerovat jedinečný název pro účet úložiště pomocí následující proměnnou:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Názvy prostředků pro identifikaci
Některé typy prostředků, které můžete chtít názvu, ale jejich názvy nemusí být jedinečný. Pro tyto typy prostředků můžete zadat název, který identifikuje prostředek kontextu a typ prostředku.

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

### <a name="generic-resource-names"></a>Obecný zdroj názvy
Pro typy prostředků, které většinou přistupovat prostřednictvím jiného prostředku můžete použít obecný název, který je pevně zakódovaná v šabloně. Například můžete nastavit standardní, obecný název pravidla brány firewall na serveru SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Umístění
Při nasazování šablony, je nutné zadat umístění každého prostředku. Různé typy prostředků jsou podporovány v různých umístěních. Chcete-li zobrazit seznam umístění, které jsou k dispozici pro vaše předplatné pro konkrétní typ prostředku, použijte prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. 

Následující příklad používá prostředí PowerShell a získat umístění pro `Microsoft.Web\sites` typ prostředku:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Následující příklad používá Azure CLI 2.0 získat umístění `Microsoft.Web\sites` typ prostředku:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Po určení podporovaná umístění pro vaše prostředky, nastavte toto umístění ve vaší šabloně. Nejjednodušší způsob, jak nastavit tuto hodnotu je vytvoření skupiny prostředků v místě, které podporuje tyto typy prostředků a nastavit každý umístění na `[resourceGroup().location]`. Můžete znovu nasaďte šablonu, kterou chcete skupiny prostředků v různých umístěních a nezmění žádné hodnoty v šabloně nebo parametry. 

Následující příklad ukazuje, účet úložiště, který je nasazen do stejného umístění jako pro skupinu prostředků:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
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

Pokud potřebujete používat pevné kódování umístění v šabloně, zadejte název jednoho z podporovaných oblastí. Následující příklad ukazuje, účet úložiště, který je vždy nasazený do Sever střední USA:

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

### <a name="add-tags-to-your-template"></a>Do šablony přidat značky

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Podřízené prostředky

V rámci některé typy prostředků můžete také definovat pole podřízené prostředky. Podřízené prostředky jsou prostředky, které existují pouze v kontextu jiný prostředek. Databáze SQL například nemůže existovat bez systému SQL server, databáze je podřízený server. Můžete definovat databáze v definici pro server.

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

Když vnořený, typ je nastavený na `databases` , ale jeho typ prostředku úplné `Microsoft.Sql/servers/databases`. Nezadáte `Microsoft.Sql/servers/` se předpokládá z nadřazeného typu prostředku. Název prostředku podřízené je nastaven `exampledatabase` ale úplný název obsahuje název nadřazené. Nezadáte `exampleserver` se předpokládá z nadřazené prostředku.

Není ve formátu podřízený typ prostředku: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Formát názvu podřízené prostředků je: `{parent-resource-name}/{child-resource-name}`

Ale není nutné definovat databázi v rámci serveru. Můžete definovat podřízených prostředků na nejvyšší úrovni. Tento postup můžete použít, pokud nadřazený prostředek není nasazený ve stejné šablony, nebo pokud chcete použít `copy` vytvořit více podřízené prostředky. S tímto přístupem musíte zadat typ prostředku úplné a zahrnout název nadřazené prostředku v názvu prostředku podřízené.

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

Při vytváření plně kvalifikovaný odkaz na prostředek, není jednoduše zřetězení těchto dvou pořadí kombinovat segmenty z typu a název.  Místo toho po oboru názvů, použijte posloupnost *nebo název typu* dvojice z nejméně specifická k nejvíce:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Příklad:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` správnost `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` není správný

## <a name="recommendations"></a>Doporučení
Při práci s prostředky, může být užitečné následující informace:

* Chcete-li jiné přispěvatele pochopit účel prostředku, zadejte **komentáře** pro každý zdroj v šabloně:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Pokud používáte *veřejný koncový bod* ve vaší šabloně (například Azure Blob storage veřejný koncový bod), *proveďte pevné kódování* obor názvů. Použití **odkaz** funkce, která se dynamicky načíst obor názvů. Tento postup můžete použít k nasazení šablony do prostředí jiný obor názvů veřejné beze změny ručně koncového bodu v šabloně. Verze rozhraní API se nastaví na stejnou verzi, kterou používáte pro účet úložiště v šabloně:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Pokud účet úložiště je nasazen do stejné šablony, kterou vytváříte, není potřeba zadat obor názvů zprostředkovatele, když odkazujete na prostředek. Následující příklad ukazuje zjednodušenou syntaxi:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Pokud máte jiné hodnoty v šabloně, které jsou nakonfigurovány pro použití veřejného obor názvů, změňte tyto hodnoty tak, aby odrážela stejné **odkaz** funkce. Například můžete nastavit **storageUri** vlastnost diagnostického profilu virtuálního počítače:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Můžete také použít stávající účet úložiště, který je v jiné skupině prostředků:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Přiřaďte veřejné IP adresy k virtuálnímu počítači jenom v případě, že aplikace vyžaduje. Pro připojení k virtuálnímu počítači (VM) pro ladění, nebo pro správu nebo správu účely, použijte příchozích pravidel NAT, bránu virtuální sítě nebo jumpbox.
   
     Další informace o připojení k virtuálním počítačům, najdete v tématu:
   
   * [Spustit virtuální počítače pro architekturu N-vrstvá v Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Nastavit přístup WinRM pro virtuální počítače ve službě Správce prostředků Azure](../virtual-machines/windows/winrm.md)
   * [Externí přístup k virtuálnímu počítači povolit pomocí portálu Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Povolit externí přístup k virtuálnímu počítači pomocí prostředí PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Povolit externí přístup k virtuálním počítačům s Linuxem pomocí rozhraní příkazového řádku Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* **DomainNameLabel** vlastnost pro veřejné IP adresy musí být jedinečný. **DomainNameLabel** hodnota musí být v rozmezí 3 až 63 znaků a postupujte podle pravidla určeného tento regulární výraz: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Protože **uniqueString** funkce generuje řetězec, který je 13 znaků, **dnsPrefixString** parametr je omezený na 50 znaků:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Když přidáte heslo k rozšíření vlastních skriptů, použijte **commandToExecute** vlastnost **protectedSettings** vlastnost:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > K zajištění, že tajné klíče se šifrují, pokud jsou předávány jako parametry pro virtuální počítače a rozšíření, použijte **protectedSettings** vlastnost relevantní rozšíření.
   > 
   > 


## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkcích, které můžete použít z v rámci šablon najdete v tématu [funkce šablon Azure Resource Manager](resource-group-template-functions.md).
* Pokud chcete kombinovat několik šablon během nasazení, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Musíte používat prostředky, které existují v jiné skupině prostředků. Tento scénář je běžný, při práci s účty úložiště a virtuální sítě, které jsou sdíleny více skupin prostředků. Další informace najdete v tématu [resourceId funkce](resource-group-template-functions-resource.md#resourceid).
* Informace o omezení přístupu názvem prostředků najdete v tématu [doporučená zásady vytváření názvů pro prostředky Azure](../guidance/guidance-naming-conventions.md).