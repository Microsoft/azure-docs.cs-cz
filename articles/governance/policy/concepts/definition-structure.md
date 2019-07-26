---
title: Podrobnosti struktury definice zásad
description: Popisuje, jak je používat prostředku definice zásady Azure Policy stanovit vytváření názvů pro prostředky ve vaší organizaci zadáním popisu vašeho nového, když je tato zásada vynucená a jaký vliv má provést.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 77bf284734428e9257b46d85296796e4051ace26
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494825"
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

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Režim

**Režim** je nakonfigurován v závislosti na tom, zda je zásada cílena na vlastnost Azure Resource Manager nebo vlastnost poskytovatele prostředků.

### <a name="resource-manager-modes"></a>Správce prostředků režimy

**Režimu** Určuje, jaké typy prostředků, se vyhodnotí zásady. Jsou podporované režimy:

- `all`: vyhodnocení skupiny prostředků a všechny typy prostředků
- `indexed`: jenom vyhodnotit typy prostředků, které podporují značky a umístění

Doporučujeme vám, že jste nastavili **režimu** k `all` ve většině případů. Všechny definice zásad, které jsou vytvořené pomocí portálu `all` režimu. Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure, můžete zadat **režimu** parametr ručně. Pokud neobsahuje definici zásady **režimu** hodnota, použije se výchozí `all` v prostředí Azure PowerShell a o `null` v Azure CLI. A `null` režim je stejný jako při použití `indexed` pro podporu zpětné kompatibility.

`indexed` by měla sloužit při vytváření zásad, které vynucují značky nebo umístění. I když to není nutné, zabrání prostředkům, které nepodporují značky a umístění, z hlediska výsledků dodržování předpisů v nedodržení předpisů. Výjimkou je **skupiny prostředků**. Zásady, které vynucují místa nebo značky na skupinu prostředků, nastavte **režimu** k `all` a konkrétně cíl `Microsoft.Resources/subscriptions/resourceGroups` typu. Příklad najdete v tématu [vynutit značky skupiny prostředků](../samples/enforce-tag-rg.md). Seznam prostředků, které podporují značky, najdete v tématu [Podpora značek pro prostředky Azure](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Režimy poskytovatele prostředků

V současné době je `Microsoft.ContainerService.Data` podporovaný jenom režim poskytovatele prostředků pro správu pravidel pro přístup k řadičům přístupu ke [službě Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy pro Kubernetes](rego-for-aks.md) jsou v Public Preview a podporují jenom předdefinované definice zásad.

## <a name="parameters"></a>Parametry

Parametry pomáhají zjednodušit správu zásad snížením počtu definic zásad. Představte si, že parametrů, jako je pole ve formuláři – `name`, `address`, `city`, `state`. Tyto parametry vždy stejné, ale změnit jejich hodnoty podle jednotlivých vyplňování formuláře.
Parametry fungovat stejným způsobem jako při vytváření zásad. Včetně parametrů v definici zásad, můžete využít této zásadě pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Parametry mohou být přidány do existující a přiřazené definice. Nový parametr musí zahrnovat vlastnost **DefaultValue** . To zabrání existující přiřazení zásady nebo iniciativa nepřímo prováděné neplatný.

### <a name="parameter-properties"></a>Vlastnosti parametru

Parametr má následující vlastnosti, které se používají v definici zásady:

- **název**: Název parametru. Používá se `parameters` funkcí nasazení v rámci pravidla zásad. Další informace najdete v tématu [použití hodnoty parametru](#using-a-parameter-value).
- `type`: Určuje, zda je parametr typu **řetězec**, **pole**, **objekt**, **Boolean**, **Integer**, **float**nebo **DateTime**.
- `metadata`: Definuje podvlastnost primárně používané Azure Portal k zobrazení uživatelsky přívětivých informací:
  - `description`: Vysvětlení, k čemu je použit parametr. Dá se použít k zadání příkladů přijatelných hodnot.
  - `displayName`: Popisný název zobrazený na portálu pro parametr
  - `strongType`: Volitelné Používá se při přiřazování definice zásady prostřednictvím portálu. Poskytuje seznam podporující kontext. Další informace najdete v tématu [strongType](#strongtype).
  - `assignPermissions`: Volitelné Nastavte na _hodnotu true_ , pokud chcete, Azure Portal během přiřazování zásad vytvářet přiřazení rolí. Tato vlastnost je užitečná v případě, že chcete přiřadit oprávnění mimo rozsah přiřazení. Podle definice role v zásadě (nebo definice role v rámci všech zásad v iniciativě) existuje jedno přiřazení role. Hodnota parametru musí být platným prostředkem nebo oborem.
- `defaultValue`: Volitelné Nastaví hodnotu parametru v přiřazení, pokud není zadána žádná hodnota.
  Vyžaduje se při aktualizaci existující definice zásady, která je přiřazená.
- `allowedValues`: Volitelné Poskytuje pole hodnot, které parametr akceptuje během přiřazování.

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

V pravidlu zásad můžete odkazovat na parametry u následujících `parameters` syntaxe funkce hodnotu nasazení:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Tento příklad odkazuje na parametr **allowedLocations** , který byl prokázán ve [vlastnostech parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

V rámci vlastnosti můžete použít strongType k poskytnutí seznamu možností s vícenásobným výběrem v rámci Azure Portal.  `metadata` Povolené hodnoty pro **strongType** aktuálně zahrnují:

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

Při vytváření iniciativy nebo zásady, je nutné zadat umístění definice. Umístění definice musí být skupinu pro správu nebo předplatného. Toto umístění Určuje obor, ke které je možné přiřadit iniciativy nebo zásady. Prostředky musí být přímými členy nebo podřízené prvky v rámci hierarchie umístění definice cílit na přiřazení.

Pokud je umístění definice:

- **Předplatné** – pouze pro prostředky v rámci tohoto předplatného je možné přiřadit zásady.
- **Skupina pro správu** – pouze pro prostředky v rámci podřízené skupiny pro správu a podřízené předplatná je možné přiřadit zásady. Pokud budete chtít použít definici zásady pro několik předplatných, musí být umístění skupiny pro správu, který obsahuje těchto předplatných.

## <a name="display-name-and-description"></a>Zobrazovaný název a popis

Použijete **displayName** a **popis** identifikovat definice zásad a zadání při použití kontextu. hodnota **DisplayName** má maximální délku _128_ znaků a **popis** nesmí být delší než _512_ znaků.

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

Podmínka vyhodnocuje, zda **pole** nebo **hodnota** přistupující objekty splňují určitá kritéria. Jsou podporované podmínky:

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

Při použití **jako** a **notLike** podmínky, zadat zástupný znak `*` v hodnotě.
Hodnota by neměla mít více než jeden zástupný znak `*`.

Při použití podmínek **Match** a **notMatch** zadejte `#` , aby odpovídaly číslici `?` , pro písmeno, `.` aby odpovídaly jakémukoli znaku a jakémukoliv jinému znaku, aby odpovídaly tomuto skutečnému znaku.
Při **porovnávání** a **notMatch** se rozlišují velká a malá písmena. Alternativy nerozlišující velká a malá písmena jsou k dispozici v **matchInsensitively** a **notMatchInsensitively**. Příklady najdete v tématu [povolit několik vzory názvů](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fields (Pole)

Podmínky jsou vytvářena pomocí polí. Pole odpovídá vlastnosti datové části požadavku prostředků a popisuje stav prostředku.

Podporovány jsou následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku. Úplný název prostředku je název prostředku předcházený žádné nadřazené názvy prostředků (například "myServer/databáze").
- `kind`
- `type`
- `location`
  - Pro prostředky, které jsou nezávislá umístění, použijte **globální** . Příklad najdete v tématu [s povolenými umístěními Samples](../samples/allowed-locations.md).
- `identity.type`
  - Vrátí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) povolené v prostředku.
- `tags`
- `tags['<tagName>']`
  - Tato syntaxe závorky podporuje názvy značek, které mají interpunkční znaménka, jako je například spojovník, tečka nebo mezera.
  - Kde **\<tagName\>** je název značky ověřit podmínku.
  - Příklady: `tags['Acct.CostCenter']` kde **Acct. CostCenter** je název značky.
- `tags['''<tagName>''']`
  - Tato syntaxe závorky podporuje názvy značek, které mají apostrofy, pomocí uvozovacích znaků s dvojitými apostrofy.
  - Kde **'\<TagName\>'** je název značky, pro kterou má být podmínka ověřena.
  - Příklad: `tags['''My.Apostrophe.Tag''']` kde **'\<TagName\>'** je název značky.
- Vlastnost aliasy – seznam najdete v tématu [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` a`tags[tag.with.dots]` jsou stále přijatelné způsoby deklarace pole značek. Preferované výrazy jsou však uvedeny výše.

#### <a name="use-tags-with-parameters"></a>Použití značek s parametry

Hodnota parametru může být předána poli značky. Předání parametru do pole značek zvyšuje flexibilitu definice zásad během přiřazení zásady.

V následujícím příkladu `concat` se používá k vytvoření vyhledávání pole značek pro značku s názvem hodnota parametru **TagName** . Pokud tato značka neexistuje, použije se  k přidání značky s použitím hodnoty stejné pojmenované sady značek v nadřazené skupině prostředků auditované prostředky pomocí `resourcegroup()` funkce Lookup efekt připojit.

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

### <a name="value"></a>Value

Podmínky mohou být také vytvořeny pomocí **hodnoty**. **hodnota** kontroluje podmínky proti [parametrům](#parameters), [podporované funkce šablon](#policy-functions)nebo literály.
**hodnota** je spárována s libovolnou podporovanou [podmínkou](#conditions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Další informace najdete v tématu [předcházení chybám šablon](#avoiding-template-failures).

#### <a name="value-examples"></a>Příklady hodnot

Tato ukázka pravidla zásad používá **hodnotu** `resourceGroup()` pro porovnání výsledku funkce `*netrg`a vlastnosti vráceného **názvu** s podmínkou **Like** . Pravidlo odepře jakýkoliv prostředek, který `Microsoft.Network/*` není **typu** v žádné skupině prostředků, jejíž název končí na. `*netrg`

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

Tato ukázka pravidla zásad používá **hodnotu** ke kontrole, jestli se výsledek více vnořených  `true`funkcí rovná. Pravidlo odmítne všechny prostředky, které nemají alespoň tři značky.

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

Použití šablonových _funkcí_ v **hodnotě** umožňuje mnoho komplexních vnořených funkcí. Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Příklad **hodnoty** , která se v některých scénářích nezdařila:

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

Vzorové pravidlo výše uvedené používá [podřetězec ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) pro porovnání prvních tří znaků **názvu** s **ABC**. Pokud je **název** kratší než tři znaky, `substring()` výsledkem funkce je chyba. Tato chyba způsobí, že se zásada stane  účinkem odepření.

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

S revidovaným pravidlem zásad `if()` před tím, než se `substring()` pokusíte získat hodnotu s méně než třemi znaky, zkontroluje délku **názvu** . Pokud je **název** příliš krátký, je místo toho vrácena hodnota "nezačíná na ABC" a porovnána s **ABC**. Prostředek s krátkým názvem, který nezačíná na **ABC** , se stále neúspěšně stane pravidlem zásad, ale během vyhodnocování se nestane příčinou chyby.

### <a name="effect"></a>Efekt

Azure Policy podporuje následující typy účinku:

- **Odepřít**: vygeneruje událost v protokolu aktivit a požadavek selže
- **Audit**: vygeneruje událost upozornění v protokolu aktivit, ale neselže, je požadavek
- **Připojit**: Přidá definovanou sadu polí k této žádosti
- **AuditIfNotExists**: povolí auditování Pokud prostředek neexistuje.
- **DeployIfNotExists**: nasadí prostředek, pokud už neexistuje.
- **Zakázané**: nevyhodnocuje prostředky pro pravidlo zásad dodržování předpisů
- **EnforceRegoPolicy**: konfiguruje řadič otevřeného přístupu agenta zásad ve službě Azure Kubernetes (Preview).

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

Podrobné informace o každém z efektů, pořadí vyhodnocení, vlastností a příkladů najdete v tématu [principy Azure Policych efektů](effects.md).

### <a name="policy-functions"></a>Funkce zásad

Pro použití v rámci pravidla zásad jsou k dispozici všechny [funkce šablon Správce prostředků](../../../azure-resource-manager/resource-group-template-functions.md) , s výjimkou následujících funkcí a uživatelsky definovaných funkcí:

- copyIndex()
- deployment()
- seznamu
- newGuid()
- pickZones()
- Zprostředkovatelé ()
- Reference ()
- resourceId()
- proměnné ()

Následující funkce jsou k dispozici pro použití v pravidle zásad, ale liší se od použití v šabloně Azure Resource Manager:

- addDays (dateTime; numberOfDaysToAdd)
  - **DateTime**: [required] řetězec řetězce ve formátu Universal ISO 8601 DateTime "rrrr-MM-DDThh: mm: ss. fffffffZ"
  - **numberOfDaysToAdd**: [povinné] celé číslo-počet dnů k přidání
- utcNow () – na rozdíl od šablony Správce prostředků lze použít mimo defaultValue.
  - Vrátí řetězec, který je nastaven na aktuální datum a čas ve formátu Universal ISO 8601 DateTime yyyy-MM-ddTHH: mm: ss. fffffffZ.

Kromě toho `field` funkce je k dispozici pro pravidla zásad. `field` se používá především s **AuditIfNotExists** a **DeployIfNotExists** na odkaz na pole v prostředku, které jsou právě vyhodnocována. Příklad použití si můžete prohlédnout ve [DeployIfNotExists příklad](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Příklad funkce zásad

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

## <a name="aliases"></a>Aliasy

Vlastnost aliasy používáte pro přístup k určité vlastnosti pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povolena pro vlastnost prostředku. Každý alias namapuje cesty v různých verzích rozhraní API pro typ daný prostředek. Modul zásad během hodnocení zásad, získá vlastnost cesty pro tuto verzi rozhraní API.

Seznam aliasů se pořád rozrůstá. Pokud chcete zjistit, jaké aliasy jsou aktuálně podporovány službou Azure Policy, použijte jednu z následujících metod:

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

- Rozhraní REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Principy alias [*]

Některé aliasy, které jsou k dispozici máte verzi, která se zobrazí jako 'normal' název, který má **[\*]** k němu připojená. Příklad:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias ' Normal ' představuje pole jako jedinou hodnotu. Toto pole je určeno pro přesné scénáře porovnání shody, pokud celá sada hodnot musí být přesně definovaná, a ne více a méně.

Alias **[\*]** umožňuje porovnání s hodnotou každého prvku v poli a konkrétní vlastnosti každého prvku. Tento přístup umožňuje porovnat vlastnosti elementu pro ' Pokud žádný z ', ', pokud je to u všech scénářů ', ', ', '. Pomocí **ipRules [\*]** je třeba ověřit, že každá _Akce_ je zamítnutá , ale nedělejte si starosti, kolik pravidel existuje nebo jaká je _hodnota_ IP. Toto ukázkové pravidlo vyhledá všechny shody **ipRules [\*]. Value** to **10.0.4.1** a použije **effectType** pouze v případě, že nenajde alespoň jednu shodu:

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

Další informace najdete v tématu [vyhodnocení aliasu\*[]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

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

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).