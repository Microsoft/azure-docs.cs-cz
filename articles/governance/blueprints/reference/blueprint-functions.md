---
title: Funkce Azure Blueprints
description: Popisuje funkce, které jsou k dispozici pro použití s artefakty podrobného plánu v definicích a přiřazeních plánů Azure.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280674"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkce pro použití s Azure Blueprints

Azure Blueprints poskytuje funkce, díky které definice podrobného plánu dynamičtější. Tyto funkce jsou určeny pro použití s definicemi podrobného plánu a artefakty podrobného plánu. Artefakt šablony Správce prostředků podporuje plné využití funkcí Správce prostředků a navíc získání dynamické hodnoty prostřednictvím parametru podrobného plánu.

Podporovány jsou následující funkce:

- [Artefakty](#artifacts)
- [Concat](#concat)
- [Parametry](#parameters)
- [skupina prostředků](#resourcegroup)
- [skupiny prostředků](#resourcegroups)
- [Předplatné](#subscription)

## <a name="artifacts"></a>Artefakty

`artifacts(artifactName)`

Vrátí objekt vlastností naplněných výstupy artefaktů podrobného plánu.

> [!NOTE]
> Funkci `artifacts()` nelze použít z evnitř šablony Správce prostředků. Funkci lze použít jenom v definici podrobného plánu JSON nebo v artefaktu JSON při správě podrobného plánu pomocí rozhraní Azure PowerShell nebo REST API jako součást [plánů jako kód](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| název artefaktu |Ano |řetězec |Název artefaktu plánu. |

### <a name="return-value"></a>Návratová hodnota

Objekt výstupních vlastností. Vlastnosti **výstupů** jsou závislé na typu artefaktu podrobného plánu, na který se odkazuje. Všechny typy postupujte podle formátu:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefakt přiřazení zásad

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefakt šablony Správce prostředků

Vlastnosti výstupů vráceného objektu jsou **definovány** v šabloně Správce prostředků a vráceny nasazením.

#### <a name="role-assignment-artifact"></a>Artefakt přiřazení role

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Příklad

Artefakt šablony Správce prostředků s ID _myTemplateArtifact_ obsahující následující ukázkovou výstupní vlastnost:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Některé příklady načítání dat z _myTemplateArtifact_ ukázky jsou:

| Expression | Typ | Hodnota |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Pole | \["první", "druhý"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Řetězec | "první" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Řetězec | "moje hodnota řetězce" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objekt | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Řetězec | "moje hodnota" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Logická hodnota | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Zkombinuje více řetězcových hodnot a vrátí zřetězený řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| řetězec1 |Ano |řetězec |První hodnota pro zřetězení. |
| další argumenty |Ne |řetězec |Další hodnoty v sekvenčním pořadí pro zřetězení |

### <a name="return-value"></a>Návratová hodnota

Řetězec zřetězených hodnot.

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony Azure Resource Manager v tom, že funguje pouze s řetězci.

### <a name="example"></a>Příklad

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Vrátí hodnotu parametru podrobného plánu. Zadaný název parametru musí být definován v definici podrobného plánu nebo v artefaktech podrobného plánu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Parametername |Ano |řetězec |Název parametru, který chcete vrátit. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadaného podrobného plánu nebo parametru artefaktu podrobného plánu.

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony Azure Resource Manager v tom, že funguje jenom s parametry podrobného plánu.

### <a name="example"></a>Příklad

Definujte _objekty parametrů v_ definici podrobného plánu:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Pak použijte _principalIds_ jako `parameters()` argument pro v artefakt u podrobného plánu:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Vrátí objekt, který představuje aktuální skupinu prostředků.

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony Azure Resource Manager. Funkci `resourceGroup()` nelze použít v artefaktu na úrovni předplatného nebo v definici podrobného plánu. Lze použít pouze v artefakty podrobného plánu, které jsou součástí artefaktskupiny prostředků.

Běžné použití `resourceGroup()` funkce je k vytvoření prostředků ve stejném umístění jako artefakt skupiny prostředků.

### <a name="example"></a>Příklad

Chcete-li použít umístění skupiny prostředků, nastavte v definici podrobného plánu nebo během přiřazení jako umístění pro jiný artefakt, deklarujte zástupný objekt skupiny prostředků v definici podrobného plánu. V tomto příkladu je zástupný symbol _skupiny_ prostředků Název skupiny prostředků.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Pak použijte `resourceGroup()` funkci v kontextu artefaktu podrobného plánu, který cílí na zástupný objekt skupiny prostředků. V tomto příkladu je artefakt šablony nasazen do skupiny prostředků _NetworkingPlaceholder_ a poskytuje parametr _resourceLocation_ dynamicky vyplněný umístěním skupiny prostředků _NetworkingPlaceholder_ do šablony. Umístění skupiny prostředků _NetworkingPlaceholder_ mohlo být staticky definováno v definici podrobného plánu nebo dynamicky definováno během přiřazení. V obou případech je k dispozici artefakt šablony, že informace jako parametr a používá k nasazení prostředků do správného umístění.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>skupiny prostředků

`resourceGroups(placeholderName)`

Vrátí objekt, který představuje artefakt zadané skupiny prostředků. Na `resourceGroup()`rozdíl od , který vyžaduje kontext artefaktu, tato funkce se používá k získání vlastností zástupný symbol skupiny prostředků, pokud není v kontextu této skupiny prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| zástupný název |Ano |řetězec |Zástupný název artefaktu skupiny prostředků, který má být vrácen. |

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Příklad

Chcete-li použít umístění skupiny prostředků, nastavte v definici podrobného plánu nebo během přiřazení jako umístění pro jiný artefakt, deklarujte zástupný objekt skupiny prostředků v definici podrobného plánu. V tomto příkladu je zástupný symbol _skupiny_ prostředků Název skupiny prostředků.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Pak použijte `resourceGroups()` funkci z kontextu libovolného artefaktu podrobného plánu k získání odkazu na zástupný objekt skupiny prostředků. V tomto příkladu je artefakt šablony nasazen mimo skupinu prostředků _NetworkingPlaceholder_ a poskytuje parametr _artefaktUmístění_ dynamicky naplněný umístěním skupiny prostředků _NetworkingPlaceholder_ do šablony. Umístění skupiny prostředků _NetworkingPlaceholder_ mohlo být staticky definováno v definici podrobného plánu nebo dynamicky definováno během přiřazení. V obou případech je k dispozici artefakt šablony, že informace jako parametr a používá k nasazení prostředků do správného umístění.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>předplatné

`subscription()`

Vrátí podrobnosti o předplatném pro aktuální přiřazení podrobného plánu.

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Příklad

Pomocí zobrazovaný název předplatného `concat()` a funkce vytvořte konvenci pojmenování předanou jako parametr _resourceName_ artefaktu šablony.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [životním cyklu podrobného plánu](../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).