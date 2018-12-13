---
title: Vysvětlení pořadí pořadí nasazení
description: Další informace o životním cyklu, který prochází podrobný plán a podrobnosti o každé fázi.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bd12aabf0ca8f82261e6b3c677d7306ee46c4171
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308613"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Vysvětlení pořadí nasazení v Azure podrobné plány

Azure používá plány **pořadí řazení** k určení pořadí vytváření prostředků při zpracování přiřazení podrobný plán. Tento článek popisuje následující pojmy:

- Výchozí pořadí řazení, který se používá
- Přizpůsobení pořadí
- Jak se zpracovávají přizpůsobené pořadí

Jsou v příkladech JSON, které budete muset nahraďte vlastními hodnotami:

- Proměnnou `{YourMG}` nahraďte názvem skupiny pro správu.

## <a name="default-sequencing-order"></a>Výchozí pořadí řazení

Pokud žádná směrnice pro pořadí je možné nasazovat artefakty obsahuje podrobný plán nebo direktivě má hodnotu null, použije se následujícím pořadí:

- Úroveň předplatného **přiřazení role** artefakty seřazené podle názvu artefaktu
- Úroveň předplatného **přiřazení zásady** artefakty seřazené podle názvu artefaktu
- Úroveň předplatného **šablony Azure Resource Manageru** artefakty seřazené podle názvu artefaktu
- **Skupina prostředků** artefakty (včetně podřízených artefakty) seřazené podle názvu zástupného symbolu

V každé **skupiny prostředků** artefaktu, následovně pořadí slouží pro artefakty mají být vytvořeny v rámci této skupiny prostředků:

- Podřízené skupiny prostředků **přiřazení role** artefakty seřazené podle názvu artefaktu
- Podřízené skupiny prostředků **přiřazení zásady** artefakty seřazené podle názvu artefaktu
- Podřízené skupiny prostředků **šablony Azure Resource Manageru** artefakty seřazené podle názvu artefaktu

## <a name="customizing-the-sequencing-order"></a>Přizpůsobení pořadí řazení

Při vytváření velké plány, může být nezbytné pro prostředky vytvořené v určitém pořadí. Nejběžnější vzor používání tohoto scénáře je při podrobný plán obsahuje několik šablon Azure Resource Manageru. Plány zpracovává tohoto modelu tím, že pořadí řazení, aby byla definována.

Řazení se provádí tak, že definujete `dependsOn` vlastností v kódu JSON. Podrobný plán (pro skupiny prostředků) a objekty artefaktů podporují tuto vlastnost. `dependsOn` je pole řetězců názvů artefaktů, které konkrétní artefaktů je potřeba vytvořit předtím, než je vytvořen.

> [!NOTE]
> **Skupina prostředků** podporují artefakty `dependsOn` vlastnost, ale nemůže být cílem `dependsOn` žádným typem artefaktů.

### <a name="example---blueprint-with-ordered-resource-group"></a>Příklad: podrobného plánu se skupinou prostředků seřazený

Tento příklad podrobný plán má skupinu prostředků, která má definované vlastní sekvencování pořadí deklarací hodnotu `dependsOn`, spolu s skupiny standardních prostředků. V tomto případě s názvem artefakt **assignPolicyTags** se zpracuje před **seřazené rg** skupinu prostředků. **Standard-rg** budou zpracovány za výchozí pořadí řazení.

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

## <a name="processing-the-customized-sequence"></a>Vlastní pořadí zpracování

Během procesu vytváření topologické řazení slouží k vytvoření grafu závislostí artefaktů podrobné plány. Kontrola je zajištěno, že se podporuje každý stupeň závislosti mezi skupinami prostředků a artefakty.

Který by změnit výchozí pořadí je deklarované jako závislost artefakt, se neprovedly žádné změny. Příkladem je skupinu prostředků, která závisí na zásadou na úrovni předplatného. Dalším příkladem je prostředku skupiny "standard-rg" podřízené přiřazení zásady, které závisí na přiřazení role podřízené skupině prostředků "standard-rg". V obou případech `dependsOn` by jste změnili výchozí pořadí řazení a žádné změny byste provedli.

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Principy použití [statických a dynamických parametrů](parameters.md)
- Zjistěte, jak používat [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)