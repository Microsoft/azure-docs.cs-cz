---
title: Podrobnosti struktury definice zásad
description: Popisuje způsob, jakým se používají definice zásad k navázání konvencí pro prostředky Azure ve vaší organizaci.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: cebba214671cfab75a3f44720578b51febacdfcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215064"
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Azure Policy stanoví konvence pro prostředky. Definice zásad popisují [podmínky](#conditions) dodržování předpisů prostředků a efekt, který se má provést, pokud je splněna podmínka. Podmínka porovnává [pole](#fields) vlastností prostředku nebo [hodnotu](#value) s požadovanou hodnotou. K polím vlastností prostředku se dostanete pomocí [aliasů](#aliases). Pokud je pole vlastnosti prostředku pole, lze použít speciální [alias pole](#understanding-the--alias) , který umožňuje vybrat hodnoty ze všech členů pole a použít podmínku na každou z nich. Přečtěte si další informace o [podmínkách](#conditions).

Definováním konvencí můžete řídit náklady a snadněji spravovat prostředky. Můžete například určit, že jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby prostředky měly konkrétní značku. Přiřazení zásad se dědí prostřednictvím podřízených prostředků. Pokud se pro skupinu prostředků použije přiřazení zásady, vztahuje se na všechny prostředky v této skupině prostředků.

_PolicyRule_ schéma definice zásad najdete tady:[https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

K vytvoření definice zásady použijete JSON. Definice zásad obsahuje prvky pro:

- zobrazované jméno
- description
- režim
- zprostředkovatele identity
- parameters
- pravidlo zásad
  - logické vyhodnocení
  - až

Například následující JSON zobrazuje zásadu, která omezuje, kde jsou prostředky nasazeny:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure Policy předdefinované a vzory jsou v [Azure Policy Samples](../samples/index.md).

## <a name="display-name-and-description"></a>Zobrazované jméno a popis

K identifikaci definice zásad a zadání kontextu, kdy se má použít, můžete použít **DisplayName** a **Description** . hodnota **DisplayName** má maximální délku _128_ znaků a **popis** nesmí být delší než _512_ znaků.

> [!NOTE]
> Během vytváření nebo aktualizace definice zásady, **ID**, **typu** a **název** jsou definovány vlastnostmi, které jsou externí pro JSON a nejsou nezbytné v souboru JSON. Načtení definice zásady prostřednictvím sady SDK vrátí vlastnosti **ID**, **typu** a **názvu** jako součást formátu JSON, ale každá z nich je informace jen pro čtení, které se týkají definice zásady.

## <a name="type"></a>Typ

I když nelze nastavit vlastnost **typu** , existují tři hodnoty, které jsou vráceny sadou SDK a viditelné na portálu:

- `Builtin`: Tyto definice zásad poskytuje a udržuje společnost Microsoft.
- `Custom`: Tato hodnota obsahuje všechny definice zásad vytvořené zákazníky.
- `Static`: Označuje definici zásady [dodržování předpisů pro zákonné](./regulatory-compliance.md) **vlastnictví s vlastnictvím** Microsoftu. Výsledky dodržování předpisů pro tyto definice zásad jsou výsledky auditů třetích stran v infrastruktuře Microsoftu. V Azure Portal se tato hodnota někdy zobrazuje jako **spravovaná Microsoftem**. Další informace najdete v tématu [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Režim

**Režim** je nakonfigurován v závislosti na tom, zda je zásada cílena na vlastnost Azure Resource Manager nebo vlastnost poskytovatele prostředků.

### <a name="resource-manager-modes"></a>Správce prostředků režimy

**Režim** určuje, které typy prostředků se vyhodnotí pro definici zásady. Podporované režimy:

- `all`: vyhodnotit skupiny prostředků, předplatná a všechny typy prostředků
- `indexed`: vyhodnotit jenom typy prostředků, které podporují značky a umístění.

Například prostředek `Microsoft.Network/routeTables` podporuje značky a umístění a je vyhodnocován v obou režimech. Prostředek ale `Microsoft.Network/routeTables/routes` nemůže být tagovaný a není vyhodnocený v `Indexed` režimu.

Ve většině případů doporučujeme nastavit **režim** na `all` . Všechny definice zásad vytvořené prostřednictvím portálu používají `all` režim. Pokud používáte PowerShell nebo Azure CLI, můžete zadat parametr **Mode** ručně. Pokud definice zásady neobsahuje hodnotu **režimu** , použije se výchozí hodnota `all` v Azure PowerShell a v rozhraní příkazového `null` řádku Azure CLI. `null`Režim je stejný jako při použití `indexed` k podpoře zpětné kompatibility.

`indexed` by měla být použita při vytváření zásad, které vysazují značky nebo umístění. I když to není nutné, zabrání prostředkům, které nepodporují značky a umístění, z hlediska výsledků dodržování předpisů v nedodržení předpisů. Výjimkou jsou **skupiny prostředků** a **předplatná**. Definice zásad, které vynutily umístění nebo značky v rámci skupiny prostředků nebo předplatného, by měly nastavit **režim** na `all` a konkrétně cílit na `Microsoft.Resources/subscriptions/resourceGroups` `Microsoft.Resources/subscriptions` typ nebo. Příklad naleznete v tématu [Pattern: Tags-Sample #1](../samples/pattern-tags.md). Seznam prostředků, které podporují značky, najdete v tématu [Podpora značek pro prostředky Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Režimy poskytovatele prostředků

Následující režim poskytovatele prostředků je plně podporovaný:

- `Microsoft.Kubernetes.Data` pro správu clusterů Kubernetes v systému Azure nebo mimo něj. Definice používající tento režim poskytovatele prostředků používají účinky _audit_, _Deny_ a _disabled_. Použití efektu [EnforceOPAConstraint](./effects.md#enforceopaconstraint) je _zastaralé_.

V současné době jsou podporovány následující režimy poskytovatele prostředků ve **verzi Preview**:

- `Microsoft.ContainerService.Data` pro správu pravidel kontroleru přístupu pro [službu Azure Kubernetes](../../../aks/intro-kubernetes.md). Definice používající tento režim poskytovatele prostředků **musí** používat efekt [EnforceRegoPolicy](./effects.md#enforceregopolicy) . Tento režim je _zastaralý_.
- `Microsoft.KeyVault.Data` pro správu trezorů a certifikátů v [Azure Key Vault](../../../key-vault/general/overview.md). Další informace o těchto definicích zásad najdete v tématu věnovaném [integraci Azure Key Vault s Azure Policy](../../../key-vault/general/azure-policy.md).

> [!NOTE]
> Režimy poskytovatele prostředků podporují jenom integrované definice zásad a nepodporují [výjimky](./exemption-structure.md).

## <a name="metadata"></a>Metadata

Volitelná `metadata` vlastnost ukládá informace o definici zásady. Zákazníci mohou definovat libovolné vlastnosti a hodnoty, které jsou užitečné pro jejich organizaci v `metadata` . Existují však některé _běžné_ vlastnosti, které používá Azure Policy a v integrovaných modulech. Každá `metadata` vlastnost má limit 1024 znaků.

### <a name="common-metadata-properties"></a>Vlastnosti běžných metadat

- `version` (String): sleduje podrobnosti o verzi obsahu definice zásady.
- `category` (String): Určuje, pod kterou kategorii v Azure Portal je tato definice zásad zobrazená.
- `preview` (Boolean): příznak True nebo false pro, pokud je definice zásady ve _verzi Preview_.
- `deprecated` (Boolean): příznak True nebo false pro, pokud byla definice zásad označena jako _zastaralá_.

> [!NOTE]
> Služba Azure Policy používá `version` vlastnosti, `preview` a `deprecated` k vyjádření úrovně změny předdefinované definice nebo iniciativy a stavu zásad. Formát `version` je: `{Major}.{Minor}.{Patch}` . Konkrétní stavy, například _zastaralé_ nebo ve _verzi Preview_, jsou připojeny k `version` vlastnosti nebo v jiné vlastnosti jako **logická hodnota**. Další informace o způsobu, jakým jsou předdefinované verze Azure Policy, najdete v tématu [Vestavěná správa verzí](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametry

Parametry vám pomůžou zjednodušit správu zásad tím, že se sníží počet definic zásad. Představte si parametry jako pole ve formuláři – `name` , `address` , `city` , `state` . Tyto parametry vždy zůstávají stejné, ale jejich hodnoty se změní na základě jednotlivých vyplňování formuláře.
Parametry fungují stejným způsobem při vytváření zásad. Zahrnutím parametrů do definice zásady můžete tuto zásadu použít pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Parametry mohou být přidány do existující a přiřazené definice. Nový parametr musí zahrnovat vlastnost **DefaultValue** . To brání tomu, aby se stávající přiřazení zásady nebo iniciativy od nepřímo neplatně vytvořila.

### <a name="parameter-properties"></a>Vlastnosti parametru

Parametr má následující vlastnosti, které se používají v definici zásady:

- `name`: Název vašeho parametru. Používá se `parameters` funkcí nasazení v rámci pravidla zásad. Další informace najdete v tématu [použití hodnoty parametru](#using-a-parameter-value).
- `type`: Určuje, zda je parametr typu **řetězec**, **pole**, **objekt**, **Boolean**, **Integer**, **float** nebo **DateTime**.
- `metadata`: Definuje podvlastnost primárně používané Azure Portal k zobrazení uživatelsky přívětivých informací:
  - `description`: Vysvětlení použití parametru pro. Dá se použít k zadání příkladů přijatelných hodnot.
  - `displayName`: Popisný název zobrazený na portálu pro parametr.
  - `strongType`: (Volitelné) používá se při přiřazování definice zásady prostřednictvím portálu. Poskytuje seznam podporující kontext. Další informace najdete v tématu [strongType](#strongtype).
  - `assignPermissions`: (Volitelné) nastavte na _hodnotu true_ , pokud chcete, Azure Portal vytvořit přiřazení rolí během přiřazování zásad. Tato vlastnost je užitečná v případě, že chcete přiřadit oprávnění mimo rozsah přiřazení. Podle definice role v zásadě (nebo definice role v rámci všech zásad v iniciativě) existuje jedno přiřazení role. Hodnota parametru musí být platným prostředkem nebo oborem.
- `defaultValue`: (Volitelné) nastaví hodnotu parametru v přiřazení, pokud není zadána žádná hodnota.
  Vyžaduje se při aktualizaci existující definice zásady, která je přiřazená.
- `allowedValues`: (Volitelné) poskytuje pole hodnot, které parametr akceptuje během přiřazování. Porovnávání povolených hodnot rozlišuje velká a malá písmena. 

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

V pravidle zásad můžete odkazovat na parametry pomocí následující `parameters` Syntaxe funkce:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Tento příklad odkazuje na parametr **allowedLocations** , který byl prokázán ve [vlastnostech parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

V rámci `metadata` vlastnosti můžete použít **strongType** k poskytnutí seznamu možností s vícenásobným výběrem v rámci Azure Portal. **strongType** může být podporovaný _typ prostředku_ nebo povolená hodnota. Chcete-li zjistit, zda je _typ prostředku_ platný pro **strongType**, použijte [příkaz Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). Formát pro _typ prostředku_ **strongType** je `<Resource Provider>/<Resource Type>` . Například, `Microsoft.Network/virtualNetworks/subnets`.

Některé _typy prostředků_ , které nejsou vraceny **Get-AzResourceProvider** , jsou podporovány. Tyto typy jsou:

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

- V rámci tohoto předplatného se dají přiřadit definice zásady jenom pro prostředky s **předplatným** .
- Pro **skupinu pro správu** jsou k disřádek pouze prostředky v rámci podřízených skupin pro správu a podřízených odběrů. Pokud plánujete použít definici zásady pro několik předplatných, umístění musí být skupina pro správu, která obsahuje každé předplatné.

Další informace najdete v tématu [vysvětlení oboru v Azure Policy](./scope.md#definition-location).

## <a name="policy-rule"></a>Pravidlo zásad

Pravidlo zásad se skládá z **if** a **pak** Block. V bloku **if** můžete definovat jednu nebo více podmínek, které určují, kdy se zásada vynutila. Můžete použít logické operátory na tyto podmínky a přesně tak definovat scénář pro zásady.

V bloku **potom** definujete efekt, který nastane, když jsou splněny podmínky **if** .

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
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

Podmínka vyhodnocuje, zda hodnota splňuje určitá kritéria. Podporované podmínky jsou:

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
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

Pro **méně**, **lessOrEquals**, **větší** a **greaterOrEquals**, pokud typ vlastnosti neodpovídá typu podmínky, je vyvolána chyba. Porovnávání řetězců je provedeno pomocí `InvariantCultureIgnoreCase` .

Při použití podmínek **Like** a **notLike** zadáte v hodnotě zástupný znak `*` . Hodnota by neměla mít více než jeden zástupný znak `*` .

Při použití podmínek **Match** a **notMatch** zadejte, `#` aby odpovídaly číslici, `?` pro písmeno, `.` aby odpovídaly jakémukoli znaku a jakémukoliv jinému znaku, aby odpovídaly tomuto skutečnému znaku. Zatímco **Match** a **notMatch** rozlišují velká a malá písmena, všechny ostatní podmínky, které vyhodnocují _StringValue_ , rozlišují malá a velká písmena. Alternativy nerozlišující velká a malá písmena jsou k dispozici v **matchInsensitively** a **notMatchInsensitively**.

### <a name="fields"></a>Pole

Podmínky, které vyhodnocují, zda hodnoty vlastností v datové části požadavku prostředku splňují určitá kritéria, mohou být vytvořeny pomocí výrazu **pole** . Podporují se následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku. Úplný název prostředku je název prostředku, který předchází všechny názvy nadřazených prostředků (například "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Pole umístění jsou normalizována na podporu různých formátů. Například `East US 2` je považována za rovnou `eastus2` .
  - Pro prostředky, které jsou nezávislá umístění, použijte **globální** .
- `id`
  - Vrátí ID prostředku, který se má vyhodnotit.
  - Příklad: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - Vrátí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) povolené v prostředku.
- `tags`
- `tags['<tagName>']`
  - Tato syntaxe závorky podporuje názvy značek, které mají interpunkční znaménka, jako je například spojovník, tečka nebo mezera.
  - Kde **\<tagName\>** je název značky, pro kterou má být podmínka ověřena.
  - Příklady: `tags['Acct.CostCenter']` kde **Acct. CostCenter** je název značky.
- `tags['''<tagName>''']`
  - Tato syntaxe závorky podporuje názvy značek, které mají apostrofy, pomocí uvozovacích znaků s dvojitými apostrofy.
  - Kde **' \<tagName\> '** je název značky, pro kterou má být podmínka ověřena.
  - Příklad: `tags['''My.Apostrophe.Tag''']` kde **' My. apostrof. tag '** je název značky.
- aliasy vlastností – pro seznam najdete v tématu [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` a `tags[tag.with.dots]` jsou stále přijatelné způsoby deklarace pole značek. Preferované výrazy jsou však uvedeny výše.

> [!NOTE]
> V výrazech **pole** odkazujících na **\[ \* \] alias** jsou jednotlivé prvky v poli vyhodnocovány individuálně pomocí logických prvků **a** mezi prvky. Další informace naleznete v tématu [odkazující na vlastnosti prostředku pole](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="use-tags-with-parameters"></a>Použití značek s parametry

Hodnota parametru může být předána poli značky. Předání parametru do pole značek zvyšuje flexibilitu definice zásad během přiřazení zásady.

V následujícím příkladu `concat` se používá k vytvoření vyhledávání pole značek pro značku s názvem hodnota parametru **TagName** . Pokud tato značka neexistuje, použije se  k přidání značky pomocí hodnoty stejné pojmenované sady značek v nadřazené skupině prostředků auditované prostředky pomocí `resourcegroup()` funkce Lookup efekt změny.

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
                "/providers/microsoft.authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f"
            ]
        }
    }
}
```

### <a name="value"></a>Hodnota

Podmínky, které vyhodnocují, zda hodnota splňují určitá kritéria, mohou být vytvořeny pomocí výrazu **hodnoty** . Hodnoty mohou být literály, hodnoty [parametrů](#parameters)nebo vrácené hodnoty všech [podporovaných funkcí šablon](#policy-functions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Další informace najdete v tématu [předcházení chybám šablon](#avoiding-template-failures). Použijte [enforcementMode](./assignment-structure.md#enforcement-mode) z **DoNotEnforce** , abyste zabránili dopadu neúspěšného vyhodnocení nových nebo aktualizovaných prostředků při testování a ověřování nové definice zásady.

#### <a name="value-examples"></a>Příklady hodnot

Tato ukázka pravidla zásad používá **hodnotu** pro porovnání výsledku `resourceGroup()` funkce a vlastnosti vráceného **názvu** s podmínkou **Like** `*netrg` . Pravidlo odepře jakýkoliv prostředek, který není `Microsoft.Network/*` **typu** v žádné skupině prostředků, jejíž název končí na `*netrg` .

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

Tato ukázka pravidla zásad používá **hodnotu** ke kontrole, jestli se výsledek více vnořených funkcí **rovná** `true` . Pravidlo odmítne všechny prostředky, které nemají alespoň tři značky.

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

Vzorové pravidlo výše uvedené používá [podřetězec ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) pro porovnání prvních tří znaků **názvu** s **ABC**. Pokud je **název** kratší než tři znaky, `substring()` výsledkem funkce je chyba. Tato chyba způsobí, že se zásada stane účinkem **odepření** .

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

S revidovaným pravidlem zásad `if()` před tím, než  se pokusíte získat `substring()` hodnotu s méně než třemi znaky, zkontroluje délku názvu. Pokud je **název** příliš krátký, je místo toho vrácena hodnota "nezačíná na ABC" a porovnána s **ABC**. Prostředek s krátkým názvem, který nezačíná na **ABC** , se stále neúspěšně stane pravidlem zásad, ale během vyhodnocování se nestane příčinou chyby.

### <a name="count"></a>Počet

Podmínky, které počítají, kolik členů pole splňují určitá kritéria, mohou být vytvořeny pomocí výrazu **Count** . Běžné scénáře kontrolují, jestli je aspoň jedna z ', ' přesně jedna z ', ' vše z ', ' all z ' nebo ' None of ', aby splňovala podmínku. Funkce **Count** vyhodnocuje jednotlivé členy pole pro výraz podmínky a sečte _skutečné_ výsledky, které jsou pak porovnány s operátorem výrazu.

#### <a name="field-count"></a>Počet polí

Spočítá, kolik členů pole v datové části požadavku vyhovuje výrazu podmínky. Struktura výrazů **počtu polí** je:

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

Pro **počet polí** se používají tyto vlastnosti:

- **Count. Field** (Required): obsahuje cestu k poli a musí se jednat o alias pole.
- **Count. Where** (volitelné): výraz podmínky, který se má individuálně vyhodnotit pro každého člena pole [ \[ \* \] aliasu](#understanding-the--alias) `count.field` . Pokud tato vlastnost není k dispozici, jsou všechny členy pole s cestou pole vyhodnoceny na _hodnotu true_. V této vlastnosti lze použít jakoukoli [podmínku](../concepts/definition-structure.md#conditions) .
  [Logické operátory](#logical-operators) lze použít uvnitř této vlastnosti k vytvoření složitých požadavků na vyhodnocení.
- **\<condition\>** (povinné): hodnota je porovnána s počtem položek, které splnily výraz **Count. Where** podmínky. Měla by se použít číselná [Podmínka](../concepts/definition-structure.md#conditions) .

Výrazy **Count pole** mohou v rámci jedné definice **policyRule** vytvořit výčet stejných polí v jednom definici až třikrát.

Další informace o tom, jak pracovat s vlastnostmi pole v Azure Policy, včetně podrobného vysvětlení toho, jak je výraz **počtu polí** vyhodnocován, naleznete v tématu [odkazující na vlastnosti prostředku pole](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="value-count"></a>Počet hodnot

Spočítá, kolik členů pole splní podmínku. Pole může být literální pole nebo [odkaz na parametr array](#using-a-parameter-value). Struktura výrazů **počtu hodnot** je:

```json
{
    "count": {
        "value": "<literal array | array parameter reference>",
        "name": "<index name>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

S **počtem hodnot** se používají tyto vlastnosti:

- **Count. Value** (Required): pole, které se má vyhodnotit.
- **Count.Name** (povinné): název indexu složený z anglických písmen a číslic. Definuje název hodnoty člena pole vyhodnoceného v aktuální iteraci. Název se používá pro odkazování aktuální hodnoty uvnitř `count.where` podmínky. Volitelné, pokud výraz **Count** není v podřízeném objektu jiného výrazu **Count** . Pokud není zadaný, název indexu se implicitně nastaví na `"default"` .
- **Count. Where** (volitelné): výraz podmínky, který se má individuálně vyhodnotit pro každého člena pole `count.value` . Pokud tato vlastnost není k dispozici, všechny členy pole budou vyhodnocovány na _hodnotu true_. V této vlastnosti lze použít jakoukoli [podmínku](../concepts/definition-structure.md#conditions) . [Logické operátory](#logical-operators) lze použít uvnitř této vlastnosti k vytvoření složitých požadavků na vyhodnocení. K hodnotě aktuálně výčtového člena pole lze přistupovat voláním [aktuální](#the-current-function) funkce.
- **\<condition\>** (povinné): hodnota je porovnána s počtem položek, které splnily `count.where` výraz podmínky. Měla by se použít číselná [Podmínka](../concepts/definition-structure.md#conditions) .

Vynutila se následující omezení:
- V jedné definici **policyRule** lze použít až 10 výrazů **počtu hodnot** .
- Každý výraz **počet hodnot** může provádět až 100 iterací. Toto číslo zahrnuje počet iterací provedených všemi nadřazenými výrazy **počtu hodnot** .

#### <a name="the-current-function"></a>Aktuální funkce

`current()`Funkce je k dispozici pouze v rámci `count.where` podmínky. Vrací hodnotu člena pole, která je aktuálně výčtem vyhodnocení výrazu **Count** .

**Využití počtu hodnot**

- `current(<index name defined in count.name>)`. Příklad: `current('arrayMember')`.
- `current()`. Povoleno pouze v případě, že výraz **Count hodnoty** není podřízeným objektem jiného výrazu **Count** . Vrací stejnou hodnotu jako výše.

Pokud hodnota vrácená voláním je objekt, jsou podporovány přístupové objekty vlastností. Příklad: `current('objectArrayMember').property`.

**Využití počtu polí**

- `current(<the array alias defined in count.field>)`. Například, `current('Microsoft.Test/resource/enumeratedArray[*]')`.
- `current()`. Povoleno pouze v případě, že výraz **počtu polí** není podřízeným prvkem jiného výrazu **Count** . Vrací stejnou hodnotu jako výše.
- `current(<alias of a property of the array member>)`. Například, `current('Microsoft.Test/resource/enumeratedArray[*].property')`.

#### <a name="field-count-examples"></a>Příklady počtu polí

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

Příklad 5: Ověřte, že aspoň jeden člen pole odpovídá více vlastnostem ve výrazu podmínky.

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

Příklad 6: použití `current()` funkce v `where` podmínkách pro přístup k hodnotě aktuálně výčtového člena pole v rámci funkce šablony. Tato podmínka kontroluje, zda virtuální síť obsahuje předponu adresy, která není v rozsahu CIDR 10.0.0.0/24.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
          "equals": false
        }
    },
    "greater": 0
}
```

Příklad 7: použití `field()` funkce v `where` podmínkách pro přístup k hodnotě aktuálně výčtového člena pole. Tato podmínka kontroluje, zda virtuální síť obsahuje předponu adresy, která není v rozsahu CIDR 10.0.0.0/24.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', first(field(('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]')))]",
          "equals": false
        }
    },
    "greater": 0
}
```

#### <a name="value-count-examples"></a>Příklady počtu hodnot

Příklad 1: Ověřte, zda název prostředku odpovídá některé z uvedených vzorů názvů.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Příklad 2: Ověřte, zda název prostředku odpovídá některé z uvedených vzorů názvů. `current()`Funkce neurčuje název indexu. Výsledek je stejný jako předchozí příklad.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

Příklad 3: Ověřte, zda název prostředku odpovídá jakémukoli z daných vzorů názvů poskytnutých parametrem Array.

```json
{
    "count": {
        "value": "[parameters('namePatterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Příklad 4: Ověřte, jestli žádná z předpon adres virtuální sítě není v seznamu schválených předpon.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
            "count": {
                "value": "[parameters('approvedPrefixes')]",
                "name": "approvedPrefix",
                "where": {
                    "value": "[ipRangeContains(current('approvedPrefix'), current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
                    "equals": true
                },
            },
            "equals": 0
        }
    },
    "greater": 0
}
```

Příklad 5: Ověřte, že jsou v NSG definovaná všechna rezervovaná pravidla NSG. Vlastnosti rezervovaných pravidel NSG jsou definovány v parametru pole obsahujícího objekty.

Hodnota parametru:

```json
[
    {
        "priority": 101,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 22
    },
    {
        "priority": 102,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 3389
    }
]
```

Politických
```json
{
    "count": {
        "value": "[parameters('reservedNsgRules')]",
        "name": "reservedNsgRule",
        "where": {
            "count": {
                "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
                "where": {
                    "allOf": [
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].priority",
                            "equals": "[current('reservedNsgRule').priority]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                            "equals": "[current('reservedNsgRule').access]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                            "equals": "[current('reservedNsgRule').direction]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                            "equals": "[current('reservedNsgRule').destinationPortRange]"
                        }
                    ]
                }
            },
            "equals": 1
        }
    },
    "equals": "[length(parameters('reservedNsgRules'))]"
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
- **Upravit**: Přidání, aktualizace nebo odebrání definovaných značek z prostředku nebo předplatného
- **EnforceOPAConstraint** (zastaralé): konfiguruje Open Controller agent admissioning Controller s gatekeeper v3 pro samoobslužně spravované clustery Kubernetes v Azure.
- **EnforceRegoPolicy** (zastaralé): konfiguruje správce otevřeného agenta zásad přístupu s gatekeeper v2 ve službě Azure Kubernetes.

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
> Tyto funkce jsou stále k dispozici v rámci `details.deployment.properties.template` nasazení šablony v definici zásady **deployIfNotExists** .

Tato funkce je k dispozici pro použití v pravidle zásad, ale liší se od použití v šabloně Azure Resource Manager (šablona ARM):

- `utcNow()` -Na rozdíl od šablony ARM lze tuto vlastnost použít mimo vlastnost _DefaultValue_.
  - Vrátí řetězec, který je nastaven na aktuální datum a čas ve formátu Universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Následující funkce jsou dostupné jenom v pravidlech zásad:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [required] řetězec řetězce ve formátu Universal ISO 8601 DateTime "rrrr-MM-DDThh: mm: ss. FFFFFFFZ'
  - **numberOfDaysToAdd**: [povinné] celé číslo-počet dnů k přidání
- `field(fieldName)`
  - **FieldName**: [required] řetězec-název [pole](#fields) , které se má načíst
  - Vrátí hodnotu tohoto pole z prostředku, který je vyhodnocován podmínkou if.
  - `field` se primárně používá s **AuditIfNotExists** a **DeployIfNotExists** pro odkazování na pole v prostředku, který je vyhodnocován. Příklad tohoto použití lze zobrazit v [DeployIfNotExists příkladu](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Vrátí verzi rozhraní API žádosti, která aktivovala vyhodnocování zásad (například: `2019-09-01` ).
    Tato hodnota je verze rozhraní API, která se použila v požadavku PUT/PATCH k vyhodnocení při vytváření nebo aktualizaci prostředku. Nejnovější verze rozhraní API se vždycky používá během hodnocení dodržování předpisů u stávajících prostředků.
- `policy()`
  - Vrátí následující informace o vyhodnocené zásadě. K vlastnostem lze přistup z vráceného objektu (například: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```

- `ipRangeContains(range, targetRange)`
  - **Range**: [required] řetězec-řetězec určující rozsah IP adres.
  - **targetRange**: [povinný] řetězec – řetězec určující rozsah IP adres.

  Vrátí, zda daný rozsah IP adres obsahuje cílový rozsah IP adres. Prázdné rozsahy nebo kombinování mezi rodinami IP adres není povoleno a vede k selhání vyhodnocení.

  Podporované formáty:
  - Jedna IP adresa (příklady: `10.0.0.0` , `2001:0DB8::3:FFFE` )
  - Rozsah CIDR (příklady: `10.0.0.0/24` , `2001:0DB8::/110` )
  - Rozsah definovaný počátečními a koncovými IP adresami (příklady: `192.168.0.1-192.168.0.9` , `2001:0DB8::-2001:0DB8::3:FFFF` )

- `current(indexName)`
  - Speciální funkce, která se dá použít jenom ve [výrazech Count](#count).

#### <a name="policy-function-example"></a>Příklad funkce zásad

Tato ukázka pravidla zásad používá `resourceGroup` funkci prostředků k získání vlastnosti **názvu** v kombinaci s `concat` funkcí Array a Object k sestavení `like` podmínky, která vynutila název prostředku, aby začínal názvem skupiny prostředků.

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

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Snímek obrazovky s příponou Azure Policy pro Visual Studio Code najetí myší na vlastnost, která zobrazuje názvy aliasů." border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Chcete-li najít aliasy, které lze použít s efektem [úprav](./effects.md#modify) , použijte následující příkaz v Azure PowerShell **4.6.0** nebo vyšší:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

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
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Princip aliasu [*]

Několik dostupných aliasů má verzi, která se zobrazí jako název Normal (normální) a další, která je **\[\*\]** k ní připojená. Například:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias ' Normal ' představuje pole jako jedinou hodnotu. Toto pole je určeno pro přesné scénáře porovnání shody, pokud celá sada hodnot musí být přesně definovaná, a ne více a méně.

**\[\*\]** Alias představuje kolekci hodnot vybraných z prvků vlastnosti prostředku pole. Například:

| Alias | Vybrané hodnoty |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | Prvky `ipRules` pole. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | Hodnoty `action` vlastnosti z každého elementu `ipRules` pole. |

Při použití v podmínce [pole](#fields) umožňují aliasy pole porovnat jednotlivé prvky pole s cílovou hodnotou. Při použití s výrazem [Count](#count) je možné:

- Ověřte velikost pole.
- Zkontroluje, jestli all\any\none prvků pole splňují komplexní podmínku.
- Zkontroluje, jestli přesně ***n*** prvků pole neodpovídají složitou podmínku.

Další informace a příklady naleznete v tématu [odkazující na vlastnosti prostředku pole](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

## <a name="next-steps"></a>Další kroky

- Zobrazení [struktury definice iniciativy](./initiative-definition-structure.md)
- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
