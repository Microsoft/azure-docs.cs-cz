---
title: Struktura a syntaxe souborů bicep
description: Popisuje strukturu a vlastnosti souboru bicep pomocí deklarativní syntaxe.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168684"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Pochopení struktury a syntaxe souborů bicep

Tento článek popisuje strukturu souboru bicep. Zobrazuje různé části souboru a vlastnosti, které jsou k dispozici v těchto oddílech.

Tento článek je určený pro uživatele, kteří mají určitou znalost souborů bicep. Poskytuje podrobné informace o struktuře šablony. Podrobný kurz, který vás provede procesem vytvoření souboru bicep, najdete v tématu [kurz: vytvoření a nasazení prvního Azure Resource Manager souboru bicep](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Formát šablon

Soubor bicep obsahuje následující prvky. Prvky se mohou objevit v libovolném pořadí.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

Následující příklad ukazuje implementaci těchto prvků.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Cílový obor

Ve výchozím nastavení je cílový obor nastaven na hodnotu `resourceGroup` . Pokud provádíte nasazení na úrovni skupiny prostředků, nemusíte v souboru bicep nastavovat cílový obor.

Povolené hodnoty jsou následující:

* Skupina prostředků **– výchozí** hodnota používaná pro [nasazení skupin prostředků](deploy-to-resource-group.md).
* **předplatné** – používá se pro [nasazení předplatných](deploy-to-subscription.md).
* skupina **pro správu** – používá se pro [nasazení skupin pro správu](deploy-to-management-group.md).
* **tenant** – používá se pro [nasazení klientů](deploy-to-tenant.md).

## <a name="parameters"></a>Parametry

Použijte parametry pro hodnoty, které se musí v různých nasazeních lišit. Můžete definovat výchozí hodnotu parametru, který se použije v případě, že během nasazování není zadána žádná hodnota.

Například můžete přidat parametr SKU pro určení různých velikostí prostředku. Můžete použít funkce šablon pro vytvoření výchozí hodnoty, jako je například získání umístění skupiny prostředků.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Dostupné datové typy najdete [v tématu datové typy v šablonách](data-types.md).

Další informace najdete v tématu [parametry v šablonách](template-parameters.md).

## <a name="parameter-decorators"></a>Dekoratéry parametru

Pro každý parametr můžete přidat jednu nebo více dekoratéry. Tyto dekoratéry definují hodnoty, které jsou povoleny pro parametr. Následující příklad určuje SKU, které lze nasadit pomocí souboru bicep.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

V následující tabulce jsou popsány dostupné dekoratéry a jejich použití.

| Dekoratér | Platí pro | Argument | Description |
| --------- | ---- | ----------- | ------- |
| povoleno | Vše | array | Povolené hodnoty pro parametr Pomocí tohoto dekoratéru zajistěte, aby uživatel poskytoval správné hodnoty. |
| description | Vše | řetězec | Text, který vysvětluje, jak použít parametr Popis se uživatelům zobrazí prostřednictvím portálu. |
| maxLength | pole, řetězec | int | Maximální délka parametrů řetězce a pole. Hodnota je včetně. |
| maxValue | int | int | Maximální hodnota parametru celého čísla. Tato hodnota je včetně. |
| zprostředkovatele identity | Vše | object | Vlastní vlastnosti, které se mají použít u parametru Může zahrnovat vlastnost Description, která je ekvivalentní dekoratér popisu. |
| minLength | pole, řetězec | int | Minimální délka parametrů řetězce a pole. Hodnota je včetně. |
| minValue | int | int | Minimální hodnota pro celočíselný parametr. Tato hodnota je včetně. |
| požadavk | řetězec, objekt | žádné | Označí parametr jako zabezpečený. Hodnota zabezpečeného parametru se neuloží do historie nasazení a není zaprotokolovaná. Další informace najdete v tématu [zabezpečení řetězců a objektů](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Proměnné

Používejte proměnné pro složité výrazy, které se opakují v souboru bicep. Například můžete přidat proměnnou pro název prostředku, který je vytvořen zřetězením několika hodnot dohromady.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Neurčíte [datový typ](data-types.md) pro proměnnou. Místo toho se datový typ odvozuje od hodnoty.

Další informace naleznete v tématu [proměnné v šablonách](template-variables.md).

## <a name="resource"></a>Prostředek

Pomocí `resource` klíčového slova definujte prostředek, který chcete nasadit. Vaše deklarace prostředku obsahuje symbolický název prostředku. Pokud potřebujete získat hodnotu z prostředku, použijete tento symbolický název v ostatních částech souboru bicep.

Deklarace prostředků zahrnuje také typ prostředku a verzi rozhraní API.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

Do své deklarace prostředku zahrnete vlastnosti pro typ prostředku. Tyto vlastnosti jsou pro každý typ prostředku jedinečné.

Další informace najdete v tématu [deklarace prostředků v šablonách](resource-declaration.md).

K [podmíněnému nasazení prostředku](conditional-resource-deployment.md)přidejte `if` výraz.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

Chcete-li [nasadit více než jednu instanci](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) typu prostředku, přidejte `for` výraz. Výraz může iterovat členy pole.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Moduly

Použijte moduly pro propojení s dalšími bicep soubory, které obsahují kód, který chcete znovu použít. Modul obsahuje jeden nebo více prostředků k nasazení. Tyto prostředky se nasazují spolu s dalšími prostředky v souboru bicep.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Symbolický název umožňuje odkazování modulu v souboru jinam. Můžete například získat výstupní hodnotu z modulu pomocí symbolického názvu a názvu výstupní hodnoty.

Podobně jako u prostředků můžete vytvořit podmíněně nebo iterativní nasazení modulu. Syntaxe je stejná pro moduly jako prostředky.

Další informace najdete v tématu [použití modulů bicep](bicep-modules.md).

## <a name="resource-and-module-decorators"></a>Prostředek a modul dekoratéry

Dekoratér můžete přidat do definice prostředku nebo modulu. Jedinou podporovanou dekoratér je `batchSize(int)` . Můžete ho použít jenom pro definici prostředku nebo modulu, který používá `for` výraz.

Ve výchozím nastavení jsou prostředky nasazeny paralelně. Neznáte pořadí, ve kterém se dokončí. Když přidáte `batchSize` dekoratér, nasadíte instance do sériového tvaru. Použijte celočíselný argument k určení počtu instancí, které mají být nasazeny paralelně.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Další informace najdete v tématu [sériové nebo paralelní](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Výstupy

Použijte výstupy pro návrat hodnoty z nasazení. Obvykle vracíte hodnotu z nasazeného prostředku, pokud potřebujete tuto hodnotu znovu použít pro jinou operaci.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Zadejte [datový typ](data-types.md) výstupní hodnoty.

Další informace najdete v tématu [výstupy v šablonách](template-outputs.md).

## <a name="comments"></a>Komentáře

Použití `//` pro komentáře na jednom řádku nebo `/* ... */` pro víceřádkové komentáře

Následující příklad ukazuje Jednořádkový komentář.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

Následující příklad ukazuje Víceřádkový komentář.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Víceřádkové řetězce

Řetězec můžete rozdělit na více řádků. `'''`Ke spuštění a ukončení víceřádkového řetězce použijte tři jednoduché znaky. 

Znaky uvnitř víceřádkového řetězce jsou zpracovávány tak, jak jsou. Řídicí znaky jsou zbytečné. Nemůžete zahrnout `'''` do víceřádkového řetězce. Interpolace řetězců není aktuálně podporována.

Můžete buď spustit řetězec přímo po otevření, `'''` nebo vložit nový řádek. V obou případech výsledný řetězec neobsahuje nový řádek. V závislosti na koncích řádků v souboru bicep se nové řádky interpretují jako `\r\n` nebo `\n` .

Následující příklad ukazuje víceřádkový řetězec.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Předchozí příklad je ekvivalentní následujícímu formátu JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Další kroky

Úvod do bicep najdete v tématu [co je bicep?](bicep-overview.md).
