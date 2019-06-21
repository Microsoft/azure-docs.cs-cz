---
title: Služba Azure functions podrobné plány.
description: Popisuje funkce pro použití s plány Azure definic a přiřazení.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209406"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkce pro použití se službou Azure podrobné plány

Plány Azure poskytuje funkce provádění dynamičtějších definice podrobného plánu. Tyto funkce jsou určeny pro použití se definice podrobného plánu a podrobný plán artefakty. Šablony Resource Manageru artefakt podporuje plně využívat funkce Resource Manageru kromě dostat se dynamické hodnoty přes parametr podrobného plánu.

Jsou podporovány následující funkce:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [předplatné](#subscription)

## <a name="artifacts"></a>Artefakty

`artifacts(artifactName)`

Vrátí že objekt vlastnosti vyplní této artefakty podrobného plánu výstupy.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| artifactName |Ano |string |Název artefaktu podrobného plánu. |

### <a name="return-value"></a>Návratová hodnota

Objekt vlastnosti output. **Výstupy** vlastnosti jsou závislé na typu odkazovaného artefakt podrobného plánu. Všechny typy použijte formát:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefaktu přiřazení zásad

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefakt šablony Resource Manageru

**Výstupy** vlastnosti vráceného objektu jsou definovány v šabloně Resource Manageru a vrácené nasazení.

#### <a name="role-assignment-artifact"></a>Artefaktu přiřazení role

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Příklad:

Artefaktem šablony Resource Manageru s ID _myTemplateArtifact_ vlastnost obsahující následující ukázka výstupu:

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

| Výraz | Type | Hodnota |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "Moje řetězcovou hodnotu" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"myproperty": "Moje value", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Kombinuje více řetězcových hodnot a vrací spojený řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| string1 |Ano |string |První hodnota pro zřetězení. |
| Další argumenty |Ne |string |Další hodnoty v postupném pořadí pro zřetězení |

### <a name="return-value"></a>Návratová hodnota

Řetězec, ze zřetězených hodnot.

### <a name="remarks"></a>Poznámky

Funkce Azure podrobného plánu se liší od funkce šablon Azure Resource Manageru v tom, že funguje pouze s řetězci.

### <a name="example"></a>Příklad:

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Vrátí hodnotu parametru podrobného plánu. Zadaný název parametru musí být definován v definici podrobného plánu nebo artefakty podrobného plánu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| parameterName |Ano |string |Název parametru se vraťte. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadaná podrobného plánu nebo parametr artefakt podrobného plánu.

### <a name="remarks"></a>Poznámky

Funkce Azure podrobného plánu se liší od funkce šablon Azure Resource Manageru, fungují jenom s parametry podrobného plánu.

### <a name="example"></a>Příklad:

Definujte parametr _principalIds_ v definici podrobného plánu:

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

Pak pomocí _principalIds_ jako argument pro `parameters()` v artefakt podrobného plánu:

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

Vrátí objekt představující aktuální skupinu prostředků.

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Poznámky

Funkce Azure podrobného plánu se liší od funkce šablon Azure Resource Manageru. `resourceGroup()` Funkci nelze použít v definici podrobného plánu nebo artefaktem úrovně předplatného. To jde použít jenom v podrobného plánu artefakty, které jsou součástí artefaktů skupiny prostředků.

Běžně `resourceGroup()` funkce je k vytváření prostředků ve stejném umístění jako artefakt skupiny prostředků.

### <a name="example"></a>Příklad:

Pokud chcete použít umístění skupiny prostředků, nastavit buď definice podrobného plánu nebo během přiřazení, jako umístění pro jiným artefaktem, deklarujte zástupný objekt skupiny prostředků v definici podrobného plánu. V tomto příkladu _NetworkingPlaceholder_ je název zástupný prvek skupiny prostředků.

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

Potom použijte `resourceGroup()` funkci v kontextu artefakt podrobného plánu, který je cílen na verzi zástupný objekt skupiny prostředků. V tomto příkladu šablony artefakt je nasazený do _NetworkingPlaceholder_ skupinu prostředků a poskytuje parametr _resourceLocation_ dynamicky vyplněna  _NetworkingPlaceholder_ umístění skupiny prostředků v šabloně. Umístění _NetworkingPlaceholder_ skupiny prostředků může mít staticky definovaná v definici podrobného plánu nebo dynamicky definované během přiřazení. V obou případech se artefakt šablony je k dispozici tyto informace jako parametr a použije ho k nasazení prostředků do správného umístění.

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

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Vrátí objekt, který představuje skupiny artefaktů zadaný prostředek. Na rozdíl od `resourceGroup()`, který vyžaduje kontext artefaktu, tato funkce slouží k získání vlastností zástupný prvek skupiny konkrétní prostředek není v kontextu této skupiny prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| placeholderName |Ano |string |Zástupný název artefaktu skupiny prostředků se vraťte. |

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Příklad:

Pokud chcete použít umístění skupiny prostředků, nastavit buď definice podrobného plánu nebo během přiřazení, jako umístění pro jiným artefaktem, deklarujte zástupný objekt skupiny prostředků v definici podrobného plánu. V tomto příkladu _NetworkingPlaceholder_ je název zástupný prvek skupiny prostředků.

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

Potom použijte `resourceGroups()` funkce z kontextu jakékoli artefakt podrobného plánu získáte odkaz na zástupný objekt skupiny prostředků. V tomto příkladu šablony artefaktů nasazeny mimo _NetworkingPlaceholder_ skupinu prostředků a poskytuje parametr _artifactLocation_ dynamicky vyplněna  _NetworkingPlaceholder_ umístění skupiny prostředků v šabloně. Umístění _NetworkingPlaceholder_ skupiny prostředků může mít staticky definovaná v definici podrobného plánu nebo dynamicky definované během přiřazení. V obou případech se artefakt šablony je k dispozici tyto informace jako parametr a použije ho k nasazení prostředků do správného umístění.

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

### <a name="example"></a>Příklad:

Použijte název zobrazení odběru a `concat()` funkci, která vytvoří zásady vytváření názvů předán jako parametr _resourceName_ artefaktu, šablony.

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

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)
