---
title: Funkce Azure modrotisky
description: Popisuje funkce, které jsou k dispozici pro použití s artefakty podrobného plánu v definicích a přiřazeních Azure modrotisky.
ms.date: 08/27/2020
ms.topic: reference
ms.openlocfilehash: fe984ee7664b0d50fb891d946f9f40a200ccce09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89050756"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkce pro použití s plány Azure

Azure modrotisky nabízí funkce, které mají dynamičtější definice podrobného plánu. Tyto funkce jsou pro použití s definicemi podrobného plánu a s artefakty podrobného plánu. Artefakt šablony Azure Resource Manager (šablona ARM) podporuje kromě získání dynamické hodnoty prostřednictvím parametru podrobného plánu úplné použití funkcí Správce prostředků.

Podporovány jsou následující funkce:

- [artefakty](#artifacts)
- [spojuje](#concat)
- [ukazatelů](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [formě](#subscription)

## <a name="artifacts"></a>artefakty

`artifacts(artifactName)`

Vrátí objekt vlastností naplněný pomocí tohoto výstupu artefaktů podrobného plánu.

> [!NOTE]
> `artifacts()`Funkci nelze použít v rámci šablony ARM. Funkci lze použít pouze v kódu JSON definice podrobného plánu nebo v kódu JSON artefaktu při správě [podrobného](https://github.com/Azure/azure-blueprints/blob/master/README.md)plánu pomocí Azure PowerShell nebo REST API jako součást podrobných plánů kódu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| artefakt |Yes |řetězec |Název artefaktu podrobného plánu |

### <a name="return-value"></a>Vrácená hodnota

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

#### <a name="arm-template-artifact"></a>Artefakt šablony ARM

Vlastnosti **výstupů** vráceného objektu jsou definovány v rámci šablony ARM a vráceny v nasazení.

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

Artefakt šablony ARM s ID _myTemplateArtifact_ obsahující následující ukázkovou vlastnost output:

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

| Výraz | Typ | Hodnota |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Pole | \["First"; "Second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Řetězec | první |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Řetězec | "moje hodnota řetězce" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objekt | {"MyProperty": "moje hodnota", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Řetězec | "moje hodnota" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Logická hodnota | Ano |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Kombinuje více řetězcových hodnot a vrátí zřetězený řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| řetězec1 |Yes |řetězec |První hodnota pro zřetězení |
| Další argumenty |No |řetězec |Další hodnoty v sekvenčním pořadí pro zřetězení |

### <a name="return-value"></a>Vrácená hodnota

Řetězec zřetězených hodnot.

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony ARM v tom, že funguje pouze s řetězci.

### <a name="example"></a>Příklad

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Vrátí hodnotu parametru podrobného plánu. Zadaný název parametru musí být definován v definici podrobného plánu nebo v artefaktech podrobného plánu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |řetězec |Název parametru, který se má vrátit. |

### <a name="return-value"></a>Vrácená hodnota

Hodnota zadaného parametru artefaktu podrobného plánu nebo podrobného plánu

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony ARM v tom, že funguje pouze s parametry podrobného plánu.

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Pak použijte _principalIds_ jako argument pro artefakt podrobného plánu `parameters()` :

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

### <a name="return-value"></a>Vrácená hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Poznámky

Funkce Azure Blueprint se liší od funkce šablony ARM. `resourceGroup()`Funkci nelze použít v artefaktu na úrovni předplatného nebo v definici podrobného plánu. Dá se použít jenom v artefaktech podrobného plánu, které jsou součástí artefaktu skupiny prostředků.

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

Pak použijte `resourceGroup()` funkci v kontextu artefaktu podrobného plánu, který cílí na zástupný objekt skupiny prostředků. V tomto příkladu je artefakt šablony nasazený do skupiny prostředků _NetworkingPlaceholder_ a poskytuje parametr _resourceLocation_ dynamicky vyplněný umístěním skupiny prostředků _NetworkingPlaceholder_ do šablony. Umístění skupiny prostředků _NetworkingPlaceholder_ bylo v definici podrobného plánu definováno staticky nebo dynamicky definované během přiřazení. V obou případech artefakt šablony poskytuje informace jako parametr a používá ho k nasazení prostředků do správného umístění.

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

Vrátí objekt, který představuje zadaný artefakt skupiny prostředků. Na rozdíl od `resourceGroup()` , který vyžaduje kontext artefaktu, se tato funkce používá k získání vlastností zástupného znaku konkrétní skupiny prostředků, pokud není v kontextu této skupiny prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| zástupný znak |Yes |řetězec |Zástupný název artefaktu skupiny prostředků, který se má vrátit |

### <a name="return-value"></a>Vrácená hodnota

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

Pak použijte `resourceGroups()` funkci z kontextu jakéhokoli artefaktu podrobného plánu a získejte odkaz na objekt zástupného objektu skupiny prostředků. V tomto příkladu je artefakt šablony nasazený mimo skupinu prostředků _NetworkingPlaceholder_ a poskytuje parametr _artifactLocation_ dynamicky vyplněný umístěním skupiny prostředků _NetworkingPlaceholder_ do šablony. Umístění skupiny prostředků _NetworkingPlaceholder_ bylo v definici podrobného plánu definováno staticky nebo dynamicky definované během přiřazení. V obou případech artefakt šablony poskytuje informace jako parametr a používá ho k nasazení prostředků do správného umístění.

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

### <a name="return-value"></a>Vrácená hodnota

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

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)