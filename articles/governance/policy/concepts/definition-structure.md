---
title: Podrobnosti struktury definice zásad
description: Popisuje způsob, jakým se používají definice zásad k navázání konvencí pro prostředky Azure ve vaší organizaci.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 329692c7220810af1d47fe7036e727bb49284810
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117280"
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Azure Policy stanoví konvence pro prostředky. Definice zásad popisují [podmínky](#conditions) dodržování předpisů prostředků a efekt, který se má provést, pokud je splněna podmínka. Podmínka porovnává [pole](#fields) vlastností prostředku s požadovanou hodnotou. K polím vlastností prostředku se dostanete pomocí [aliasů](#aliases). Pole vlastností prostředku je buď pole s jednou hodnotou, nebo [pole](#understanding-the--alias) s více hodnotami. Vyhodnocení podmínky se liší u polí.
Přečtěte si další informace o [podmínkách](#conditions).

Definováním konvencí můžete řídit náklady a snadněji spravovat prostředky. Můžete například určit, že jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby všechny prostředky měly konkrétní značku. Zásady se dědí ze všech podřízených prostředků. Pokud se zásada použije pro skupinu prostředků, vztahuje se na všechny prostředky v této skupině prostředků.

Schéma definice zásad najdete tady:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

K vytvoření definice zásady použijete JSON. Definice zásad obsahuje prvky pro:

- režim
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

## <a name="mode"></a>Mode

**Režim** je nakonfigurován v závislosti na tom, zda je zásada cílena na vlastnost Azure Resource Manager nebo vlastnost poskytovatele prostředků.

### <a name="resource-manager-modes"></a>Správce prostředků režimy

**Režim** určuje, které typy prostředků se budou vyhodnocovat pro zásady. Podporované režimy:

- `all`: vyhodnotit skupiny prostředků a všechny typy prostředků
- `indexed`: vyhodnotit jenom typy prostředků, které podporují značky a umístění.

Například prostředek `Microsoft.Network/routeTables` podporuje značky a umístění a je vyhodnocován v obou režimech. Prostředek `Microsoft.Network/routeTables/routes` ale nemůže být tagovaný a není vyhodnocený `Indexed` v režimu.

Ve většině případů doporučujeme nastavit **režim** na `all` . Všechny definice zásad vytvořené prostřednictvím portálu používají `all` režim. Pokud používáte PowerShell nebo Azure CLI, můžete zadat parametr **Mode** ručně. Pokud definice zásady neobsahuje hodnotu **režimu** , použije se výchozí hodnota `all` v Azure PowerShell a `null` v rozhraní příkazového řádku Azure CLI. `null` Režim je stejný jako při použití `indexed` k podpoře zpětné kompatibility.

`indexed`by měla být použita při vytváření zásad, které vysazují značky nebo umístění. I když to není nutné, zabrání prostředkům, které nepodporují značky a umístění, z hlediska výsledků dodržování předpisů v nedodržení předpisů. Výjimkou jsou **skupiny prostředků**. Zásady, které vynutily umístění nebo značky ve skupině prostředků **mode** , by `all` měly nastavit režim na `Microsoft.Resources/subscriptions/resourceGroups` a konkrétně cílit na typ. Příklad najdete v tématu [vymáhání značek skupin prostředků](../samples/enforce-tag-rg.md). Seznam prostředků, které podporují značky, najdete v tématu [Podpora značek pro prostředky Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Režimy poskytovatele prostředků (Preview)

V současné době jsou podporovány následující režimy poskytovatele prostředků ve verzi Preview:

- `Microsoft.ContainerService.Data`pro správu pravidel kontroleru přístupu pro [službu Azure Kubernetes](../../../aks/intro-kubernetes.md). Zásady, které používají tento režim poskytovatele prostředků, **musí** používat [EnforceRegoPolicy](./effects.md#enforceregopolicy) efekt.
- `Microsoft.Kubernetes.Data`pro správu samoobslužných clusterů AKS Engine Kubernetes v Azure.
  Zásady, které používají tento režim poskytovatele prostředků, **musí** používat [EnforceOPAConstraint](./effects.md#enforceopaconstraint) efekt.
- `Microsoft.KeyVault.Data`pro správu trezorů a certifikátů v [Azure Key Vault](../../../key-vault/general/overview.md).

> [!NOTE]
> Režimy poskytovatele prostředků podporují jenom integrované definice zásad a nepodporují iniciativy ve verzi Preview.

## <a name="parameters"></a>Parametry

Parametry vám pomůžou zjednodušit správu zásad tím, že se sníží počet definic zásad. Představte si parametry jako pole ve formuláři – `name`, `address`, `city`, `state`. Tyto parametry vždy zůstávají stejné, ale jejich hodnoty se změní na základě jednotlivých vyplňování formuláře.
Parametry fungují stejným způsobem při vytváření zásad. Zahrnutím parametrů do definice zásady můžete tuto zásadu použít pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Parametry mohou být přidány do existující a přiřazené definice. Nový parametr musí zahrnovat vlastnost **DefaultValue** . To brání tomu, aby se stávající přiřazení zásady nebo iniciativy od nepřímo neplatně vytvořila.

### <a name="parameter-properties"></a>Vlastnosti parametru

Parametr má následující vlastnosti, které se používají v definici zásady:

- **Name (název**): název parametru. Používá se `parameters` funkcí nasazení v rámci pravidla zásad. Další informace najdete v tématu [použití hodnoty parametru](#using-a-parameter-value).
- `type`: Určuje, zda je parametr typu **řetězec**, **pole**, **objekt**, **Boolean**, **Integer**, **float**nebo **DateTime**.
- `metadata`: Definuje podvlastnost primárně používané Azure Portal k zobrazení uživatelsky přívětivých informací:
  - `description`: Vysvětlení použití parametru pro. Dá se použít k zadání příkladů přijatelných hodnot.
  - `displayName`: Popisný název zobrazený na portálu pro parametr.
  - `version`: (Volitelné) sleduje podrobnosti o verzi obsahu definice zásady.

    > [!NOTE]
    > Služba Azure Policy používá `version`vlastnosti, `preview`a `deprecated` k vyjádření úrovně změny předdefinované definice nebo iniciativy a stavu zásad. Formát `version` je: `{Major}.{Minor}.{Patch}`. Konkrétní stavy, například _zastaralé_ nebo ve _verzi Preview_, jsou připojeny k `version` vlastnosti nebo v jiné vlastnosti jako **logická hodnota**.

  - `category`: (Volitelné) určuje, v jaké kategorii v Azure Portal se tato definice zásady zobrazuje.
  - `strongType`: (Volitelné) používá se při přiřazování definice zásady prostřednictvím portálu. Poskytuje seznam podporující kontext. Další informace najdete v tématu [strongType](#strongtype).
  - `assignPermissions`: (Volitelné) nastavte na _hodnotu true_ , pokud chcete, Azure Portal vytvořit přiřazení rolí během přiřazování zásad. Tato vlastnost je užitečná v případě, že chcete přiřadit oprávnění mimo rozsah přiřazení. Podle definice role v zásadě (nebo definice role v rámci všech zásad v iniciativě) existuje jedno přiřazení role. Hodnota parametru musí být platným prostředkem nebo oborem.
- `defaultValue`: (Volitelné) nastaví hodnotu parametru v přiřazení, pokud není zadána žádná hodnota.
  Vyžaduje se při aktualizaci existující definice zásady, která je přiřazená.
- `allowedValues`: (Volitelné) poskytuje pole hodnot, které parametr akceptuje během přiřazování.

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

V pravidle zásad můžete odkazovat na parametry pomocí následující `parameters` syntaxe funkce:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Tento příklad odkazuje na parametr **allowedLocations** , který byl prokázán ve [vlastnostech parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

V rámci `metadata` vlastnosti můžete použít **strongType** k poskytnutí seznamu možností s vícenásobným výběrem v rámci Azure Portal. **strongType** může být podporovaný _typ prostředku_ nebo povolená hodnota. Chcete-li zjistit, zda je _typ prostředku_ platný pro **strongType**, použijte [příkaz Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Některé _typy prostředků_ , které nejsou vraceny **Get-AzResourceProvider** , jsou podporovány. Jsou to tyto:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Povolené hodnoty pro **strongType** _typu non prostředku_ jsou:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Umístění definice

Při vytváření iniciativy nebo zásad je nutné zadat umístění definice. Umístění definice musí být skupina pro správu nebo předplatné. Toto umístění určuje obor, do kterého lze iniciativu nebo zásadu přiřadit. Prostředky musí být přímé členy nebo podřízené položky v rámci hierarchie umístění definice na cíl pro přiřazení.

Pokud je umístění definice:

- Zásadu můžou přiřadit jenom prostředky v rámci daného **předplatného** .
- Zásadu můžou přiřadit jenom prostředky v rámci podřízených skupin **pro správu a** podřízených předplatných. Pokud plánujete použít definici zásady pro několik předplatných, umístění musí být skupina pro správu, která obsahuje tyto odběry.

## <a name="display-name-and-description"></a>Zobrazované jméno a popis

K identifikaci definice zásad a zadání kontextu, kdy se má použít, můžete použít **DisplayName** a **Description** . hodnota **DisplayName** má maximální délku _128_ znaků a **popis** nesmí být delší než _512_ znaků.

> [!NOTE]
> Během vytváření nebo aktualizace definice zásady, **ID**, **typu**a **název** jsou definovány vlastnostmi, které jsou externí pro JSON a nejsou nezbytné v souboru JSON. Načtení definice zásady prostřednictvím sady SDK vrátí vlastnosti **ID**, **typu**a **názvu** jako součást formátu JSON, ale každá z nich je informace jen pro čtení, které se týkají definice zásady.

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

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

Pro **méně**, **lessOrEquals**, **větší**a **greaterOrEquals**, pokud typ vlastnosti neodpovídá typu podmínky, je vyvolána chyba. Porovnávání řetězců je provedeno pomocí `InvariantCultureIgnoreCase`.

Při použití podmínek **Like** a **notLike** zadáte v hodnotě zástupný znak `*` .
Hodnota by neměla mít více než jeden zástupný `*`znak.

Při použití podmínek **Match** a **notMatch** zadejte `#` , aby odpovídaly číslici, `?` pro písmeno, `.` aby odpovídaly jakémukoli znaku a jakémukoliv jinému znaku, aby odpovídaly tomuto skutečnému znaku. Při **porovnávání** a **notMatch** se rozlišují velká a malá písmena. všechny ostatní podmínky, které vyhodnocují _StringValue_ , rozlišují malá a velká písmena. Alternativy nerozlišující velká a malá písmena jsou k dispozici v **matchInsensitively** a **notMatchInsensitively**.

V hodnotě pole **and** ** \] alias pole je každý prvek v poli vyhodnocen individuálně pomocí logických prvků a mezi \[ \* ** prvky. Další informace najdete v tématu [vyhodnocení \[ \* \] aliasu](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Pole

Podmínky jsou tvořeny pomocí polí. Pole odpovídá vlastnostem v datové části požadavku prostředku a popisuje stav prostředku.

Podporují se následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku. Úplný název prostředku je název prostředku, který předchází všechny názvy nadřazených prostředků (například "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Pro prostředky, které jsou nezávislá umístění, použijte **globální** .
- `identity.type`
  - Vrátí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) povolené v prostředku.
- `tags`
- `tags['<tagName>']`
  - Tato syntaxe závorky podporuje názvy značek, které mají interpunkční znaménka, jako je například spojovník, tečka nebo mezera.
  - Kde ** \<TagName\> ** je název značky, pro kterou má být podmínka ověřena.
  - Příklady: `tags['Acct.CostCenter']` kde **Acct. CostCenter** je název značky.
- `tags['''<tagName>''']`
  - Tato syntaxe závorky podporuje názvy značek, které mají apostrofy, pomocí uvozovacích znaků s dvojitými apostrofy.
  - Kde **'\<TagName\>'** je název značky, pro kterou má být podmínka ověřena.
  - Příklad: `tags['''My.Apostrophe.Tag''']` kde **' My. apostrof. tag '** je název značky.
- aliasy vlastností – pro seznam najdete v tématu [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`a `tags[tag.with.dots]` jsou stále přijatelné způsoby deklarace pole značek. Preferované výrazy jsou však uvedeny výše.

#### <a name="use-tags-with-parameters"></a>Použití značek s parametry

Hodnota parametru může být předána poli značky. Předání parametru do pole značek zvyšuje flexibilitu definice zásad během přiřazení zásady.

V následujícím příkladu `concat` se používá k vytvoření vyhledávání pole značek pro značku s názvem hodnota parametru **TagName** . Pokud tato značka neexistuje, použije se **modify** k přidání značky pomocí hodnoty stejné pojmenované sady značek v nadřazené skupině prostředků auditované prostředky pomocí funkce `resourcegroup()` Lookup efekt změny.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Hodnota

Podmínky mohou být také vytvořeny pomocí **hodnoty**. **hodnota** kontroluje podmínky proti [parametrům](#parameters), [podporované funkce šablon](#policy-functions)nebo literály.
**hodnota** je spárována s libovolnou podporovanou [podmínkou](#conditions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Další informace najdete v tématu [předcházení chybám šablon](#avoiding-template-failures). Použijte [enforcementMode](./assignment-structure.md#enforcement-mode) z **DoNotEnforce** , abyste zabránili dopadu neúspěšného vyhodnocení nových nebo aktualizovaných prostředků při testování a ověřování nové definice zásady.

#### <a name="value-examples"></a>Příklady hodnot

Tato ukázka pravidla zásad používá **hodnotu** pro `resourceGroup()` porovnání výsledku funkce a vlastnosti vráceného **názvu** s podmínkou like. **like** `*netrg` Pravidlo odepře jakýkoliv prostředek, který `Microsoft.Network/*` není **typu** v žádné skupině prostředků, jejíž název končí na. `*netrg`

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

Tato ukázka pravidla zásad používá **hodnotu** ke kontrole, jestli se výsledek více vnořených funkcí **rovná** `true`. Pravidlo odmítne všechny prostředky, které nemají alespoň tři značky.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
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

Vzorové pravidlo výše uvedené používá [podřetězec ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) pro porovnání prvních tří znaků **názvu** s **ABC**. Pokud je **název** kratší než tři znaky, výsledkem `substring()` funkce je chyba. Tato chyba způsobí, že se zásada stane účinkem **odepření** .

Místo toho použijte funkci [if ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) ke kontrole, zda první tři znaky **názvu** odpovídají **ABC** bez povolení **názvu** kratšího než tři znaky, aby došlo k chybě:

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

S revidovaným pravidlem zásad `if()` před tím, než se pokusíte získat `substring()` hodnotu s méně než třemi znaky, zkontroluje délku **názvu** . Pokud je **název** příliš krátký, je místo toho vrácena hodnota "nezačíná na ABC" a porovnána s **ABC**. Prostředek s krátkým názvem, který nezačíná na **ABC** , se stále neúspěšně stane pravidlem zásad, ale během vyhodnocování se nestane příčinou chyby.

### <a name="count"></a>Počet

Podmínky, které počítají, kolik členů pole v datové části prostředků, které odpovídají výrazu podmínky, mohou být tvořeny pomocí výrazu **Count** . Běžné scénáře kontrolují, jestli alespoň jedno z ', ' přesně jedno z ', ' vše z ' nebo ' žádné z ', které členové pole splní. funkce **Count** vyhodnocuje každého [ \[ \* \] člena pole aliasu](#understanding-the--alias) pro výraz podmínky a sečte _skutečný_ výsledek, který je pak porovnán s operátorem výrazu. Výrazy **Count** můžou být do jedné definice **policyRule** přidané až třikrát.

Struktura výrazu **Count** je:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Pro **počet**se používají tyto vlastnosti:

- **Count. Field** (Required): obsahuje cestu k poli a musí se jednat o alias pole. Pokud pole chybí, je výraz vyhodnocen jako _nepravdivý_ bez zvážení výrazu podmínky.
- **Count. Where** (volitelné): výraz podmínky, který má individuálně vyhodnotit každého [ \[ \* \] člena pole aliasu](#understanding-the--alias) **Count. Field**. Pokud tato vlastnost není zadána, jsou všechny členy pole s cestou pole vyhodnoceny na _hodnotu true_. V této vlastnosti lze použít jakoukoli [podmínku](../concepts/definition-structure.md#conditions) .
  [Logické operátory](#logical-operators) lze použít uvnitř této vlastnosti k vytvoření složitých požadavků na vyhodnocení.
- podmínka (povinné): hodnota je porovnána s počtem položek, které splnily výraz **Count. Where** podmínky. **\> \<** Měla by se použít číselná [Podmínka](../concepts/definition-structure.md#conditions) .

#### <a name="count-examples"></a>Příklady počtu

Příklad 1: zjištění, zda je pole prázdné

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Příklad 2: zkontrolování pouze jednoho člena pole pro splnění výrazu podmínky

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Příklad 3: Vyhledání alespoň jednoho člena pole pro splnění výrazu podmínky

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Příklad 4: Ověřte, že všechny členy pole objektů splňují výraz podmínky.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Příklad 5: Ověřte, že všechny členy pole řetězců splňují výraz podmínky.

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Příklad 6: použití **pole** uvnitř **hodnoty** ke kontrole, že všichni členové pole splňují výraz podmínky

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Příklad 7: Ověřte, že aspoň jeden člen pole odpovídá více vlastnostem ve výrazu podmínky.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Účinek

Azure Policy podporuje následující typy účinku:

- **Připojit**: přidá do žádosti definovanou sadu polí.
- **Audit**: vygeneruje událost upozornění v protokolu aktivit, ale požadavek neselže.
- **AuditIfNotExists**: vygeneruje událost upozornění v protokolu aktivit, pokud neexistuje související prostředek.
- **Deny**: vygeneruje událost v protokolu aktivit a neuspěje požadavek.
- **DeployIfNotExists**: nasadí související prostředek, pokud ještě neexistuje.
- **Zakázáno**: nevyhodnotí prostředky pro dodržování předpisů pro pravidlo zásad.
- **EnforceOPAConstraint** (Preview): konfiguruje Open Controller agent admissioning Controller s gatekeeper v3 pro samoobslužně spravované clustery Kubernetes v Azure (Preview).
- **EnforceRegoPolicy** (Preview): konfiguruje Open Controller agent admissioning Controller s gatekeeper v2 ve službě Azure Kubernetes.
- **Upravit**: Přidání, aktualizace nebo odebrání definovaných značek z prostředku

Podrobné informace o každém z efektů, pořadí vyhodnocení, vlastností a příkladů najdete v tématu [principy Azure Policych efektů](effects.md).

### <a name="policy-functions"></a>Funkce zásad

Pro použití v rámci pravidla zásad jsou k dispozici všechny [funkce šablon Správce prostředků](../../../azure-resource-manager/templates/template-functions.md) , s výjimkou následujících funkcí a uživatelsky definovaných funkcí:

- copyIndex()
- nasazení ()
- seznamu
- newGuid()
- pickZones()
- Zprostředkovatelé ()
- Reference ()
- resourceId ()
- proměnné ()

> [!NOTE]
> Tyto funkce jsou stále k dispozici `details.deployment.properties.template` v rámci nasazení šablony v definici zásady **deployIfNotExists** .

Tato funkce je k dispozici pro použití v pravidle zásad, ale liší se od použití v šabloně Azure Resource Manager:

- `utcNow()`Na rozdíl od šablony Správce prostředků lze použít mimo defaultValue.
  - Vrátí řetězec, který je nastaven na aktuální datum a čas ve formátu Universal ISO 8601 DateTime yyyy-MM-ddTHH: mm: ss. fffffffZ.

Následující funkce jsou dostupné jenom v pravidlech zásad:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [required] řetězec řetězce ve formátu Universal ISO 8601 DateTime "rrrr-MM-DDThh: mm: ss. fffffffZ"
  - **numberOfDaysToAdd**: [povinné] celé číslo-počet dnů k přidání
- `field(fieldName)`
  - **FieldName**: [required] řetězec-název [pole](#fields) , které se má načíst
  - Vrátí hodnotu tohoto pole z prostředku, který je vyhodnocován podmínkou if.
  - `field`se primárně používá s **AuditIfNotExists** a **DeployIfNotExists** pro odkazování na pole v prostředku, který je vyhodnocován. Příklad tohoto použití lze zobrazit v [DeployIfNotExists příkladu](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Vrátí verzi rozhraní API žádosti, která aktivovala vyhodnocování zásad (například: `2019-09-01`).
    Toto bude verze rozhraní API, která se použila v požadavku PUT/PATCH k vyhodnocení při vytváření nebo aktualizaci prostředku. Nejnovější verze rozhraní API se vždycky používá během hodnocení dodržování předpisů u stávajících prostředků.
  
#### <a name="policy-function-example"></a>Příklad funkce zásad

Tato ukázka pravidla zásad používá funkci `resourceGroup` prostředků k získání vlastnosti **názvu** v kombinaci s funkcí `concat` Array a Object k sestavení `like` podmínky, která vynutila název prostředku, aby začínal názvem skupiny prostředků.

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

- Azure Policy rozšíření pro Visual Studio Code (doporučeno)

  Pomocí [rozšíření Azure Policy pro Visual Studio Code](../how-to/extension-for-vscode.md) můžete zobrazit a vyhledat aliasy pro vlastnosti prostředku.

  ![Azure Policy rozšíření pro Visual Studio Code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Použijte `project` operátor k zobrazení **aliasu** prostředku.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

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

Několik dostupných aliasů má verzi, která se zobrazí jako název Normal (normální) a další, která je ** \[ \* ** k ní připojená. Příklad:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias ' Normal ' představuje pole jako jedinou hodnotu. Toto pole je určeno pro přesné scénáře porovnání shody, pokud celá sada hodnot musí být přesně definovaná, a ne více a méně.

** \[ Alias umožňuje porovnání s hodnotou každého prvku v poli a konkrétní vlastnosti \* ** každého prvku. Tento přístup umožňuje porovnat vlastnosti elementu pro ' Pokud žádný z ', ', pokud je to u všech scénářů ', ', ', '. U složitějších scénářů použijte výraz podmínky [Count](#count) . Pomocí **ipRules\[\*** může příklad ověřit, že každá _Akce_ je _Zamítnutá_, ale nemusí se obávat, kolik pravidel existuje nebo jaká je _hodnota_ IP.
Toto ukázkové pravidlo vyhledá všechny shody **hodnoty ipRules\[\*\]. Value** to **10.0.4.1** a použije **effectType** pouze v případě, že nenajde alespoň jednu shodu:

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

Iniciativy umožňují seskupit několik souvisejících definic zásad, které zjednodušují přiřazování a správu, protože pracujete se skupinou jako s jednou položkou. Můžete například seskupit související definice zásad označování do jedné iniciativy. Místo toho, abyste každou zásadu přiřadíte jednotlivě, můžete použít iniciativu.

> [!NOTE]
> Po přiřazení iniciativy se parametry úrovně iniciativy nedají změnit. Z tohoto důvodu doporučujeme, abyste při definování parametru nastavili hodnotu **DefaultValue** .

Následující příklad ukazuje, jak vytvořit iniciativu pro zpracování dvou značek: `costCenter` a. `productName` Pomocí dvou předdefinovaných zásad použije výchozí hodnotu značky.

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
