---
title: Principy pořadí pořadí nasazení
description: Přečtěte si o výchozí pořadí, které jsou nasazeny artefakty podrobného plánu v průběhu přiřazení podrobného plánu a jak přizpůsobit pořadí nasazení.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 51026862c989f15acf6d3e21702cfcfc8b2b27b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74128814"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Principy pořadí nasazení v Azure Blueprints

Azure Blueprints používá **pořadí sekvencování** k určení pořadí vytváření prostředků při zpracování přiřazení definice podrobného plánu. Tento článek vysvětluje následující pojmy:

- Výchozí pořadí sekvencování, které se používá
- Jak přizpůsobit objednávku
- Jak je přizpůsobená objednávka zpracována

V příkladech JSON jsou proměnné, které je třeba nahradit vlastními hodnotami:

- Proměnnou `{YourMG}` nahraďte názvem skupiny pro správu.

## <a name="default-sequencing-order"></a>Výchozí pořadí sekvencování

Pokud definice podrobného plánu neobsahuje žádnou direktivu pro pořadí pro nasazení artefaktů nebo směrnice je null, použije se následující pořadí:

- Artefakty **přiřazení role** na úrovni předplatného seřazené podle názvu artefaktu
- Artefakty **přiřazení zásad na** úrovni předplatného seřazené podle názvu artefaktu
- Artefakty **šablony Azure Resource Manager** na úrovni předplatného seřazené podle názvu artefaktu
- **Artefakty skupiny prostředků** (včetně podřízených artefaktů) seřazené podle zástupného názvu

V rámci každého artefaktu **skupiny prostředků** se pro artefakty, které mají být vytvořeny v rámci této skupiny prostředků, používá následující pořadí pořadí:

- Artefakty **přiřazení** podřízené role skupiny prostředků seřazené podle názvu artefaktu
- Artefakty **přiřazení podřízených zásad** skupiny prostředků seřazené podle názvu artefaktu
- Podřízené artefakty šablony Azure **Resource Manager** u skupiny prostředků seřazené podle názvu artefaktu

> [!NOTE]
> Použití [artefakty()](../reference/blueprint-functions.md#artifacts) vytvoří implicitní závislost na artefakt ukázáno.

## <a name="customizing-the-sequencing-order"></a>Přizpůsobení pořadí řazení

Při vytváření velkých definic podrobného plánu může být nezbytné pro prostředky, které mají být vytvořeny v určitém pořadí. Nejběžnější vzor použití tohoto scénáře je, když definice podrobného plánu obsahuje několik šablon Azure Resource Manager. Podrobné plány zpracovává tento vzor tím, že pořadí pořadí, které mají být definovány.

Řazení se provádí definováním `dependsOn` vlastnosti v JSON. Definice podrobného plánu pro skupiny prostředků a objekty artefaktů podporují tuto vlastnost. `dependsOn`je řetězec pole názvů artefaktů, které je třeba vytvořit konkrétní artefakt před jeho vytvořením.

> [!NOTE]
> Při vytváření objektů podrobného plánu získá každý prostředek artefaktu svůj název z názvu [souboru, pokud používáte PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)nebo koncový bod url, pokud [používáte rozhraní REST API](/rest/api/blueprints/artifacts/createorupdate).
> odkazy _na skupiny_ v artefaktech musí odpovídat odkazům definovaným v definici podrobného plánu.

### <a name="example---ordered-resource-group"></a>Příklad - objednaná skupina prostředků

Tato příklad definice podrobného plánu má skupinu prostředků, která definovala `dependsOn`vlastní pořadí sekvencování deklarováním hodnoty pro , spolu se standardní skupinou prostředků. V tomto případě artefakt s názvem **assignPolicyTags** budou zpracovány před skupinou prostředků **objednané rg.**
**standard-rg** budou zpracovány podle výchozího pořadí řazení.

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
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Příklad - artefakt s vlastní objednávkou

Tento příklad je artefakt zásad, který závisí na šabloně Azure Resource Manager. Ve výchozím nastavení řazení by artefakt zásady být vytvořen před šablonou Azure Resource Manager. Toto pořadí umožňuje artefakt zásady čekat na šablonu Azure Resource Manager, které mají být vytvořeny.

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
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Příklad – artefakt šablony na úrovni předplatného v závislosti na skupině prostředků

Tento příklad je pro šablonu Správce prostředků nasazenou na úrovni předplatného, která závisí na skupině prostředků. Ve výchozím pořadí by artefakty na úrovni předplatného byly vytvořeny před všemi skupinami prostředků a podřízenými artefakty v těchto skupinách prostředků. Skupina prostředků je definována v definici podrobného plánu takto:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Artefakt šablony úrovně předplatného v závislosti na skupině prostředků **čekání na mě** je definován takto:

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
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Zpracování vlastní sekvence

Během procesu vytváření topologické řazení se používá k vytvoření grafu závislostí artefaktů podrobné plány. Kontrola zajišťuje, že je podporována každá úroveň závislosti mezi skupinami prostředků a artefakty.

Pokud je deklarována závislost artefaktu, která by nezměnila výchozí pořadí, pak není provedena žádná změna. Příkladem je skupina prostředků, která závisí na zásadách úrovně předplatného. Dalším příkladem je přiřazení podřízených zásad skupiny zdrojů "standard-rg", které závisí na přiřazení podřízené role skupiny zdrojů "standard-rg". V obou případech `dependsOn` by se nezměnila výchozí pořadí řazení a žádné změny by byly provedeny.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [životním cyklu podrobného plánu](lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](parameters.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).