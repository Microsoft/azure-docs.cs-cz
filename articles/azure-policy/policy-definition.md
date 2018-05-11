---
title: Struktura definic Azure Policy
description: Popisuje použití zásad definice prostředků zásadami Azure k vytvoření konvence pro prostředky ve vaší organizaci pomocí popisující, když je tato zásada vynucená a jaký vliv má provést.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: a56fa61c6d77ab50dc1342c5a7feeaf1c579697d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Definice zásad prostředků používané zásad Azure umožňuje vytvořit konvence pro prostředky ve vaší organizaci prostřednictvím popisu, když je tato zásada vynucená a jaký vliv má provést. Definováním konvence můžete řídit náklady a snadněji spravovat vaše prostředky. Například můžete zadat, že jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby všechny prostředky měli konkrétní značku. Zásady jsou zdědí všechny podřízené prostředky. Ano Pokud je zásada pro skupinu prostředků, se vztahuje na všechny prostředky v příslušné skupině prostředků.

Schéma používá zásad Azure naleznete zde: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

JSON použijete k vytvoření definice zásady. Definice zásad obsahuje prvky pro:

- režim
- parametry
- Zobrazovaný název
- Popis
- Pravidlo zásad
  - logické vyhodnocení
  - Platnost

Například následujícím kódu JSON zobrazuje zásadu, která omezuje, kdy jsou prostředky nasazené:

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

Všechny ukázky šablony zásad Azure jsou [šablon pro Azure zásad](json-samples.md).

## <a name="mode"></a>Režim

**Režimu** Určuje, jaké typy prostředků se vyhodnotí pro zásadu. Podporované režimy jsou:

- `all`: vyhodnocení skupiny prostředků a všechny typy prostředků
- `indexed`: pouze vyhodnotit typy prostředků, které podporují značky a umístění

Doporučujeme, abyste nastavili **režimu** k `all` ve většině případů. Všechny definice zásady vytvořené pomocí portálu použijte `all` režimu. Pokud používáte prostředí PowerShell nebo rozhraní příkazového řádku Azure, můžete zadat **režimu** parametr ručně. Pokud definice zásady neobsahuje **režimu** hodnota je výchozí hodnota je `all` v prostředí Azure PowerShell a na `null` v rozhraní příkazového řádku Azure, což je totéž jako `indexed`, pro zpětné kompatibility.

`indexed` by měl být použit při vytváření zásad, který vynutí značky nebo umístění. Tato akce není povinná, ale nebude možné prostředky, které nepodporují značky a umístění zobrazovala jako nevyhovující ve výsledcích dodržování předpisů. Jedinou výjimkou je **skupiny prostředků**. Měli nastavit zásady, které se pokoušíte vynutit umístění nebo značky na skupinu prostředků **režimu** k `all` a konkrétně cíle `Microsoft.Resources/subscriptions/resourceGroup` typu. Příklad, naleznete v části [vynutit značky prostředku skupiny](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry zjednodušit vaší zásady správy snížením počtu definice zásady. Představte si, že parametry jako pole ve formuláři – `name`, `address`, `city`, `state`. Tyto parametry vždy zůstaly stejné, ale změnit jejich hodnoty podle jednotlivých vyplňování formuláře. Parametry fungovat stejným způsobem jako při vytváření zásad. Zahrnutím parametry v definici zásady můžete znovu použít tuto zásadu pro různé scénáře pomocí různé hodnoty.

Můžete třeba definovat zásady pro vlastnosti prostředku, který omezit umístění, kde můžete nasadit prostředky. Při vytváření zásady, v takovém případě by deklarovat následující parametry:

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

Typ parametru může být řetězec nebo pole. Vlastnost metadat slouží k nástroje, například na portálu Azure a zobrazte uživatelské informace.

V rámci vlastnost metadat, můžete použít **strongType** zajistit vybrat víc seznam možností v rámci portálu Azure.  Povolené hodnoty pro **strongType** aktuálně patří:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

V pravidle zásady můžete odkazovat na parametry s následující syntaxí:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Umístění definice

Při vytváření definice initiative nebo zásady, je třeba zadat umístění definice.

Definice umístění Určuje obor, ke kterému lze přiřadit definici initiative nebo zásad. Umístění lze zadat jako skupinu pro správu nebo předplatné.

> [!NOTE]
> Pokud budete chtít použít tuto definici zásady pro více předplatných, musí být umístění obsahující odběry, které chcete přiřadit initiative nebo zásady skupiny pro správu.

## <a name="display-name-and-description"></a>Zobrazovaný název a popis

Můžete použít **displayName** a **popis** k identifikaci definice zásady a poskytují kontext pro při použití.

## <a name="policy-rule"></a>Pravidlo zásad

Pravidla zásad se skládá z **Pokud** a **pak** bloky. V **Pokud** blok, můžete definovat jednu nebo víc podmínek, které určují, kdy je tato zásada vynucená. Logické operátory můžete použít pro tyto podmínky přesně definovat, tento scénář pro zásady.

V **pak** bloku, definujete o tom, že se stane, když **Pokud** podmínky jsou splněny.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Logické operátory

Jsou podporované logické operátory:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**Není** syntaxe Invertuje výběr výsledek podmínku. **AllOf** syntaxe (podobně jako logické **a** operaci) vyžaduje všechny podmínek. **AnyOf** syntaxe (podobně jako logické **nebo** operaci) vyžaduje jeden nebo více podmínek.

Logické operátory lze vnořit. Následující příklad ukazuje **není** operace, která je vnořená v rámci **allOf** operaci.

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

Podmínka vyhodnocena jako jestli **pole** splňuje určitá kritéria. Jsou podporované podmínky:

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

Při použití **jako** a **notLike** podmínky, můžete zadat zástupný znak (*) v hodnotě.

Při použití **odpovídat** a **notMatch** podmínky, poskytují `#` představují číslice, `?` pro písmeno a libovolný znak představují tento skutečný znak. Příklady najdete v tématu [povolit více vzory názvů](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Pole

Podmínky se vytváří pomocí pole. Pole představuje vlastnosti v datová část požadavku prostředku, který se používá k popisu stavu prostředku.  

Podporovány jsou následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku, včetně všech nadřazených položek (například "myServer/databáze")
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - Tato syntaxe závorky podporuje názvy značek, které obsahovat tečky.
- Vlastnost aliasy – seznam najdete v tématu [aliasy](#aliases).

### <a name="alternative-accessors"></a>Alternativní přístupové objekty

**Pole** je primární přistupujícího objektu používá v pravidla zásad. Zkontroluje přímo k prostředku, který se vyhodnocuje. Však zásady podporuje jeden další přistupujícího objektu **zdroj**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Zdroj** podporuje pouze jednu hodnotu, **akce**. Akce vrátí autorizace akce požadavek, který se vyhodnocuje. Autorizace akce jsou zveřejněné v části autorizace [protokol aktivit](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Zásady je vyhodnocení existující prostředky na pozadí, nastaví **akce** k `/write` autorizace akce u typu prostředku.

### <a name="effect"></a>Účinek

Zásady podporuje následující typy vliv:

- **Odepřít**: vygeneruje událost v protokolu auditování a požadavek se nezdaří
- **Audit**: vygeneruje událost upozornění v protokolu auditu ale nesplní žádost
- **Připojit**: Přidá definovanou sadu pole k této žádosti
- **AuditIfNotExists**: umožňuje auditování, pokud prostředek neexistuje.
- **DeployIfNotExists**: nasadí prostředku, pokud ještě neexistuje. V současné době tímto účelem je podporována pouze prostřednictvím integrovaných zásad.

Pro **připojit**, je nutné zadat následující podrobnosti:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Hodnota může být řetězec nebo objekt formátu JSON.

S **AuditIfNotExists** a **DeployIfNotExists** můžete vyhodnotit existenci související prostředek a použít pravidlo a odpovídající efekt, pokud tento prostředek neexistuje. Například může vyžadovat, že sledovací proces sítě nasazuje pro všechny virtuální sítě.
Příklad audit, když není nasazený rozšíření virtuálního počítače, naleznete v části [Audit Pokud rozšíření neexistuje](scripts/audit-ext-not-exist.md).

## <a name="aliases"></a>Aliasy

Vlastnost aliasy používáte pro přístup k vlastnosti specifické pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povoleny pro vlastnost prostředku. Každý alias mapuje cesty v různých verzích rozhraní API pro typ daného prostředku. Modul zásad během hodnocení zásad, získá vlastnost cesty pro tuto verzi rozhraní API.

Seznam aliasy vždy roste. Chcete-li zjistit, jaké aliasy jsou aktuálně podporovány zásadami Azure, použijte jednu z následujících metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- Rozhraní API REST nebo ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>Iniciativy

Povolit iniciativy seskupit několik související definice zásad ke zjednodušení přiřazení a správy, protože pracujete s skupinu jako jednu položku. Například můžete seskupit všechny související označování definice zásady v jednom iniciativy. Přiřazovat jednotlivě každou zásadu, můžete použít iniciativa zaměřená.

Následující příklad ukazuje, jak vytvořit initiative pro zpracování dvě značky: `costCenter` a `productName`. Chcete-li použít výchozí hodnota značky používá dvě předdefinované zásady.

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

- Přečtěte si ukázky šablony zásad Azure v [šablon pro Azure zásad](json-samples.md).