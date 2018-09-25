---
title: Vysvětlení pořadí nasazení v Azure podrobné plány
description: Další informace o životním cyklu, který prochází podrobný plán a podrobnosti o každé fázi.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955448"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Vysvětlení pořadí nasazení v Azure podrobné plány

Azure používá plány **pořadí řazení** k určení pořadí vytváření prostředků při zpracování přiřazení podrobný plán. Tento článek vysvětluje, výchozí pořadí řazení, který se používá, jak přizpůsobit pořadí a jak se zpracovávají přizpůsobené pořadí.

Jsou v příkladech JSON, které budete muset nahraďte vlastními hodnotami:

- `{YourMG}` -Nahraďte název skupiny pro správu

## <a name="default-sequencing-order"></a>Výchozí pořadí řazení

Pokud žádná směrnice pro pořadí je možné nasazovat artefakty obsahuje podrobný plán nebo direktivě má hodnotu null, použije se následujícím pořadí:

- Úroveň předplatného **přiřazení role** artefakty seřazené podle názvu artefaktu
- Úroveň předplatného **přiřazení zásady** artefakty seřazené podle názvu artefaktu
- Úroveň předplatného **šablony Azure Resource Manageru** artefakty seřazené podle názvu artefaktu
- **Skupina prostředků** artefakty (včetně podřízených artefakty) seřazené podle názvu zástupného symbolu

V každé **skupiny prostředků** artefakt, který je zpracován následovně pořadí mají být vytvořeny v rámci této skupiny prostředků slouží pro artefakty:

- Podřízené skupiny prostředků **přiřazení role** artefakty seřazené podle názvu artefaktu
- Podřízené skupiny prostředků **přiřazení zásady** artefakty seřazené podle názvu artefaktu
- Podřízené skupiny prostředků **šablony Azure Resource Manageru** artefakty seřazené podle názvu artefaktu

## <a name="customizing-the-sequencing-order"></a>Přizpůsobení pořadí řazení

Při vytváření velké plány, může být nezbytné pro prostředek má být vytvořen v určitém pořadí ve vztahu k jiným prostředkem. Nejběžnější vzor používání tohoto objektu je při podrobný plán obsahuje několik šablon Azure Resource Manageru. Plány zpracovává to tím, že pořadí řazení, aby byla definována.

To lze provést definováním `dependsOn` vlastností v kódu JSON. Podrobný plán (pro skupiny prostředků) a objekty artefaktů podporují tuto vlastnost. `dependsOn` je pole řetězců názvů artefaktů, které konkrétní artefaktů je potřeba vytvořit předtím, než je vytvořen.

### <a name="example---blueprint-with-ordered-resource-group"></a>Příklad: podrobného plánu se skupinou prostředků seřazený

Jedná se příklad podrobného plánu se skupinou prostředků, který byl definován vlastní sekvencování pořadí deklarací hodnotu pro `dependsOn`, spolu s skupiny standardních prostředků. V tomto případě s názvem artefakt **assignPolicyTags** se zpracuje před **seřazené rg** skupinu prostředků.
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

Toto je příklad zásad artefakt, který závisí na šablony Azure Resource Manageru. Ve výchozím nastavení řazení, by se vytvořily artefakt zásad před šablony Azure Resource Manageru. To umožňuje artefakt zásad čekání na šablony Azure Resource Manageru, který se má vytvořit.

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

Během procesu vytváření topologické řazení slouží k vytvoření grafu závislostí podrobný plán a jeho artefaktů. Tím se zajistí, že může podporovat více úrovní závislosti mezi skupinami prostředků a artefakty.

Pokud se na podrobný plán nebo artefakt, který by změnit výchozí pořadí je deklarován závislost, se neprovedly žádné změny pořadí řazení. Příklady jsou skupinu prostředků, která závisí na přiřazení zásady podřízené úrovni zásad nebo prostředku skupiny "standard-rg" předplatného, který závisí na přiřazení role podřízené skupině prostředků "standard-rg". V obou případech `dependsOn` by jste změnili výchozí pořadí řazení a žádné změny byste provedli.

## <a name="next-steps"></a>Další postup

- Další informace o [podrobný plán životního cyklu](lifecycle.md)
- Vysvětlení použití [statické a dynamické parametry](parameters.md)
- Zjistěte, jak se využívání [podrobný plán uzamčení prostředků](resource-locking.md)
- Zjistěte, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md)
- Řešení problémů při přiřazení podrobného plánu se [obecný postup řešení potíží](../troubleshoot/general.md)