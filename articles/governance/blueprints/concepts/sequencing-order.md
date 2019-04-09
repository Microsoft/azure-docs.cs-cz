---
title: Vysvětlení pořadí pořadí nasazení
description: Další informace o životním cyklu, který prochází definice podrobného plánu a podrobnosti o každé fázi.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5552e44fcca056bd4fd5b4fd19559adfbd005444
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266184"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Vysvětlení pořadí nasazení v Azure podrobné plány

Azure používá plány **pořadí řazení** k určení pořadí vytváření prostředků při zpracování přiřazení definice podrobného plánu. Tento článek popisuje následující pojmy:

- Výchozí pořadí řazení, který se používá
- Přizpůsobení pořadí
- Jak se zpracovávají přizpůsobené pořadí

Jsou v příkladech JSON, které budete muset nahraďte vlastními hodnotami:

- `{YourMG}` -Nahraďte název skupiny pro správu

## <a name="default-sequencing-order"></a>Výchozí pořadí řazení

Pokud žádná směrnice pro pořadí je možné nasazovat artefakty obsahuje definici podrobného plánu nebo direktivě má hodnotu null, použije se následujícím pořadí:

- Úroveň předplatného **přiřazení role** artefakty seřazené podle názvu artefaktu
- Úroveň předplatného **přiřazení zásady** artefakty seřazené podle názvu artefaktu
- Úroveň předplatného **šablony Azure Resource Manageru** artefakty seřazené podle názvu artefaktu
- **Skupina prostředků** artefakty (včetně podřízených artefakty) seřazené podle názvu zástupného symbolu

V každé **skupiny prostředků** artefaktu, následovně pořadí slouží pro artefakty mají být vytvořeny v rámci této skupiny prostředků:

- Podřízené skupiny prostředků **přiřazení role** artefakty seřazené podle názvu artefaktu
- Podřízené skupiny prostředků **přiřazení zásady** artefakty seřazené podle názvu artefaktu
- Podřízené skupiny prostředků **šablony Azure Resource Manageru** artefakty seřazené podle názvu artefaktu

## <a name="customizing-the-sequencing-order"></a>Přizpůsobení pořadí řazení

Při vytváření definice podrobného plánu. velké, může být nezbytné pro prostředky vytvořené v určitém pořadí. Nejběžnější vzor používání tohoto scénáře je při definici podrobného plánu obsahuje několik šablon Azure Resource Manageru. Plány zpracovává tohoto modelu tím, že pořadí řazení, aby byla definována.

Řazení se provádí tak, že definujete `dependsOn` vlastností v kódu JSON. Definice podrobného plánu pro skupiny prostředků a artefaktů objekty podporují tuto vlastnost. `dependsOn` je pole řetězců názvů artefaktů, které konkrétní artefaktů je potřeba vytvořit předtím, než je vytvořen.

### <a name="example---ordered-resource-group"></a>Příklad: seřazené skupiny prostředků

Tento příklad definice podrobného plánu má skupinu prostředků, která má definované vlastní sekvencování pořadí deklarací hodnotu `dependsOn`, spolu s skupiny standardních prostředků. V tomto případě s názvem artefakt **assignPolicyTags** se zpracuje před **seřazené rg** skupinu prostředků.
**Standard-rg** budou zpracovány za výchozí pořadí řazení.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Příklad: artefakt se vlastní pořadí

V tomto příkladu je artefakt zásad, který závisí na šablony Azure Resource Manageru. Ve výchozím nastavení řazení, by se vytvořily artefakt zásad před šablony Azure Resource Manageru. Toto uspořádání umožňuje artefakt zásad čekání na šablony Azure Resource Manageru, který se má vytvořit.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Příklad: předplatné úrovně šablony artefakt v závislosti na skupinu prostředků

Tento příklad je určený pro šablonu Resource Manageru, který je nasazený na úrovni předplatného závisí na skupině prostředků. Ve výchozích řazení, by úrovně artefaktů předplatné vytvořit před všechny skupiny prostředků a podřízené artefakty v těchto skupinách prostředků. Skupina prostředků je definované v definici podrobného plánu takto:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Artefakt předplatné úrovně šablony v závislosti na **wait pro mě** skupina prostředků je definována takto:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>Vlastní pořadí zpracování

Během procesu vytváření topologické řazení slouží k vytvoření grafu závislostí artefaktů podrobné plány. Kontrola je zajištěno, že se podporuje každý stupeň závislosti mezi skupinami prostředků a artefakty.

Který by změnit výchozí pořadí je deklarované jako závislost artefakt, se neprovedly žádné změny. Příkladem je skupinu prostředků, která závisí na zásadou na úrovni předplatného. Dalším příkladem je prostředku skupiny "standard-rg" podřízené přiřazení zásady, které závisí na přiřazení role podřízené skupině prostředků "standard-rg". V obou případech `dependsOn` by jste změnili výchozí pořadí řazení a žádné změny byste provedli.

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Principy použití [statických a dynamických parametrů](parameters.md)
- Použití [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)