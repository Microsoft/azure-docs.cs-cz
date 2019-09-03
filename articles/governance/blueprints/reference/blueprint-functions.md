---
title: Funkce Azure modrotisky
description: Popisuje funkce pro použití s definicemi a přiřazeními Azure modrotisky.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dcf073c58a723b8dbd835ac331c0ce9d16187445
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232853"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkce pro použití s plány Azure

Azure modrotisky nabízí funkce, které mají dynamičtější definice podrobného plánu. Tyto funkce jsou pro použití s definicemi podrobného plánu a s artefakty podrobného plánu. Artefakt šablony Správce prostředků podporuje kromě získání dynamické hodnoty prostřednictvím parametru podrobného plánu i úplné použití funkcí Správce prostředků.

Podporovány jsou následující funkce:

- [artefakty](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [předplatné](#subscription)

## <a name="artifacts"></a>artefakty

`artifacts(artifactName)`

Vrátí objekt vlastností naplněný pomocí tohoto výstupu artefaktů podrobného plánu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| artefakt |Ano |řetězec |Název artefaktu podrobného plánu |

### <a name="return-value"></a>Návratová hodnota

Objekt vlastností výstupu. Vlastnosti **výstupy** jsou závislé na typu odkazovaného artefaktu podrobného plánu. Všechny typy mají následující formát:

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

Vlastnosti **výstupů** vráceného objektu jsou definovány v rámci šablony Správce prostředků a jsou vráceny nasazením.

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

Artefakt šablony Správce prostředků s ID _myTemplateArtifact_ obsahující následující ukázkovou vlastnost output:

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

Některé příklady načítání dat z ukázky _myTemplateArtifact_ jsou:

| Výraz | type | Value |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["First"; "Second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Řetězec | první |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Řetězec | "moje hodnota řetězce" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"MyProperty": "moje hodnota", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Řetězec | "moje hodnota" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | Pravda |

## <a name="concat"></a>spojuje

`concat(string1, string2, string3, ...)`

Kombinuje více řetězcových hodnot a vrátí zřetězený řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| string1 |Ano |řetězec |První hodnota pro zřetězení |
| Další argumenty |Ne |řetězec |Další hodnoty v sekvenčním pořadí pro zřetězení |

### <a name="return-value"></a>Návratová hodnota

Řetězec zřetězených hodnot.

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od Azure Resource Manager funkce šablony v tom, že funguje pouze s řetězci.

### <a name="example"></a>Příklad

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Vrátí hodnotu parametru podrobného plánu. Zadaný název parametru musí být definován v definici podrobného plánu nebo v artefaktech podrobného plánu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| parameterName |Ano |řetězec |Název parametru, který se má vrátit. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadaného parametru artefaktu podrobného plánu nebo podrobného plánu

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony Azure Resource Manager v tom, že funguje pouze s parametry podrobného plánu.

### <a name="example"></a>Příklad

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

Pak použijte _principalIds_ jako argument pro artefakt `parameters()` podrobného plánu:

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

Funkce Azure Blueprint se liší od Azure Resource Manager funkce šablony. `resourceGroup()` Funkci nelze použít v artefaktu na úrovni předplatného nebo v definici podrobného plánu. Dá se použít jenom v artefaktech podrobného plánu, které jsou součástí artefaktu skupiny prostředků.

Běžným použitím `resourceGroup()` funkce je vytvořit prostředky ve stejném umístění jako artefakt skupiny prostředků.

### <a name="example"></a>Příklad

Pokud chcete použít umístění skupiny prostředků, nastavte v definici podrobného plánu nebo v přiřazení jako umístění pro jiný artefakt a v definici podrobného plánu deklarujte zástupný objekt skupiny prostředků. V tomto příkladu je _NetworkingPlaceholder_ názvem zástupného symbolu skupiny prostředků.

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

Pak použijte `resourceGroup()` funkci v kontextu artefaktu podrobného plánu, který cílí na zástupný objekt skupiny prostředků. V tomto příkladu je artefakt šablony nasazený do skupiny prostředků _NetworkingPlaceholder_ a poskytuje parametr _resourceLocation_ dynamicky naplněný pomocí umístění skupiny prostředků _NetworkingPlaceholder_ do vzhledu. Umístění skupiny prostředků _NetworkingPlaceholder_ bylo v definici podrobného plánu definováno staticky nebo dynamicky definované během přiřazení. V obou případech artefakt šablony poskytuje informace jako parametr a používá ho k nasazení prostředků do správného umístění.

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

Vrátí objekt, který představuje zadaný artefakt skupiny prostředků. Na rozdíl `resourceGroup()`od, který vyžaduje kontext artefaktu, se tato funkce používá k získání vlastností zástupného znaku konkrétní skupiny prostředků, pokud není v kontextu této skupiny prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| zástupný znak |Ano |řetězec |Zástupný název artefaktu skupiny prostředků, který se má vrátit |

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Příklad

Pokud chcete použít umístění skupiny prostředků, nastavte v definici podrobného plánu nebo v přiřazení jako umístění pro jiný artefakt a v definici podrobného plánu deklarujte zástupný objekt skupiny prostředků. V tomto příkladu je _NetworkingPlaceholder_ názvem zástupného symbolu skupiny prostředků.

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

Pak použijte `resourceGroups()` funkci z kontextu jakéhokoli artefaktu podrobného plánu a získejte odkaz na objekt zástupného objektu skupiny prostředků. V tomto příkladu je artefakt šablony nasazený mimo skupinu prostředků _NetworkingPlaceholder_ a poskytuje parametr _artifactLocation_ dynamicky naplněný pomocí umístění skupiny prostředků _NetworkingPlaceholder_ do vzhledu. Umístění skupiny prostředků _NetworkingPlaceholder_ bylo v definici podrobného plánu definováno staticky nebo dynamicky definované během přiřazení. V obou případech artefakt šablony poskytuje informace jako parametr a používá ho k nasazení prostředků do správného umístění.

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

Pomocí zobrazovaného jména a `concat()` funkce odběru vytvořte zásadu vytváření názvů předanou jako parametr _resourceName_ do artefaktu šablony.

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

- Přečtěte si informace o [životním cyklu](../concepts/lifecycle.md)podrobného plánu.
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)