---
title: Podrobnosti o struktuře definice zásad
description: Popisuje, jak je používat prostředku definice zásady Azure Policy stanovit vytváření názvů pro prostředky ve vaší organizaci zadáním popisu vašeho nového, když je tato zásada vynucená a jaký vliv má provést.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: deaad5e7a04362ab021bfb90ab08377cb99694e0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250380"
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Definice zásad prostředků používají k navázání vytváření názvů pro prostředky Azure Policy. Každá definice popisuje dodržování předpisů prostředkem a co projeví má provést, když prostředek se jako nevyhovující.
Definuje konvence, můžete řídit náklady a snadněji spravovat vaše prostředky. Například můžete určit, zda jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby všechny prostředky měly konkrétní značku. Všechny podřízené prostředky dědí zásady. Pokud zásady se použijí pro skupinu prostředků, se vztahuje na všechny prostředky v příslušné skupině prostředků.

Schéma používané službou Azure Policy najdete tady: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Vytvoření definice zásady pomocí JSON. Definice zásady obsahuje elementy pro:

- režim
- parameters
- Zobrazovaný název
- description
- pravidlo zásad
  - logické hodnocení
  - Efekt

Například následující kód JSON ukazuje zásadu, která omezí, ve které jsou nasazené prostředky:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Všechny ukázky zásady Azure jsou v [ukázky zásad](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Mode

**Režimu** Určuje, jaké typy prostředků, se vyhodnotí zásady. Jsou podporované režimy:

- `all`: vyhodnocení skupiny prostředků a všechny typy prostředků
- `indexed`: jenom vyhodnotit typy prostředků, které podporují značky a umístění

Doporučujeme vám, že jste nastavili **režimu** k `all` ve většině případů. Všechny definice zásad, které jsou vytvořené pomocí portálu `all` režimu. Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure, můžete zadat **režimu** parametr ručně. Pokud neobsahuje definici zásady **režimu** hodnota, použije se výchozí `all` v prostředí Azure PowerShell a o `null` v Azure CLI. A `null` režim je stejný jako při použití `indexed` pro podporu zpětné kompatibility.

`indexed` by měla sloužit při vytváření zásad, které vynucují značky nebo umístění. Přestože se nevyžaduje, brání prostředky, které nepodporují značky a umístěním objeví jako nedodržující předpisy ve výsledcích dodržování předpisů. Výjimkou je **skupiny prostředků**. Zásady, které vynucují místa nebo značky na skupinu prostředků, nastavte **režimu** k `all` a konkrétně cíl `Microsoft.Resources/subscriptions/resourceGroup` typu. Příklad najdete v tématu [vynutit značky skupiny prostředků](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry pomáhají zjednodušit správu zásad snížením počtu definic zásad. Představte si, že parametrů, jako je pole ve formuláři – `name`, `address`, `city`, `state`. Tyto parametry vždy stejné, ale změnit jejich hodnoty podle jednotlivých vyplňování formuláře.
Parametry fungovat stejným způsobem jako při vytváření zásad. Včetně parametrů v definici zásad, můžete využít této zásadě pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Definice parametry pro zásadu nebo definice iniciativy můžete nakonfigurovat jenom během počátečního vytváření zásady nebo iniciativa. Parametry definici není možné později změnit.
> To zabrání existující přiřazení zásady nebo iniciativa nepřímo prováděné neplatný.

Například můžete definovat zásady pro omezení umístění, kde můžete nasadit prostředky.
Při vytváření zásady, by deklarovat následující parametry:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Typ parametru může být řetězec nebo pole. Vlastnost metadat se používá pro nástroje, jako je na webu Azure portal k zobrazení informací uživatelsky přívětivé.

V rámci vlastnost metadat, můžete použít **strongType** poskytnout vícenásobný výběr seznam možností na webu Azure portal. Povolené hodnoty pro **strongType** aktuálně zahrnují:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

V pravidlu zásad můžete odkazovat na parametry u následujících `parameters` syntaxe funkce hodnotu nasazení:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Umístění definice

Při vytváření iniciativy nebo zásady, je nutné zadat umístění definice. Umístění definice musí být skupinu pro správu nebo předplatného. Toto umístění Určuje obor, ke které je možné přiřadit iniciativy nebo zásady. Prostředky musí být přímými členy nebo podřízené prvky v rámci hierarchie umístění definice cílit na přiřazení.

Pokud je umístění definice:

- **Předplatné** – pouze pro prostředky v rámci tohoto předplatného je možné přiřadit zásady.
- **Skupina pro správu** – pouze pro prostředky v rámci podřízené skupiny pro správu a podřízené předplatná je možné přiřadit zásady. Pokud budete chtít použít definici zásady pro několik předplatných, musí být umístění skupiny pro správu, který obsahuje těchto předplatných.

## <a name="display-name-and-description"></a>Zobrazovaný název a popis

Použijete **displayName** a **popis** identifikovat definice zásad a zadání při použití kontextu.

## <a name="policy-rule"></a>Pravidlo zásad

Pravidlo zásad se skládá z **Pokud** a **pak** bloky. V **Pokud** bloku, definujete jednu nebo více podmínek, které určují, kdy je tato zásada vynucená. Tyto podmínky přesně definovat scénář pro zásady můžete použít logické operátory.

V **pak** bloku, definujete efekt, který se stane, když **Pokud** jsou splněny podmínky.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logické operátory

Podporované logické operátory jsou:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**Není** syntaxe obrátí výsledek podmínky. **AllOf** syntaxe (podobně jako logický **a** operace) vyžaduje všechny podmínky na hodnotu true. **AnyOf** syntaxe (podobně jako logický **nebo** operace) vyžaduje jednu nebo více podmínek na hodnotu true.

Logické operátory lze vnořit. Následující příklad ukazuje **není** operace, která je vnořená v rámci **allOf** operace.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Podmínky

Podmínka vyhodnotí, zda **pole** splňuje určitá kritéria. Jsou podporované podmínky:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Při použití **jako** a **notLike** podmínky, zadat zástupný znak `*` v hodnotě.
Hodnota by neměla mít více než jeden zástupný znak `*`.

Při použití **odpovídat** a **notMatch** podmínky, poskytují `#` tak, aby odpovídaly číslici, `?` pro písmeno, `.` tak, aby odpovídaly všechny znaky a jakýkoli jiný znak tak, aby odpovídaly skutečné znaku. Příklady najdete v tématu [povolit několik vzory názvů](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fields (Pole)

Podmínky jsou vytvářena pomocí polí. Pole odpovídá vlastnosti datové části požadavku prostředků a popisuje stav prostředku.

Podporovány jsou následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku. Úplný název prostředku je název prostředku předcházený žádné nadřazené názvy prostředků (například "myServer/databáze").
- `kind`
- `type`
- `location`
  - Použití **globální** za prostředky, které jsou nezávislé na umístění. Příklad najdete v tématu [ukázky – povolená umístění](../samples/allowed-locations.md).
- `identity.type`
  - Vrátí typ [Identity spravované](../../../active-directory/managed-identities-azure-resources/overview.md) u daného prostředku povolena.
- `tags`
- `tags.<tagName>`
  - Kde **\<tagName\>** je název značky ověřit podmínku.
  - Příklad: `tags.CostCenter` kde **nákladové středisko** je název značky.
- `tags[<tagName>]`
  - Tato syntaxe závorky podporuje názvy značek, které mají tečku.
  - Kde **\<tagName\>** je název značky ověřit podmínku.
  - Příklad: `tags[Acct.CostCenter]` kde **Acct.CostCenter** je název značky.
- Vlastnost aliasy – seznam najdete v tématu [aliasy](#aliases).

### <a name="effect"></a>Efekt

Zásady podporuje následující typy dopadu:

- **Odepřít**: vygeneruje událost v protokolu aktivit a požadavek selže
- **Audit**: vygeneruje událost upozornění v protokolu aktivit, ale neselže, je požadavek
- **Připojit**: Přidá definovanou sadu polí k této žádosti
- **AuditIfNotExists**: povolí auditování Pokud prostředek neexistuje.
- **DeployIfNotExists**: nasadí prostředek, pokud už neexistuje.
- **Zakázané**: nevyhodnocuje prostředky pro pravidlo zásad dodržování předpisů

Pro **připojit**, je nutné zadat následující údaje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Hodnota může být řetězec nebo objekt formátu JSON.

**AuditIfNotExists** a **DeployIfNotExists** vyhodnotit existenci souvisejících prostředků a použít pravidlo. Pokud prostředek neodpovídá pravidlo, je implementováno efekt. Můžete třeba vyžadovat, že sledovací proces sítě se nasazuje pro všechny virtuální sítě. Další informace najdete v tématu [auditovat, jestli rozšíření neexistuje](../samples/audit-ext-not-exist.md) příklad.

**DeployIfNotExists** vyžaduje vliv **roledefinitionid, které je** vlastnost **podrobnosti** část pravidla zásad. Další informace najdete v tématu [nápravy - nakonfigurovat definici zásady](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Kompletní informace o jednotlivých vliv pořadí vyhodnocení, vlastností a příkladů, najdete v části [účinky zásad Principy](effects.md).

### <a name="policy-functions"></a>Funkce zásad

Několik [funkce šablon Resource Manageru](../../../azure-resource-manager/resource-group-template-functions.md) jsou k dispozici pro použití v rámci pravidla zásad. Jsou aktuálně podporované funkce:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [předplatné](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Kromě toho `field` funkce je k dispozici pro pravidla zásad. `field` se používá především s **AuditIfNotExists** a **DeployIfNotExists** na odkaz na pole v prostředku, které jsou právě vyhodnocována. Příklad použití si můžete prohlédnout ve [DeployIfNotExists příklad](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Příklady zásad – funkce

Používá tento příklad pravidla zásad `resourceGroup` prostředků funkce získáte **název** vlastnost, společně s `concat` pole a objektu funkce pro sestavení `like` podmínku, která vynucuje název prostředku pomocí názvu skupiny prostředků.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

Používá tento příklad pravidla zásad `resourceGroup` prostředků funkce získáte **značky** hodnota vlastnosti pole **nákladové středisko** označit skupiny prostředků a připojte ho k **nákladové středisko**  značku na nový prostředek.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Aliasy

Vlastnost aliasy používáte pro přístup k určité vlastnosti pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povolena pro vlastnost prostředku. Každý alias namapuje cesty v různých verzích rozhraní API pro typ daný prostředek. Modul zásad během hodnocení zásad, získá vlastnost cesty pro tuto verzi rozhraní API.

Seznam aliasů se pořád rozrůstá. Pokud chcete zjistit, jaké aliasy jsou aktuálně podporovány službou Azure Policy, použijte jednu z následujících metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- Rozhraní REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Principy alias [*]

Některé aliasy, které jsou k dispozici máte verzi, která se zobrazí jako 'normal' název, který má **[\*]** k němu připojená. Příklad:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

V prvním příkladu se používá k vyhodnocení celého pole, ve kterém **[\*]** alias vyhodnocuje každý prvek pole.

Podívejme se na pravidlo zásad jako příklad. Tato zásada **Odepřít** účtu úložiště, která má nakonfigurované ipRules a pokud **žádný** ipRules má hodnotu "127.0.0.1".

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

**IpRules** pole je následující příklad:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Zde je, jak se zpracovávají v tomto příkladu:

- `networkAcls.ipRules` – Zkontrolujte, že pole je jiná než null. Je vyhodnocen jako true, takže vyhodnocování pokračuje.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` -Zkontroluje každý _hodnotu_ vlastnost **ipRules** pole.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Jako pole, které se budou zpracovávat každý prvek.

    - "127.0.0.1"! = "127.0.0.1" vyhodnotí jako false.
    - "127.0.0.1"! = "192.168.1.1" vyhodnotí jako true.
    - Alespoň jeden _hodnotu_ vlastnost **ipRules** pole vyhodnotí jako false, takže vyhodnocování se zastaví.

Jako podmínka vyhodnocena jako false **Odepřít** efekt neaktivuje.

## <a name="initiatives"></a>Iniciativy

Iniciativy umožňují seskupit několik definic zásad souvisejících ke zjednodušení přiřazení a správy, protože pracujete s skupinu jako jednu položku. Například můžete seskupovat související označení definice zásad do jednoho initiative. Místo toho každé zásady zvlášť, můžete použít iniciativy.

Následující příklad ukazuje, jak vytvořit iniciativu pro zpracování dvěma značkami: `costCenter` a `productName`. Využívá dvě předdefinované zásady použít výchozí hodnotu značky.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
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
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md)
- Kontrola [Principy účinky zásad](effects.md)
- Pochopit postup [programové vytváření zásad](../how-to/programmatically-create.md)
- Zjistěte, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md)
- Zjistěte, jak [opravit nekompatibilní prostředky](../how-to/remediate-resources.md)
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../../management-groups/overview.md)