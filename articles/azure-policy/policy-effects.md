---
title: Principy Azure zásad efekty
description: Azure definice zásady mají různé důsledky, které určují, jak je dodržování předpisů spravovaná a ohlásil.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 23bbbe9cf86268f93ae1f8fcec9303efa8a673de
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796712"
---
# <a name="understanding-policy-effects"></a>Principy účinky zásad

Definice jednotlivých zásad v zásadách Azure má efekt jeden, který určuje, co se stane, že během skenování, kdy **Pokud** segment pravidla zásad vyhodnotí tak, aby odpovídaly prostředek se má zkontrolovat. Důsledky můžete také chovat jinak v případě, že jsou pro nový prostředek, prostředek aktualizované nebo existující prostředek.

Aktuálně neexistují pět důsledky, které jsou podporovány v definici zásady:

- Připojit
- Auditování
- AuditIfNotExists
- Odepřít
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Při požadavku Vytvořit nebo aktualizovat prostředek prostřednictvím Správce Azure Resource Manager, zásady zpracují řadu důsledky před blokováním požadavek do příslušného poskytovatele prostředků.
Tím zabráníte zbytečnému zpracování poskytovatelem prostředků, pokud prostředek nesplňuje ovládací prvky navrženou zásad správného řízení zásad. Zásady vytvoří seznam všechny definice zásady přiřazené zásady nebo iniciativy přiřazení, které se vztahují oborem (minus vyloučení) k prostředku a připraví vyhodnotit prostředků u jednotlivých definice.

- **Připojit** je první vyhodnocen. Protože připojit může změnit žádost, změny provedené pomocí připojení může zabránit auditování nebo odepřít vliv ze spuštění.
- **Odepřít** pak vyhodnotí. Vyhodnocením odepřete před auditu, nemůže být dvojité protokolování nežádoucí prostředku.
- **Audit** vyhodnocována před žádost přejdete k poskytovateli prostředků.

Jakmile požadavek je zadán pro poskytovatele prostředků a poskytovatele prostředků vrátí stavový kód úspěch, **AuditIfNotExists** a **DeployIfNotExists** vyhodnocují k určení, zda následné vyžaduje se dodržování předpisů protokolování nebo akce.

## <a name="append"></a>Připojit

Připojit se používá k přidání další pole na požadovaný prostředek během vytvoření či aktualizaci. Může být užitečná pro přidání značek na prostředky, jako je například costCenter nebo povoleno zadat IP adresy pro prostředek úložiště.

### <a name="append-evaluation"></a>Append – vyhodnocení

Jak je uvedeno, připojit vyhodnotí před požadavek zpracovává poskytovatelem prostředků při vytváření nebo aktualizaci prostředku. Připojit přidá polí k prostředku při **Pokud** je splněna podmínka pravidla zásad. Pokud platnost připojení by se mělo přepsat hodnotu v původní žádost s jinou hodnotou, funguje jako vliv Odepřít a odmítnout žádosti.

Při spuštění definici zásady pomocí připojení účinek jako součást cyklus hodnocení ho neprovede změny na prostředky, které již existují. Místo toho označuje jakémukoli prostředku, který splňuje **Pokud** podmínka vyhodnocena jako nekompatibilní.

### <a name="append-properties"></a>Append – vlastnosti

Má vliv připojit jenom **podrobnosti** pole, která je vyžadována. Jako **podrobnosti** je pole, může trvat buď jediný **pole/hodnota** pár nebo násobky. Odkazovat na [definice zásady](policy-definition.md#fields) pro seznam polí, přípustné.

### <a name="append-examples"></a>Připojit příklady

Příklad 1: Jeden **pole/hodnota** pár připojit značku.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Příklad 2: Více **pole/hodnota** páry připojit sadu značky.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Příklad 3: Jeden **pole/hodnota** párovat pomocí [alias](policy-definition.md#aliases) s pole **hodnotu** nastavování pravidel IP pro účet úložiště.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

## <a name="deny"></a>Odepřít

Odepřít se používá při prevenci požadavek na prostředek, který neodpovídá požadované standardy prostřednictvím definic zásad a požadavek se nezdaří.

### <a name="deny-evaluation"></a>Odepřít vyhodnocení

Při vytváření nebo aktualizaci prostředku, odepřít brání požadavku před odesílána poskytovatele prostředků. Žádost se vrátí jako 403 (zakázáno). Na portálu lze zobrazit zakázáno jako stav na nasazení, které bylo ukončeno z důvodu přiřazení zásad.

Cyklu hodnocení definice zásady s odepřít vliv odpovídající prostředky jsou označeny jako nedodržující předpisy, ale na tento prostředek je provedena žádná akce.

### <a name="deny-properties"></a>Odepřít vlastnosti

Odepřít účinek nemá žádné další vlastnosti pro použití v **pak** podmínku definice zásady.

### <a name="deny-example"></a>Odepřít příklad

Příklad: Použití odepřít účinek.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditování

Vliv auditu je používá k vytvoření upozorňovací událost v protokolu auditování, když vyhodnotí nevyhovující prostředků, ale nezastaví žádosti.

### <a name="audit-evaluation"></a>Vyhodnocení auditu

Účinek auditu je poslední spuštění při vytvoření nebo aktualizace prostředku před prostředku je přenášená k poskytovateli prostředků. Audit funguje stejně v případě žádost prostředků a cyklus hodnocení a spustí `Microsoft.Authorization/policies/audit/action` operace protokolu aktivit. V obou případech prostředku je označena jako nevyhovující.

### <a name="audit-properties"></a>Vlastnosti auditu

Audit účinek nemá žádné další vlastnosti pro použití v **pak** podmínku definice zásady.

### <a name="audit-example"></a>Příklad auditu

Příklad: Pomocí účinek auditu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umožňuje auditování na prostředek, který odpovídá **Pokud** podmínky, ale nemá zadaný v součásti **podrobnosti** z **pak** podmínku.

### <a name="auditifnotexists-evaluation"></a>Vyhodnocení AuditIfNotExists

AuditIfNotExists spustí po poskytovatel prostředků má zpracovat žádost o vytvoření nebo aktualizace na prostředek a vrátil kód stavu úspěch. Účinek se aktivuje, pokud nejsou žádné související prostředky nebo pokud definované prostředky **ExistenceCondition** nevyhodnocují na hodnotu true. Když se aktivuje účinek `Microsoft.Authorization/policies/audit/action` provedení operace v protokolu aktivit stejným způsobem jako účinek auditu. Při aktivaci, prostředků, která by uspokojila **Pokud** podmínka je prostředek, který je označen jako nevyhovující.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

**Podrobnosti** všechny podvlastnosti, které definují související prostředky tak, aby odpovídaly má vlastnost AuditIfNotExists důsledky.

- **Typ** [vyžaduje]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Spustí na pokusu o načtení prostředku pod **Pokud** podmínku prostředků a potom dotazů v rámci stejné skupině prostředků jako **Pokud** podmínky prostředků.
- **název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady tak, aby načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
- **Název skupiny prostředků** (volitelné)
  - Umožňuje shodu související prostředek pocházet z jiné skupině prostředků.
  - Není platné, pokud **typ** je na prostředek, který by bylo pod **Pokud** podmínky prostředků.
  - Výchozí hodnota je **Pokud** podmínky prostředku skupiny prostředků.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví rozsah, přičemž se načíst související prostředek tak, aby odpovídaly z.
  - Není platné, pokud **typ** je na prostředek, který by bylo pod **Pokud** podmínky prostředků.
  - Pro _ResourceGroup_, by omezit **Pokud** podmínku prostředků skupiny prostředků nebo skupinu prostředků, zadaný v **ResourceGroupName**.
  - Pro _předplatné_, dotazuje celé předplatné pro související prostředek.
  - Výchozí hodnota je _ResourceGroup_.
- **ExistenceCondition** (volitelné)
  - Pokud není zadaný, všechny související prostředek **typ** splňuje účinek a neaktivuje auditu.
  - Používá stejný jazyk jako pravidlo zásad **Pokud** podmínky, ale je porovnán s všechny související prostředky jednotlivě.
  - Pokud žádný odpovídající prostředek, související se vyhodnotí jako true, je splněna účinek a neaktivuje auditu.
  - [Field()] můžete zkontrolovat pomocí ekvivalenční s hodnotami v **Pokud** podmínku.
  - Může například použít k ověření, že nadřazený prostředek (v **Pokud** podmínku) je ve stejném umístění prostředků jako odpovídající související prostředek.

### <a name="auditifnotexists-example"></a>Příklad AuditIfNotExists

Příklad: Vyhodnotí virtuálními počítači zjistěte, pokud rozšíření antimalwarových existuje poté audity v případě, že chybí.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Podobně jako u AuditIfNotExists, DeployIfNotExists spustí nasazení šablony při splnění této podmínky.

### <a name="deployifnotexists-evaluation"></a>Vyhodnocení DeployIfNotExists

DeployIfNotExists také spustí po poskytovatele prostředků byla zpracována vytvoření nebo aktualizace požadavek na prostředek a vrátil kód stavu úspěch. Účinek se aktivuje, pokud nejsou žádné související prostředky nebo pokud definované prostředky **ExistenceCondition** nevyhodnocují na hodnotu true. Když se aktivuje účinek se spustí nasazení šablony.

Cyklu hodnocení definice zásady s DeployIfNotExists vliv odpovídající prostředky jsou označeny jako nedodržující předpisy, ale na tento prostředek je provedena žádná akce.

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

**Podrobnosti** má vlastnost důsledky DeployIfNotExists všechny podvlastnosti, které definují související prostředky tak, aby shodu a šablony nasazení provést.

- **Typ** [vyžaduje]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Spustí na pokusu o načtení prostředku pod **Pokud** podmínku prostředků a potom dotazů v rámci stejné skupině prostředků jako **Pokud** podmínky prostředků.
- **název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady tak, aby načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
- **Název skupiny prostředků** (volitelné)
  - Umožňuje shodu související prostředek pocházet z jiné skupině prostředků.
  - Není platné, pokud **typ** je na prostředek, který by bylo pod **Pokud** podmínky prostředků.
  - Výchozí hodnota je **Pokud** podmínky prostředku skupiny prostředků.
  - Pokud je proveden nasazení šablony, je nasazená ve skupině prostředků této hodnoty.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví rozsah, přičemž se načíst související prostředek tak, aby odpovídaly z.
  - Není platné, pokud **typ** je na prostředek, který by bylo pod **Pokud** podmínky prostředků.
  - Pro _ResourceGroup_, by omezit **Pokud** podmínku prostředků skupiny prostředků nebo skupinu prostředků, zadaný v **ResourceGroupName**.
  - Pro _předplatné_, dotazuje celé předplatné pro související prostředek.
  - Výchozí hodnota je _ResourceGroup_.
- **ExistenceCondition** (volitelné)
  - Pokud není zadaný, všechny související prostředek **typ** splňuje účinek a neaktivuje nasazení.
  - Používá stejný jazyk jako pravidlo zásad **Pokud** podmínky, ale je porovnán s všechny související prostředky jednotlivě.
  - Pokud žádný odpovídající prostředek, související se vyhodnotí jako true, je splněna účinek a neaktivuje nasazení.
  - [Field()] můžete zkontrolovat pomocí ekvivalenční s hodnotami v **Pokud** podmínku.
  - Může například použít k ověření, že nadřazený prostředek (v **Pokud** podmínku) je ve stejném umístění prostředků jako odpovídající související prostředek.
- **Nasazení** [vyžaduje]
  - Tato vlastnost musí obsahovat úplnou šablonu nasazení, jako by byly předány `Microsoft.Resources/deployments` PUT rozhraní API. Další informace najdete v tématu [nasazení REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Všechny funkce uvnitř **nasazení** vlastnost, jsou vyhodnoceny jako součásti šablony, nikoli zásady. Jedinou výjimkou je **parametry** vlastnost, která předá hodnoty ze zásad do šablony. **Hodnotu** v této části z této šablony název parametru slouží k provádění tuto hodnotu předávání (viz _fullDbName_ v příkladu DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Příklad DeployIfNotExists

Příklad: Vyhodnotí databáze systému SQL Server k určení, zda je povoleno transparentDataEncryption. Pokud ne, pak se spustí nasazení povolit.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>Rozvrstvení zásady

Prostředek může být ovlivněno více přiřazení. Tato přiřazení může být ve stejném oboru (konkrétní zdroj, skupinu prostředků, předplatné nebo skupinu pro správu) nebo na různých místech. Každá z těchto přiřazení je pravděpodobně jiný dopad definované. Bez ohledu na to podmínku a vliv pro každou zásadu (přiřazen přímo, nebo jako součást initiative) nezávisle vyhodnotí. Například pokud má zásada 1 podmínku, která omezuje umístění pro předplatné A z vytváří v 'westus' s efekt Odepřít a zásad 2, které omezují prostředky v prostředku skupiny B (což je v rámci předplatného A) z vytváří v 'eastus' s auditu vliv obě přidělenou, bude výsledný výsledek:

- Všechny prostředek už ve skupině prostředků B v 'eastus' je vyhovující zásadám 2, ale označena jako nevyhovující zásady 1.
- Jakémukoli prostředku, už ve skupině prostředků B není v 'eastus, budou označeny jako nevyhovující zásady 2 a by také být označeny není kompatibilní pro zásady 1, pokud ne, westus'.
- Všechny nové prostředků v předplatném A v 'westus' by odepřen zásadami 1.
- Všechny nové prostředků v předplatném A / B skupiny prostředků v 'westus' by být označen jako nedodržující předpisy na zásady 2, ale by se vytvořily (kompatibilní do zásad 1 a 2 je auditovat a není Odepřít).

Pokud měl ovlivňuje zásady 1 a 2 zásady z odepřít, situaci změní na:

- Jakémukoli prostředku, už ve skupině prostředků B není v 'eastus, budou označeny jako nevyhovující zásady 2.
- Jakémukoli prostředku, už ve skupině prostředků B není v 'westus, budou označeny jako nevyhovující zásady 1.
- Všechny nové prostředků v předplatném A v 'westus' by odepřen zásadami 1.
- Všechny nové prostředků v předplatném A / skupiny prostředků B by byla odepřena (protože jeho umístění může nikdy splňují zásady 1 a 2 zásady).

Protože každé přiřazení jednotlivě vyhodnotí, není k dispozici možnost pro prostředek do listu prostřednictvím mezera kvůli rozdíly v oboru. Proto se považuje za net výsledek rozvrstvení zásady nebo zásad překrývají **kumulativní nejvíc omezující**. Jinými slovy prostředek, který má být vytvořen mohla být zablokována kvůli překrývajících se a konfliktní zásady, třeba v předchozím příkladu, pokud zásady 1 a 2 zásad měli mít nežádoucí vliv odepřít. Pokud stále potřebujete prostředků, které byly vytvořeny v cílový obor, zkontrolujte vyloučení na každé přiřazení zajistit že správné zásady mají vliv na správné obory.

## <a name="next-steps"></a>Další postup

Teď, když máte lepší představu o důsledcích definice zásady, projděte si ukázky zásad:

- Přečtěte si další příklady v [ukázky zásad Azure](json-samples.md).
