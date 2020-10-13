---
title: Podrobnosti struktury definice iniciativy
description: Popisuje způsob, jakým se definice iniciativ zásad používají k definování zásad pro nasazení do prostředků Azure ve vaší organizaci.
ms.date: 10/07/2020
ms.topic: conceptual
ms.openlocfilehash: 8f9c6146e1dde5b5a7f6595c61638319de60a82d
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876171"
---
# <a name="azure-policy-initiative-definition-structure"></a>Struktura definice Azure Policy iniciativy

Iniciativy umožňují seskupit několik souvisejících definic zásad, které zjednodušují přiřazování a správu, protože pracujete se skupinou jako s jednou položkou. Můžete například seskupit související definice zásad označování do jedné iniciativy. Místo toho, abyste každou zásadu přiřadíte jednotlivě, můžete použít iniciativu.

K vytvoření definice iniciativy zásad použijte JSON. Definice iniciativy zásad obsahuje prvky pro:

- zobrazované jméno
- Popis
- zprostředkovatele identity
- parameters
- definice zásad
- skupiny zásad (Tato vlastnost je součástí [funkce dodržování předpisů (Preview)](./regulatory-compliance.md))

Následující příklad ukazuje, jak vytvořit iniciativu pro zpracování dvou značek: `costCenter` a `productName` . Pomocí dvou předdefinovaných zásad použije výchozí hodnotu značky.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy předdefinované a vzory jsou v [Azure Policy Samples](../samples/index.md).

## <a name="metadata"></a>Metadata

Volitelná `metadata` vlastnost ukládá informace o definici iniciativy zásad.
Zákazníci mohou definovat libovolné vlastnosti a hodnoty, které jsou užitečné pro jejich organizaci v `metadata` . Existují však některé _běžné_ vlastnosti, které používá Azure Policy a v integrovaných modulech.

### <a name="common-metadata-properties"></a>Vlastnosti běžných metadat

- `version` (řetězec): sleduje podrobnosti o verzi obsahu definice iniciativy zásad.
- `category` (String): Určuje, pod kterou kategorii v Azure Portal je tato definice zásad zobrazená.

  > [!NOTE]
  > V případě iniciativ [dodržování předpisů](./regulatory-compliance.md) `category` musí být **předpisy dodržování**předpisů.

- `preview` (Boolean): příznak True nebo false pro, pokud je definice iniciativy pro zásady ve _verzi Preview_.
- `deprecated` (Boolean): příznak True nebo false pro, pokud byla definice iniciativy zásad označena jako _zastaralá_.

> [!NOTE]
> Služba Azure Policy používá `version` vlastnosti, `preview` a `deprecated` k vyjádření úrovně změny předdefinované definice nebo iniciativy a stavu zásad. Formát `version` je: `{Major}.{Minor}.{Patch}` . Konkrétní stavy, například _zastaralé_ nebo ve _verzi Preview_, jsou připojeny k `version` vlastnosti nebo v jiné vlastnosti jako **logická hodnota**. Další informace o způsobu, jakým jsou předdefinované verze Azure Policy, najdete v tématu [Vestavěná správa verzí](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametry

Parametry vám pomůžou zjednodušit správu zásad tím, že se sníží počet definic zásad. Představte si parametry jako pole ve formuláři – `name` , `address` , `city` , `state` . Tyto parametry vždy zůstávají stejné, ale jejich hodnoty se změní na základě jednotlivých vyplňování formuláře.
Parametry fungují stejným způsobem při vytváření iniciativ zásad. Zahrnutím parametrů do definice iniciativy zásad můžete tento parametr znovu použít v zahrnutých zásadách.

> [!NOTE]
> Jakmile je iniciativa přiřazena, parametry úrovně Initative nelze změnit. Z tohoto důvodu doporučujeme, abyste při definování parametru nastavili hodnotu **DefaultValue** .

### <a name="parameter-properties"></a>Vlastnosti parametru

Parametr má následující vlastnosti, které se používají v definici iniciativy zásad:

- `name`: Název vašeho parametru. Používá se `parameters` funkcí nasazení v rámci pravidla zásad. Další informace najdete v tématu [použití hodnoty parametru](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Určuje, zda je parametr typu **řetězec**, **pole**, **objekt**, **Boolean**, **Integer**, **float**nebo **DateTime**.
- `metadata`: Definuje podvlastnost primárně používané Azure Portal k zobrazení uživatelsky přívětivých informací:
  - `description`: Vysvětlení použití parametru pro. Dá se použít k zadání příkladů přijatelných hodnot.
  - `displayName`: Popisný název zobrazený na portálu pro parametr.
  - `strongType`: (Volitelné) používá se při přiřazování definice zásady prostřednictvím portálu. Poskytuje seznam podporující kontext. Další informace najdete v tématu [strongType](#strongtype).
- `defaultValue`: (Volitelné) nastaví hodnotu parametru v přiřazení, pokud není zadána žádná hodnota.
- `allowedValues`: (Volitelné) poskytuje pole hodnot, které parametr akceptuje během přiřazování.

Jako příklad můžete definovat definici iniciativy zásad pro omezení umístění prostředků v různých zahrnutých definicích zásad. Parametr této definice iniciativy zásad může být **allowedLocations**. Parametr je pak k dispozici pro každou zahrnutou definici zásad a definuje se během přiřazování iniciativy zásad.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Předání hodnoty parametru do definice zásady

Deklarujete, které parametry iniciativy předáváte, do kterých patří definice zásad v poli [policyDefinitions](#policy-definitions) definice iniciativy. I když název parametru může být stejný, použití různých názvů v iniciativách, než je v definicích zásad, zjednodušuje čitelnost kódu.

Například parametr iniciativy **init_allowedLocations** , který jste dříve definovali, lze předat několika zahrnutým definicím zásad a jejich parametrům, **sql_locations** a **vm_locations**, jako je:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Tento příklad odkazuje na parametr **init_allowedLocations** , který byl prokázán ve [vlastnostech parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

V rámci `metadata` vlastnosti můžete použít **strongType** k poskytnutí seznamu možností s vícenásobným výběrem v rámci Azure Portal. **strongType** může být podporovaný _typ prostředku_ nebo povolená hodnota. Chcete-li zjistit, zda je _typ prostředku_ platný pro **strongType**, použijte [příkaz Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Některé typy prostředků, které nejsou vraceny **Get-AzResourceProvider** , jsou podporovány. Tyto typy prostředků:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Povolené hodnoty pro **strongType** typu non-Resource jsou:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Definice zásad

`policyDefinitions`Část definice iniciativy je _pole_ , ze kterého jsou zahrnuty existující definice zásad v iniciativě. Jak je uvedeno v části [předání hodnoty parametru do definice zásady](#passing-a-parameter-value-to-a-policy-definition), tato vlastnost je tam, kde jsou [parametry iniciativy](#parameters) předány do definice zásady.

### <a name="policy-definition-properties"></a>Vlastnosti definice zásad

Každý prvek _pole_ , který představuje definici zásady, má následující vlastnosti:

- `policyDefinitionId` (String): ID vlastní nebo předdefinované definice zásady, která se má zahrnout.
- `policyDefinitionReferenceId` (String): krátký název zahrnuté definice zásady.
- `parameters`: (Volitelné) páry název/hodnota pro předání parametru iniciativy do zahrnuté definice zásad jako vlastnost v této definici zásady. Další informace najdete v tématu [parametry](#parameters).
- `groupNames` (pole řetězců): (volitelné) skupina, které je definicí zásad členem. Další informace najdete v tématu [skupiny zásad](#policy-definition-groups).

Tady je příklad `policyDefinitions` , který má dvě zahrnuté definice zásad, které mají každý stejný parametr iniciativy:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Skupiny definic zásad

Definice zásad v definici iniciativy lze seskupit a kategorizovat. Funkce [dodržování legislativních předpisů](./regulatory-compliance.md) v Azure Policy (Preview) používá tuto vlastnost k seskupení definic do domén **řízení** a **dodržování předpisů**. Tyto informace jsou definovány ve vlastnosti `policyDefinitionGroups` _Array_ . Další podrobnosti o seskupení se dají najít v objektu **policyMetadata** vytvořeném Microsoftem. Informace najdete v tématu [objekty metadat](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Parametry skupin definic zásad

Každý prvek _pole_ v `policyDefinitionGroups` musí mít obě následující vlastnosti:

- `name` (String) \[ požadováno \] : krátký název **skupiny**. **Řízení**dodržování předpisů v souladu se zákonem. Hodnota této vlastnosti je používána `groupNames` v `policyDefinitions` .
- `category` (String): hierarchie, do které skupina patří. V souladu se zákonem o dodržování předpisů, **doména dodržování předpisů** ovládacího prvku.
- `displayName` (String): popisný název pro **skupinu** nebo **ovládací prvek**. Používáno portálem.
- `description` (String): popis toho, co **Skupina** nebo **ovládací prvek** pokrývá.
- `additionalMetadataId` (String): umístění objektu [policyMetadata](#metadata-objects) , který obsahuje další podrobnosti o **řízení** a **doméně dodržování předpisů**.

  > [!NOTE]
  > Zákazníci mohou ukazovat na existující objekt [policyMetadata](#metadata-objects) . Tyto objekty jsou však jen _pro čtení_ a jsou vytvořeny pouze společností Microsoft.

Příklad `policyDefinitionGroups` vlastnosti z předdefinované definice iniciativy NIST vypadá takto:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Objekty metadat

Vestavěné předpisy vytvořené Microsoftem obsahují další informace o jednotlivých ovládacích prvkůch.
Tyto informace jsou:

- Zobrazuje se v Azure Portal přehledu **ovládacího prvku** v iniciativě dodržování předpisů.
- K dispozici prostřednictvím REST API. Prohlédněte si `Microsoft.PolicyInsights` poskytovatele prostředků a [skupinu operací policyMetadata](/rest/api/policy-insights/policymetadata/getresource).
- K dispozici prostřednictvím Azure CLI. Viz příkaz [AZ Policy metadata](/cli/azure/policy/metadata) .

> [!IMPORTANT]
> Objekty metadat pro dodržování předpisů jsou jen _pro čtení_ a zákazníci je nemůžou vytvořit.

Metadata pro seskupení zásad mají v uzlu následující informace `properties` :

- `metadataId`: **ID ovládacího prvku** , ke kterému se vztahuje seskupení.
- `category` (povinné): **doména dodržování předpisů** , ke které **ovládací prvek** patří.
- `title` (povinné): popisný název **ID ovládacího prvku**.
- `owner` (povinné): Určuje, kdo má odpovědnost za ovládací prvek v Azure: _Customer_, _Microsoft_, _Shared_.
- `description`: Další informace o ovládacím prvku.
- `requirements`: Podrobnosti o odpovědnosti za implementaci ovládacího prvku.
- `additionalContentUrl`: Odkaz na Další informace o ovládacím prvku. Tato vlastnost je obvykle odkazem na část dokumentace, která pokrývá tento ovládací prvek ve standardu dodržování předpisů.

Níže je uveden příklad objektu **policyMetadata** . Tato příklad metadat patří k ovládacímu prvku _NIST SP 800-53 R4 AC-1_ .

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Další kroky

- Zobrazit [strukturu definice](./definition-structure.md)
- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
