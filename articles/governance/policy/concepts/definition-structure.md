---
title: Podrobnosti struktury definice zásad
description: Popisuje použití definice zásad prostředků Azure Policy k vytvoření konvencí pro prostředky ve vaší organizaci, a to tak, že popisuje, kdy se zásada vynutila a jaký je efekt, který je potřeba provést.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 27cf1539fc98b2ad7f1b82e194989c1619ab99fb
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980712"
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Definice zásad prostředků používá Azure Policy k navázání konvencí pro prostředky. Každá definice popisuje dodržování předpisů a postup, který se má provést v případě nedodržení prostředku.
Definováním konvencí můžete řídit náklady a snadněji spravovat prostředky. Můžete například určit, že jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby všechny prostředky měly konkrétní značku. Zásady se dědí ze všech podřízených prostředků. Pokud se zásada použije pro skupinu prostředků, vztahuje se na všechny prostředky v této skupině prostředků.

Schéma definice zásad najdete tady: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

K vytvoření definice zásady použijete JSON. Definice zásad obsahuje prvky pro:

- Mode
- parameters
- zobrazované jméno
- description
- pravidlo zásad
  - logické vyhodnocení
  - až

Například následující JSON zobrazuje zásadu, která omezuje, kde jsou prostředky nasazeny:

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
                },
                "defaultValue": [ "westus2" ]
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

Všechny ukázky Azure Policy jsou na [Azure Policy Samples](../samples/index.md).

## <a name="mode"></a>Režim

**Režim** je nakonfigurován v závislosti na tom, zda je zásada cílena na vlastnost Azure Resource Manager nebo vlastnost poskytovatele prostředků.

### <a name="resource-manager-modes"></a>Správce prostředků režimy

**Režim** určuje, které typy prostředků se budou vyhodnocovat pro zásady. Podporované režimy:

- `all`: vyhodnotí skupiny prostředků a všechny typy prostředků.
- `indexed`: vyhodnotí jenom typy prostředků, které podporují značky a umístění.

Ve většině případů doporučujeme nastavit **režim** na `all`. Všechny definice zásad vytvořené prostřednictvím portálu používají režim @no__t 0. Pokud používáte PowerShell nebo Azure CLI, můžete zadat parametr **Mode** ručně. Pokud definice zásady neobsahuje hodnotu **režimu** , použije se výchozí hodnota `all` v Azure PowerShell a `null` v rozhraní příkazového řádku Azure CLI. Režim `null` je stejný jako použití `indexed` pro podporu zpětné kompatibility.

`indexed` by se měly používat při vytváření zásad, které vydávají značky nebo umístění. I když to není nutné, zabrání prostředkům, které nepodporují značky a umístění, z hlediska výsledků dodržování předpisů v nedodržení předpisů. Výjimkou jsou **skupiny prostředků**. Zásady, které vynutily umístění nebo značky ve skupině prostředků, by měly nastavit **režim** na `all` a konkrétně cílit na typ `Microsoft.Resources/subscriptions/resourceGroups`. Příklad najdete v tématu [vymáhání značek skupin prostředků](../samples/enforce-tag-rg.md). Seznam prostředků, které podporují značky, najdete v tématu [Podpora značek pro prostředky Azure](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Režimy poskytovatele prostředků

V současné době je podporovaný jenom režim poskytovatele prostředků `Microsoft.ContainerService.Data` pro správu pravidel kontroleru přístupu pro [službu Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy pro Kubernetes](rego-for-aks.md) jsou v Public Preview a podporují jenom předdefinované definice zásad.

## <a name="parameters"></a>Parametry

Parametry vám pomůžou zjednodušit správu zásad tím, že se sníží počet definic zásad. Představte si parametry, jako jsou pole ve formuláři – `name`, `address`, `city`, `state`. Tyto parametry vždy zůstávají stejné, ale jejich hodnoty se změní na základě jednotlivých vyplňování formuláře.
Parametry fungují stejným způsobem při vytváření zásad. Zahrnutím parametrů do definice zásady můžete tuto zásadu použít pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Parametry mohou být přidány do existující a přiřazené definice. Nový parametr musí zahrnovat vlastnost **DefaultValue** . To brání tomu, aby se stávající přiřazení zásady nebo iniciativy od nepřímo neplatně vytvořila.

### <a name="parameter-properties"></a>Vlastnosti parametru

Parametr má následující vlastnosti, které se používají v definici zásady:

- **Name (název**): název parametru. Používá se funkcí nasazení `parameters` v rámci pravidla zásad. Další informace najdete v tématu [použití hodnoty parametru](#using-a-parameter-value).
- `type`: Určuje, zda je parametr typu **řetězec**, **pole**, **objekt**, **Boolean**, **Integer**, **float**nebo **DateTime**.
- `metadata`: definuje podvlastnosti primárně používané Azure Portal k zobrazení uživatelsky přívětivých informací:
  - `description`: vysvětlení použití parametru pro. Dá se použít k zadání příkladů přijatelných hodnot.
  - `displayName`: popisný název zobrazený na portálu pro parametr.
  - `strongType`: (volitelné) používá se při přiřazování definice zásady prostřednictvím portálu. Poskytuje seznam podporující kontext. Další informace najdete v tématu [strongType](#strongtype).
  - `assignPermissions`: (volitelné) nastavte na _hodnotu true_ , pokud chcete, aby Azure Portal během přiřazování zásady vytvořila přiřazení rolí. Tato vlastnost je užitečná v případě, že chcete přiřadit oprávnění mimo rozsah přiřazení. Podle definice role v zásadě (nebo definice role v rámci všech zásad v iniciativě) existuje jedno přiřazení role. Hodnota parametru musí být platným prostředkem nebo oborem.
- `defaultValue`: (volitelné) nastaví hodnotu parametru v přiřazení, pokud není zadána žádná hodnota.
  Vyžaduje se při aktualizaci existující definice zásady, která je přiřazená.
- `allowedValues`: (volitelné) poskytuje pole hodnot, které parametr akceptuje během přiřazování.

Jako příklad můžete definovat definici zásady, která omezí umístění, kde mohou být prostředky nasazeny. Parametr této definice zásad může být **allowedLocations**. Tento parametr by se použil při každém přiřazení definice zásady, aby se omezily přijatelné hodnoty. Použití **strongType** poskytuje vylepšené prostředí při dokončování přiřazení prostřednictvím portálu:

```json
"parameters": {
    "allowedLocations": {
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

### <a name="using-a-parameter-value"></a>Použití hodnoty parametru

V pravidle zásad se odkazuje na parametry s následující syntaxí funkce @no__t hodnoty nasazení-0:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Tento příklad odkazuje na parametr **allowedLocations** , který byl prokázán ve [vlastnostech parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

V rámci vlastnosti `metadata` můžete použít **strongType** k poskytnutí seznamu možností s vícenásobným výběrem v rámci Azure Portal. Povolené hodnoty pro **strongType** aktuálně zahrnují:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Umístění definice

Při vytváření iniciativy nebo zásad je nutné zadat umístění definice. Umístění definice musí být skupina pro správu nebo předplatné. Toto umístění určuje obor, do kterého lze iniciativu nebo zásadu přiřadit. Prostředky musí být přímé členy nebo podřízené položky v rámci hierarchie umístění definice na cíl pro přiřazení.

Pokud je umístění definice:

- Zásadu můžou přiřadit jenom prostředky v rámci daného **předplatného** .
- Zásadu můžou přiřadit jenom prostředky v rámci podřízených skupin **pro správu a** podřízených předplatných. Pokud plánujete použít definici zásady pro několik předplatných, umístění musí být skupina pro správu, která obsahuje tyto odběry.

## <a name="display-name-and-description"></a>Zobrazované jméno a popis

K identifikaci definice zásad a zadání kontextu, kdy se má použít, můžete použít **DisplayName** a **Description** . hodnota **DisplayName** má maximální délku _128_ znaků a **popis** nesmí být delší než _512_ znaků.

## <a name="policy-rule"></a>Pravidlo zásad

Pravidlo zásad se skládá z **if** a **pak** Block. V bloku **if** můžete definovat jednu nebo více podmínek, které určují, kdy se zásada vynutila. Můžete použít logické operátory na tyto podmínky a přesně tak definovat scénář pro zásady.

V bloku **potom** definujete efekt, který nastane, když jsou splněny podmínky **if** .

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

Syntaxe **Not** převertuje výsledek podmínky. Syntaxe **allOf** (podobná logické hodnotě **a** operaci) vyžaduje splnění všech podmínek. Syntaxe **anyOf** (podobná logické hodnotě **nebo** operaci) vyžaduje splnění jedné nebo více podmínek.

Logické operátory můžete vnořovat. Následující příklad ukazuje, že operace **není** vnořena v rámci operace **allOf** .

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

Podmínka vyhodnocuje, zda **pole** nebo **hodnota** přistupující objekty splňují určitá kritéria. Podporované podmínky jsou:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Při použití podmínek **Like** a **notLike** zadejte do hodnoty zástupný znak `*`.
Hodnota by neměla mít více než jeden zástupný znak `*`.

Pokud používáte podmínky **Match** a **notMatch** , zadejte `#`, aby odpovídaly číslici, `?` pro písmeno, `.` tak, aby odpovídaly jakémukoli znaku a dalším znakům, aby odpovídaly tomuto skutečnému znaku.
Při **porovnávání** a **notMatch** se rozlišují velká a malá písmena. Alternativy nerozlišující velká a malá písmena jsou k dispozici v **matchInsensitively** a **notMatchInsensitively**. Příklady najdete v tématu [povolení několika vzorů názvů](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fields (Pole)

Podmínky jsou tvořeny pomocí polí. Pole odpovídá vlastnostem v datové části požadavku prostředku a popisuje stav prostředku.

Podporují se následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku. Úplný název prostředku je název prostředku, který předchází všechny názvy nadřazených prostředků (například "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Pro prostředky, které jsou nezávislá umístění, použijte **globální** . Příklad najdete v tématu [s povolenými umístěními Samples](../samples/allowed-locations.md).
- `identity.type`
  - Vrátí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) povolené v prostředku.
- `tags`
- `tags['<tagName>']`
  - Tato syntaxe závorky podporuje názvy značek, které mají interpunkční znaménka, jako je například spojovník, tečka nebo mezera.
  - Kde **\<tagName @ no__t-2** je název značky pro ověření podmínky.
  - Příklady: `tags['Acct.CostCenter']`, kde **Acct. CostCenter** je název značky.
- `tags['''<tagName>''']`
  - Tato syntaxe závorky podporuje názvy značek, které mají apostrofy, pomocí uvozovacích znaků s dvojitými apostrofy.
  - Kde **' \<tagName @ no__t-2 '** je název značky, pro kterou má být podmínka ověřena.
  - Příklad: `tags['''My.Apostrophe.Tag''']`, kde **' \<tagName @ no__t-3 '** je název značky.
- aliasy vlastností – pro seznam najdete v tématu [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` a `tags[tag.with.dots]` jsou stále přijatelné způsoby deklarace pole značek. Preferované výrazy jsou však uvedeny výše.

#### <a name="use-tags-with-parameters"></a>Použití značek s parametry

Hodnota parametru může být předána poli značky. Předání parametru do pole značek zvyšuje flexibilitu definice zásad během přiřazení zásady.

V následujícím příkladu `concat` slouží k vytvoření vyhledávání pole značek pro značku s názvem hodnota parametru **TagName** . Pokud tato značka neexistuje, **použije se k** přidání značky pomocí hodnoty stejné pojmenované sady jako v nadřazené skupině prostředků auditované prostředky pomocí funkce vyhledávání `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Hodnota

Podmínky mohou být také vytvořeny pomocí **hodnoty**. **hodnota** kontroluje podmínky proti [parametrům](#parameters), [podporované funkce šablon](#policy-functions)nebo literály.
**hodnota** je spárována s libovolnou podporovanou [podmínkou](#conditions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Další informace najdete v tématu [předcházení chybám šablon](#avoiding-template-failures).

#### <a name="value-examples"></a>Příklady hodnot

Tato ukázka pravidla zásad používá **hodnotu** k porovnání výsledku funkce `resourceGroup()` a vlastnosti vráceného **názvu** s podmínkou **Like** `*netrg`. Pravidlo odmítne jakýkoli prostředek, který není **typu** `Microsoft.Network/*` v žádné skupině prostředků, jejíž název končí na `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Tato ukázka pravidla zásad používá **hodnotu** ke kontrole, jestli výsledek více vnořených funkcí **se rovná** `true`. Pravidlo odmítne všechny prostředky, které nemají alespoň tři značky.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Předcházení chybám šablon

Použití _šablonových funkcí_ v **hodnotě** umožňuje mnoho komplexních vnořených funkcí. Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Příklad **hodnoty** , která se v některých scénářích nezdařila:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Vzorové pravidlo výše uvedené používá [podřetězec ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) pro porovnání prvních tří znaků **názvu** s **ABC**. Pokud je **název** kratší než tři znaky, výsledkem funkce `substring()` bude chyba. Tato chyba způsobí, že se zásada stane účinkem **odepření** .

Místo toho použijte funkci [if ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) ke kontrole, zda první tři znaky **názvu** odpovídají **ABC** bez povolení **názvu** kratšího než tři znaky, aby došlo k chybě:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Pomocí revidovaného pravidla zásad `if()` zkontroluje délku **názvu** a potom se pokusí získat `substring()` na hodnotu s méně než třemi znaky. Pokud je **název** příliš krátký, je místo toho vrácena hodnota "nezačíná na ABC" a porovnána s **ABC**. Prostředek s krátkým názvem, který nezačíná na **ABC** , se stále neúspěšně stane pravidlem zásad, ale během vyhodnocování se nestane příčinou chyby.

### <a name="effect"></a>Efekt

Azure Policy podporuje následující typy účinku:

- **Deny**: vygeneruje událost v protokolu aktivit a neuspěje požadavek.
- **Audit**: vygeneruje událost upozornění v protokolu aktivit, ale požadavek neselže.
- **Připojit**: přidá do žádosti definovanou sadu polí.
- **AuditIfNotExists**: povolí auditování, pokud prostředek neexistuje.
- **DeployIfNotExists**: nasadí prostředek, pokud ještě neexistuje.
- **Zakázáno**: nevyhodnotí prostředky pro dodržování předpisů pro pravidlo zásad.
- **EnforceRegoPolicy**: konfiguruje řadič otevřeného přístupu agenta zásad ve službě Azure Kubernetes (Preview).
- **Upravit**: Přidání, aktualizace nebo odebrání definovaných značek z prostředku

Pro **připojení**je nutné zadat následující údaje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Hodnota může být buď řetězec, nebo objekt formátu JSON.

**AuditIfNotExists** a **DeployIfNotExists** vyhodnocují existenci souvisejícího prostředku a použijí pravidlo. Pokud se prostředek neshoduje s pravidlem, je tento efekt implementován. Můžete třeba vyžadovat, aby se sledovací proces sítě nasadil pro všechny virtuální sítě. Další informace najdete v příkladech pro [audit, pokud rozšíření neexistuje](../samples/audit-ext-not-exist.md) .

**DeployIfNotExists** efekt vyžaduje vlastnost **roleDefinitionId** v části **podrobností** pravidla zásad. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Podobně platí, že příkaz **Upravit** vyžaduje vlastnost **roleDefinitionId** v části **podrobností** pravidla zásad pro [úlohu nápravy](../how-to/remediate-resources.md). **Úprava** také vyžaduje, aby pole **operace** definovala akce, které se mají u značek prostředků provést.

Podrobné informace o každém z efektů, pořadí vyhodnocení, vlastností a příkladů najdete v tématu [principy Azure Policych efektů](effects.md).

### <a name="policy-functions"></a>Funkce zásad

Pro použití v rámci pravidla zásad jsou k dispozici všechny [funkce šablon Správce prostředků](../../../azure-resource-manager/resource-group-template-functions.md) , s výjimkou následujících funkcí a uživatelsky definovaných funkcí:

- copyIndex()
- nasazení ()
- seznamu
- newGuid()
- pickZones()
- Zprostředkovatelé ()
- Reference ()
- resourceId ()
- proměnné ()

Následující funkce jsou k dispozici pro použití v pravidle zásad, ale liší se od použití v šabloně Azure Resource Manager:

- addDays (dateTime; numberOfDaysToAdd)
  - **DateTime**: [required] řetězec řetězce ve formátu Universal ISO 8601 DateTime "rrrr-MM-DDThh: mm: ss. fffffffZ"
  - **numberOfDaysToAdd**: [povinné] celé číslo-počet dnů k přidání
- utcNow () – na rozdíl od šablony Správce prostředků lze použít mimo defaultValue.
  - Vrátí řetězec, který je nastaven na aktuální datum a čas ve formátu Universal ISO 8601 DateTime yyyy-MM-ddTHH: mm: ss. fffffffZ.

Kromě toho je k dispozici funkce `field` pro pravidla zásad. `field` se primárně používá s **AuditIfNotExists** a **DeployIfNotExists** k odkazování na pole v prostředku, který se vyhodnocuje. Příklad tohoto použití lze zobrazit v [DeployIfNotExists příkladu](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Příklad funkce zásad

Tato ukázka pravidla zásad používá funkci prostředku `resourceGroup` k získání vlastnosti **Name** v kombinaci s funkcí Array a Array `concat` pro sestavení podmínky `like`, která vynutila název prostředku, aby začal s názvem skupiny prostředků.

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

## <a name="aliases"></a>Aliasy

Aliasy vlastností se používají pro přístup ke specifickým vlastnostem pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povoleny pro vlastnost v prostředku. Každý alias se mapuje na cesty v různých verzích rozhraní API pro daný typ prostředku. Během hodnocení zásad získá modul zásad cestu k vlastnosti pro tuto verzi rozhraní API.

Seznam aliasů se vždycky zvětšuje. Chcete-li zjistit, které aliasy jsou aktuálně podporovány nástrojem Azure Policy, použijte jednu z následujících metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Princip aliasu [*]

Několik dostupných aliasů má verzi, která se zobrazuje jako "normální" název a další, která má k ní přiřazený **[\*]** . Například:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias ' Normal ' představuje pole jako jedinou hodnotu. Toto pole je určeno pro přesné scénáře porovnání shody, pokud celá sada hodnot musí být přesně definovaná, a ne více a méně.

Alias **[\*]** umožňuje porovnání s hodnotou každého prvku v poli a konkrétní vlastnosti každého prvku. Tento přístup umožňuje porovnat vlastnosti elementu pro ' Pokud žádný z ', ', pokud je to u všech scénářů ', ', ', '. Pomocí **ipRules [\*]** může příklad ověřit, že každá _Akce_ je _Zamítnutá_, ale nemusíte si dělat obavy o tom, kolik pravidel existuje nebo jaká je _hodnota_ IP. Toto ukázkové pravidlo vyhledá všechny shody **ipRules [\*]. Value** to **10.0.4.1** a použije **effectType** pouze v případě, že nenajde alespoň jednu shodu:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Další informace najdete v tématu [vyhodnocení aliasu [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Iniciativy

Iniciativy umožňují seskupit několik souvisejících definic zásad, které zjednodušují přiřazování a správu, protože pracujete se skupinou jako s jednou položkou. Můžete například seskupit související definice zásad označování do jedné iniciativy. Místo toho, abyste každou zásadu přiřadíte jednotlivě, můžete použít iniciativu.

Následující příklad ukazuje, jak vytvořit iniciativu pro zpracování dvou značek: `costCenter` a `productName`. Pomocí dvou předdefinovaných zásad použije výchozí hodnotu značky.

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).