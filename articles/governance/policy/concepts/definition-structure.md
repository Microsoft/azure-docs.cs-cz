---
title: Podrobnosti o struktuře definice politiky
description: Popisuje, jak se definice zásad používají k vytváření konvencí pro prostředky Azure ve vaší organizaci.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 0a7c4e05270ff242fa97b253b27a5de92895368a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461000"
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Zásady Azure vytváří konvence pro prostředky. Definice zásad popisují [podmínky](#conditions) dodržování předpisů o zdrojích a efekt, který má trvat, pokud je podmínka splněna. Podmínka porovná [pole](#fields) vlastnosti zdroje s požadovanou hodnotou. K polím vlastností prostředku se přistupuje pomocí [aliasů](#aliases). Pole vlastnosti zdroje je buď pole s jednou hodnotou, nebo [pole](#understanding-the--alias) s více hodnotami. Vyhodnocení stavu se liší v polích.
Další informace o [podmínkách](#conditions).

Definováním konvencí můžete řídit náklady a snadněji spravovat prostředky. Můžete například určit, že jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby všechny prostředky měly určitou značku. Zásady jsou zděděny všemi podřízenými prostředky. Pokud je zásada použita pro skupinu prostředků, je použitelná pro všechny prostředky v této skupině prostředků.

Schéma definice zásad naleznete zde:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

JSON slouží k vytvoření definice zásady. Definice zásad obsahuje prvky pro:

- režim
- parameters
- zobrazovaný název
- description
- pravidlo zásad
  - logické vyhodnocení
  - Účinek

Například následující JSON zobrazuje zásadu, která omezuje, kde jsou nasazeny prostředky:

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

Všechny ukázky zásad Azure jsou na [ukázkách zásad Azure](../samples/index.md).

## <a name="mode"></a>Mode

**Režim** je nakonfigurovaný v závislosti na tom, jestli zásada cílí na vlastnost Azure Resource Manager nebo vlastnost Zprostředkovatele prostředků.

### <a name="resource-manager-modes"></a>Režimy Správce zdrojů

**Režim** určuje, které typy prostředků budou vyhodnoceny pro zásadu. Podporované režimy jsou:

- `all`: vyhodnocení skupin prostředků a všech typů prostředků
- `indexed`: Vyhodnocujte pouze typy prostředků, které podporují značky a umístění

Prostředek například `Microsoft.Network/routeTables` podporuje značky a umístění a je vyhodnocován v obou režimech. Prostředek `Microsoft.Network/routeTables/routes` však nelze označit a není vyhodnocován v `Indexed` režimu.

Ve většině případů doporučujeme nastavit **režim.** `all` Všechny definice zásad vytvořené prostřednictvím `all` portálu používají režim. Pokud používáte PowerShell nebo Azure CLI, můžete zadat parametr **režimu** ručně. Pokud definice zásad neobsahuje hodnotu **režimu,** `all` výchozí nastavení v `null` Azure PowerShell a v Azure CLI. Režim `null` je stejný jako `indexed` použití pro podporu zpětné kompatibility.

`indexed`by měl být použit při vytváření zásad, které vynucují značky nebo umístění. I když to není nutné, zabrání prostředkům, které nepodporují značky a umístění z zobrazí jako nedodržující ve výsledcích dodržování předpisů. Výjimkou jsou **skupiny prostředků**. Zásady, které vynucují umístění `all` nebo značky `Microsoft.Resources/subscriptions/resourceGroups` ve skupině prostředků, by **měly** nastavit režim a konkrétně cílit na typ. Příklad najdete v tématu [Vynucení značek skupiny prostředků](../samples/enforce-tag-rg.md). Seznam prostředků, které podporují značky, najdete v [tématu Tag podpora pro prostředky Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Režimy zprostředkovatele prostředků (náhled)

V náhledu jsou aktuálně podporovány následující režimy zprostředkovatele prostředků:

- `Microsoft.ContainerService.Data`pro správu pravidel přijímacího řadiče ve [službě Azure Kubernetes Service](../../../aks/intro-kubernetes.md). Zásady používající tento režim zprostředkovatele prostředků **musí** používat efekt [EnforceRegoPolicy.](./effects.md#enforceregopolicy)
- `Microsoft.Kubernetes.Data`pro správu samoobslužných clusterů AKS Engine Kubernetes v Azure.
  Zásady používající tento režim zprostředkovatele prostředků **musí** používat efekt [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
- `Microsoft.KeyVault.Data`pro správu trezorů a certifikátů v [úložišti klíčů Azure](../../../key-vault/general/overview.md).

> [!NOTE]
> Režimy zprostředkovatele prostředků podporují pouze definice integrovaných zásad a nepodporují iniciativy ve verzi Preview.

## <a name="parameters"></a>Parametry

Parametry pomáhají zjednodušit správu zásad snížením počtu definic zásad. Představte si parametry, jako `name`jsou `address` `city`pole `state`ve formuláři – , , . . Tyto parametry vždy zůstávají stejné, ale jejich hodnoty se mění na základě jednotlivých vyplňování formuláře.
Parametry fungují stejným způsobem při vytváření zásad. Zahrnutím parametrů do definice zásad můžete tuto zásadu znovu použít pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Parametry mohou být přidány do existující a přiřazené definice. Nový parametr musí obsahovat vlastnost **defaultValue.** Tím zabráníte nepřímému neplatnosti existujících přiřazení zásad nebo iniciativy.

### <a name="parameter-properties"></a>Vlastnosti parametrů

Parametr má následující vlastnosti, které se používají v definici zásady:

- **název**: Název parametru. Používá se `parameters` funkce nasazení v rámci pravidla zásad. Další informace naleznete [v tématu použití hodnoty parametru](#using-a-parameter-value).
- `type`: Určuje, zda je parametrem **řetězec**, **pole**, **objekt**, **logické**číslo , **celé číslo**, **plovoucí**nebo **datetime**.
- `metadata`: Definuje podvlastnosti, které portál Azure používá k zobrazení uživatelsky přívětivých informací:
  - `description`: Vysvětlení, k čemu se parametr používá. Lze použít k poskytnutí příkladů přijatelných hodnot.
  - `displayName`: Popisný název zobrazený na portálu pro parametr.
  - `version`: (Volitelné) Sleduje podrobnosti o verzi obsahu definice zásady.

    > [!NOTE]
    > Služba Zásady `version`Azure `preview`používá `deprecated` a vlastnosti k vyjádření úrovně změny do předdefinované definice zásad nebo iniciativy a stavu. Formát `version` je: `{Major}.{Minor}.{Patch}`. Určité stavy, například _zastaralé_ nebo _náhledové_, `version` jsou připojeny k vlastnosti nebo k jiné vlastnosti jako **logická vlastnost**.

  - `category`: (Volitelné) Určuje, ve které kategorii na portálu Azure se zobrazí definice zásad.
  - `strongType`: (Volitelné) Používá se při přiřazování definice zásadprostředi. Obsahuje seznam s vědomi kontextu. Další informace naleznete v tématu [strongType](#strongtype).
  - `assignPermissions`: (Volitelné) Nastavte jako _true,_ aby portál Azure vytvářel přiřazení rolí během přiřazení zásad. Tato vlastnost je užitečná v případě, že chcete přiřadit oprávnění mimo obor přiřazení. Existuje jedno přiřazení role pro definici role v zásadě (nebo pro definici role ve všech zásadách v iniciativě). Hodnota parametru musí být platný prostředek nebo obor.
- `defaultValue`: (Volitelné) Nastaví hodnotu parametru v přiřazení, pokud není uvedena žádná hodnota.
  Povinné při aktualizaci existující definice zásady, která je přiřazena.
- `allowedValues`: (Volitelné) Poskytuje pole hodnot, které parametr přijímá během přiřazení.

Jako příklad můžete definovat definici zásad, která omezí umístění, kde lze prostředky nasadit. Parametr pro tuto definici zásad může být **povolenoLocations**. Tento parametr by byl použit každým přiřazením definice zásady k omezení přijatých hodnot. Použití **strongType** poskytuje rozšířené prostředí při dokončení přiřazení prostřednictvím portálu:

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

V pravidle zásad odkazujete na `parameters` parametry s následující syntaxí funkce:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Tato ukázka odkazuje na **parametr allowedLocations,** který byl demonstrován ve [vlastnostech parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

V `metadata` rámci vlastnosti můžete použít **strongType** poskytnout více násobný výběr seznamu možností v rámci portálu Azure. **strongType** může být podporovaný _typ prostředku_ nebo povolenou hodnotu. Chcete-li zjistit, zda je _typ prostředku_ platný pro **strongType**, použijte [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Některé _typy prostředků,_ které nejsou vráceny **get-AzResourceProvider** jsou podporovány. Jedná se o:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Povolené hodnoty _typu bez prostředků_ pro **strongType** jsou:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Umístění definice

Při vytváření iniciativy nebo zásady je nutné zadat umístění definice. Umístění definice musí být skupina pro správu nebo předplatné. Toto umístění určuje obor, ke kterému lze přiřadit iniciativu nebo zásadu. Zdroje musí být přímými členy nebo podřízenými členy v hierarchii umístění definice, na které se má cílit přiřazení.

Pokud je umístění definice:

- **Předplatné** – zásady lze přiřadit jenom prostředky v rámci tohoto předplatného.
- **Skupiny pro správu** – zásady lze přiřadit pouze prostředky v rámci podřízených skupin pro správu a podřízených předplatných. Pokud plánujete použít definici zásad na několik předplatných, umístění musí být skupina pro správu, která obsahuje tato předplatná.

## <a name="display-name-and-description"></a>Zobrazovaný název a popis

Pomocí **funkce displayName** a **popis** můžete identifikovat definici zásady a poskytnout kontext pro její použití. **displayName** má maximální délku _128_ znaků a **popis** maximální délku _512_ znaků.

> [!NOTE]
> Při vytváření nebo aktualizaci definice zásad jsou **id**, **typ**a **název** definovány vlastnostmi mimo JSON a nejsou v souboru JSON nutné. Při načítání definice zásad pomocí sady SDK vrátí **id**, **typ**a **název** vlastnosti jako součást JSON, ale každý z nich jsou jen pro čtení informace týkající se definice zásady.

## <a name="policy-rule"></a>Pravidlo zásad

Pravidlo zásad se skládá z **if** a **then** bloky. V **bloku If** definujete jednu nebo více podmínek, které určují, kdy je zásada vynucena. Na tyto podmínky můžete použít logické operátory k přesnému definování scénáře pro zásadu.

V **pak** bloku, definujete efekt, který se stane, když jsou splněny **podmínky If.**

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

Není **not** syntaxe invertuje výsledek podmínky. **AllOf** syntaxe (podobně jako logické **a** operace) vyžaduje všechny podmínky, které mají být pravdivé. **AnyOf** syntaxe (podobně jako logické **nebo** operace) vyžaduje jednu nebo více podmínek, které mají být true.

Logické operátory můžete vnořit. Následující příklad ukazuje **není** operace, která je vnořena v rámci **allOf** operace.

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

Podmínka vyhodnotí, zda **pole** nebo přistupující **hodnotu** splňuje určitá kritéria. Podporované podmínky jsou:

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

Pro **méně**, **lessOrEquals**, **greater**, a **greaterOrEquals**, pokud typ vlastnosti neodpovídá typu podmínky, je vyvolána chyba. Porovnání řetězců se `InvariantCultureIgnoreCase`provádí pomocí .

Při použití podmínky to se mi k `*` nám **a** **nelike,** zadáte zástupný znak v hodnotě.
Hodnota by neměla mít více `*`než jeden zástupný znak .

Při použití **shody** a **notMatch** podmínky, zadejte `#` tak, aby odpovídaly číslice, `?` pro písmeno, `.` aby odpovídaly libovolný znak a jakýkoli jiný znak, aby odpovídaly skutečné znak. While, **match** a **notMatch** are case sensitive, všechny ostatní podmínky, které vyhodnocují _řetězecValue_ jsou malá a velká písmena. Alternativy bez rozlišování velkých a malých písmen jsou k dispozici v **matchNesensitively** a **notMatchInsensitively**.

V ** \[ \* \] ** hodnotě pole aliasu je každý prvek v poli vyhodnocován jednotlivě s logickými **a** mezi prvky. Další informace naleznete [v \[ \* tématu Vyhodnocení aliasu \] ](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Pole

Podmínky jsou tvořeny pomocí polí. Pole odpovídá vlastnostem v datové části požadavku na zdroj a popisuje stav zdroje.

Podporována jsou následující pole:

- `name`
- `fullName`
  - Vrátí úplný název zdroje. Úplný název prostředku je název prostředku předřazený názvy nadřazených prostředků (například myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Globální **global** použití pro prostředky, které jsou umístění agnostik.
- `identity.type`
  - Vrátí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) povolené u prostředku.
- `tags`
- `tags['<tagName>']`
  - Tato syntaxe závorky podporuje názvy značek, které mají interpunkci, například pomlčku, tečku nebo mezeru.
  - Kde ** \<tagName\> ** je název značky pro ověření podmínky.
  - Příklady: `tags['Acct.CostCenter']` **kde Acct.CostCenter** je název značky.
- `tags['''<tagName>''']`
  - Tato syntaxe závorky podporuje názvy značek, které mají v ní apostrofy, tím, že uniká s dvojitými apostrofy.
  - Kde **\<'\>tagName '** je název značky, pro kterou má být podmínka ověřena.
  - Příklad: `tags['''My.Apostrophe.Tag''']` kde **'My.Apoststrophe.Tag'** je název značky.
- aliasy vlastností - seznam naleznete v tématu [Aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`a `tags[tag.with.dots]` jsou stále přijatelné způsoby deklarování pole značek. Upřednostňované výrazy jsou však uvedeny výše.

#### <a name="use-tags-with-parameters"></a>Použití značek s parametry

Hodnota parametru může být předána do pole značky. Předání parametru do pole značky zvyšuje flexibilitu definice zásady během přiřazení zásady.

V následujícím příkladu se `concat` používá k vytvoření vyhledávání polí značek pro značku s názvem hodnota parametru **tagName.** Pokud tato značka neexistuje, **efekt úpravy** se použije k přidání značky pomocí hodnoty stejné pojmenované značky nastavené na nadřazené skupině prostředků auditovaných prostředků pomocí `resourcegroup()` vyhledávací funkce.

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

Podmínky mohou být také vytvořeny pomocí **hodnoty**. **hodnota** kontroluje podmínky podle parametrů , [podporovaných](#parameters) [funkcí šablony](#policy-functions)nebo literál.
**je** spárována s jakoukoli [podporovanou podmínkou](#conditions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdaří. Neúspěšné vyhodnocení je implicitní **odepřít**. Další informace naleznete v [tématu předcházení chybám šablony](#avoiding-template-failures). Použití [enforcementMode](./assignment-structure.md#enforcement-mode) **DoNotEnforce** zabránit dopadu neúspěšné hodnocení na nové nebo aktualizované prostředky při testování a ověřování nové definice zásad.

#### <a name="value-examples"></a>Příklady hodnot

Tento příklad pravidla zásad používá **hodnotu** k porovnání `resourceGroup()` výsledku funkce a vráceného **name** vlastnost i na **podobné** podmínky . `*netrg` Pravidlo zakazuje všechny prostředky, `Microsoft.Network/*` které nejsou **typu** v žádné `*netrg`skupině prostředků, jejíž název končí v .

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

Tento příklad pravidla zásad používá **hodnotu** ke kontrole, zda se výsledek více vnořených funkcí **rovná** `true`. Pravidlo zakazuje všechny prostředky, které nemají alespoň tři značky.

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

#### <a name="avoiding-template-failures"></a>Předcházení chybám šablony

Použití _funkcí šablony_ v **hodnotě** umožňuje mnoho složitých vnořených funkcí. Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdaří. Neúspěšné vyhodnocení je implicitní **odepřít**. Příklad **hodnoty,** která se nezdaří v určitých scénářích:

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

Výše uvedené pravidlo zásad používá [podřetězec()](../../../azure-resource-manager/templates/template-functions-string.md#substring) k porovnání prvních tří znaků **názvu** s **abc**. Pokud je **název** kratší než `substring()` tři znaky, výsledkem funkce je chyba. Tato chyba způsobí, že zásady, aby se stal **odepřít** efekt.

Místo toho použijte funkci [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) ke kontrole, zda první tři znaky **názvu** se rovnají **abc,** aniž by **název** kratší než tři znaky způsobit chybu:

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

Pomocí revidovaného pravidla `if()` zásad zkontroluje délku **názvu** `substring()` před pokusem o získání hodnoty s méně než třemi znaky. Pokud **název** je příliš krátká, je vrácena hodnota "nezačíná abc" je vrácena a porovnána **abc**. Prostředek s krátkým názvem, který nezačíná **abc** stále selže pravidlo zásad, ale již způsobuje chybu během hodnocení.

### <a name="count"></a>Počet

Podmínky, které počítají, kolik členů pole v datové části prostředku splňuje výraz podmínky, lze vytvořit pomocí výrazu **count.** Běžné scénáře jsou kontrolu, zda 'alespoň jeden z', 'přesně jeden z', 'všechny' nebo 'žádný z' členy pole splňují podmínku. **Count** vyhodnotí _true_ každý [ \[ \* \] ](#understanding-the--alias) člen pole aliasu pro výraz podmínky a sečte skutečné výsledky, které jsou pak porovnány s operátorem výrazu.

Struktura výrazu **count** je:

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

S **počtem**se používají následující vlastnosti :

- **count.field** (povinné): Obsahuje cestu k poli a musí být alias pole. Pokud pole chybí, výraz je vyhodnocen na _false_ bez ohledu na výraz podmínky.
- **count.where** (nepovinné): Výraz podmínky pro [ \[ \* \] ](#understanding-the--alias) individuální vyhodnocení každého člena pole aliasu **count.field**. Pokud tato vlastnost není k dispozici, všechny členy pole s cestou 'pole' jsou vyhodnoceny na _true_. V této vlastnosti lze použít libovolnou [podmínku.](../concepts/definition-structure.md#conditions)
  [Logické operátory](#logical-operators) lze použít uvnitř této vlastnosti k vytvoření komplexní požadavky na vyhodnocení.
- condition (required): Hodnota je porovnána s počtem položek, které splnily výraz podmínky **count.where.** ** \<\> ** Měla by být použita číselná [podmínka.](../concepts/definition-structure.md#conditions)

#### <a name="count-examples"></a>Počet příkladů

Příklad 1: Kontrola, zda je pole prázdné

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Příklad 2: Kontrola pouze jednoho člena pole, který splňuje výraz podmínky

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

Příklad 3: Kontrola alespoň jednoho člena pole, aby splnil výraz podmínky

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

Příklad 4: Zkontrolujte, zda všichni členové pole objektů splňují výraz podmínky

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

Příklad 5: Zkontrolujte, zda všichni členové pole řetězců splňují výraz podmínky

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

Příklad 6: Pomocí **hodnoty** **uvnitř pole** zkontrolujte, zda všichni členové pole splňují výraz podmínky.

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

Příklad 7: Zkontrolujte, zda alespoň jeden člen pole odpovídá více vlastnostem ve výrazu podmínky

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

Azure Policy podporuje následující typy efektů:

- **Připojit**: přidá do požadavku definovanou sadu polí.
- **Audit**: vygeneruje varovnou událost v protokolu aktivit, ale nesplní požadavek.
- **AuditIfNotExists**: Generuje událost upozornění v protokolu aktivit, pokud související prostředek neexistuje.
- **Odepřít**: vygeneruje událost v protokolu aktivit a nezdaří požadavek
- **DeployIfNotExists:** nasazuje související prostředek, pokud ještě neexistuje.
- **Zakázáno**: nevyhodnocuje prostředky z důvodu dodržování zásad.
- **EnforceOPAConstraint** (preview): Konfiguruje řadič přijetí agenta otevřených zásad s Gatekeeper v3 pro clustery Kubernetes s vlastním spravem v Azure (preview)
- **EnforceRegoPolicy** (preview): Konfiguruje řadič přijetí agenta otevřených zásad pomocí gatekeeperu v2 ve službě Azure Kubernetes Service.
- **Změnit**: přidá, aktualizuje nebo odebere definované značky z prostředku.

Úplné podrobnosti o jednotlivých účincích, pořadí vyhodnocení, vlastnostech a příkladech najdete [v tématu Principy efektů zásad Azure](effects.md).

### <a name="policy-functions"></a>Funkce zásad

Všechny [funkce šablony Správce prostředků](../../../azure-resource-manager/templates/template-functions.md) jsou k dispozici pro použití v rámci pravidla zásad, s výjimkou následujících funkcí a uživatelem definovaných funkcí:

- copyIndex()
- nasazení()
- seznam*
- newGuid()
- pickZones()
- poskytovatelé()
- reference()
- resourceId()
- proměnné()

> [!NOTE]
> Tyto funkce jsou stále `details.deployment.properties.template` k dispozici v rámci části nasazení šablony v definici zásad **deployIfNotExists.**

Následující funkce je k dispozici pro použití v pravidle zásad, ale liší se od použití v šabloně Azure Resource Manager:

- `utcNow()`- Na rozdíl od šablony Správce prostředků, to lze použít mimo defaultValue.
  - Vrátí řetězec, který je nastaven na aktuální datum a čas ve formátu Universal ISO 8601 DateTime 'yyyy-MM-ddTHH:mm:ss.fffffffZ'

Následující funkce jsou k dispozici pouze v pravidlech zásad:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [Povinné] řetězec - Řetězec v univerzálním formátu ISO 8601 DateTime 'yyyy-MM-ddTHH:mm:ss.fffffffZ'
  - **numberOfDaysToAdd**: [Povinné] celé číslo - Počet dní, které chcete přidat
- `field(fieldName)`
  - **fieldName**: [Povinné] řetězec - Název [pole,](#fields) které chcete načíst
  - Vrátí hodnotu tohoto pole ze zdroje, který je vyhodnocován podmínkou If.
  - `field`Používá se především s **AuditIfNotExists** a **DeployIfNotExists** pro referenční pole na prostředek, které jsou vyhodnocovány. Příklad tohoto použití lze vidět v [příkladu DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Vrátí verzi rozhraní API požadavku, který spustil `2019-09-01`vyhodnocení zásad (příklad: ).
    Bude to verze rozhraní API, která byla použita v požadavku PUT/PATCH pro vyhodnocení při vytváření nebo aktualizaci prostředků. Nejnovější verze rozhraní API se vždy používá při hodnocení dodržování předpisů na stávající prostředky.
  
#### <a name="policy-function-example"></a>Příklad funkce zásad

Tento příklad pravidla `resourceGroup` zásad používá funkci prostředku k získání `concat` vlastnosti **name** v kombinaci s funkcí array a object k vytvoření `like` podmínky, která vynucuje název prostředku, aby začal s názvem skupiny prostředků.

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

Aliasy vlastností se používají k přístupu k určitým vlastnostem pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povoleny pro vlastnost prostředku. Každý alias se mapuje na cesty v různých verzích rozhraní API pro daný typ prostředku. Během vyhodnocení zásad modul získá cestu vlastnosti pro tuto verzi rozhraní API.

Seznam aliasů se neustále zvětšuje. Chcete-li zjistit, jaké aliasy jsou aktuálně podporované zásadami Azure, použijte jednu z následujících metod:

- Rozšíření zásad Azure pro kód Visual Studia (doporučeno)

  Pomocí [rozšíření Zásad Azure pro visual studio kód](../how-to/extension-for-vscode.md) zobrazit a zjišťovat aliasy pro vlastnosti prostředků.

  ![Rozšíření zásad Azure pro kód Visual Studia](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Pomocí `project` operátoru zobrazte **alias** prostředku.

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

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Principy aliasu [*]

Některé aliasy, které jsou k dispozici mají verzi, která se ** \[ \* ** zobrazí jako 'normální' název a jiný, který je připojen k němu. Příklad:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normal" představuje pole jako jednu hodnotu. Toto pole je pro scénáře porovnání přesné shody, kdy celá sada hodnot musí být přesně tak, jak je definována, nic víc a ne méně.

** \[ Alias \* ** umožňuje porovnat s hodnotou každého prvku v poli a specifické vlastnosti každého prvku. Tento přístup umožňuje porovnat vlastnosti elementu pro "if none of", "If any of" nebo "if all of" scénáře. Pro složitější scénáře použijte výraz podmínky [count.](#count) Pomocí **\[\*ipRules**, příkladem by bylo ověření, že každá _akce_ je _Deny_, ale nestarat o tom, kolik pravidel existují nebo jaká je _hodnota_ IP.
Toto vzorové pravidlo kontroluje všechny shody **\[\*\]ipRules .value** na **10.0.4.1** a použije **effectType** pouze v případě, že nenajde alespoň jednu shodu:

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

Další informace naleznete [v tématu vyhodnocení aliasu [\*].](../how-to/author-policies-for-arrays.md#evaluating-the--alias)

## <a name="initiatives"></a>Iniciativy

Iniciativy umožňují seskupit několik souvisejících definic zásad, abyste zjednodušili přiřazení a správu, protože se skupinou pracujete jako s jednou položkou. Můžete například seskupit definice souvisejících definic zásad označování do jedné iniciativy. Místo přiřazování jednotlivých zásad jednotlivě použijete iniciativu.

> [!NOTE]
> Jakmile je iniciativa přiřazena, nelze změnit parametry na úrovni iniciativy. Z tohoto důvodu je doporučení nastavit **defaultValue** při definování parametru.

Následující příklad ukazuje, jak vytvořit iniciativu pro `costCenter` `productName`zpracování dvou značek: a . Používá dvě předdefinované zásady k použití výchozí hodnoty značky.

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

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
