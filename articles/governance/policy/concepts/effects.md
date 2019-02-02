---
title: Vysvětlení fungování efekty
description: Azure definice zásady mají různé účinky, které určují způsob správy a hlásí dodržování předpisů.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: cf30d5dd8648a2b1da3f4a40399376182bf342c4
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562296"
---
# <a name="understand-policy-effects"></a>Vysvětlení efektu služby Policy

Každá definice zásady ve službě Azure Policy obsahuje jediný efekt. Tento efekt Určuje, co se stane, když se pravidlo zásad vyhodnotí tak, aby odpovídaly. Účinky chovat jinak, pokud jsou pro nový prostředek, prostředek aktualizované nebo existující prostředek.

Aktuálně existuje šest efekty, které jsou podporovány v definici zásad:

- Připojit
- Auditování
- AuditIfNotExists
- Odepřít
- DeployIfNotExists
- Zakázáno

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Požadavky na vytvoření nebo aktualizace prostředku prostřednictvím Azure Resource Manageru se vyhodnocují zásady nejprve. Zásady vytvoří seznam všech přiřazení, které použít na prostředek a potom je vyhodnocen jako prostředek pro každou definici. Zásady zpracuje několik efektů ještě před požadavek na příslušné poskytovatele prostředků. Tím zabráníte zbytečnému zpracování přes poskytovatele prostředků, pokud prostředek nesplňuje ovládací prvky zásad navržených zásad správného řízení.

- **Zakázané** je nejprve zkontrolována k určení, pokud by se mělo vyhodnotit pravidlo zásad.
- **Připojit** se pak vyhodnocuje. Od té doby připojení může změnit požadavek, změny provedené pomocí připojení může zabránit auditu nebo odepřít efekt spouštět.
- **Odepřít** se pak vyhodnocuje. Vyhodnocením odepření před auditu, je zabráněno double protokolování nežádoucí prostředku.
- **Audit** se pak vyhodnocuje před požadavkem na poskytovateli prostředků.

Po poskytovatele prostředků se vrátí kód úspěšnosti, **AuditIfNotExists** a **DeployIfNotExists** hodnocení k určení, pokud je potřeba další dodržování předpisů protokolování nebo akce.

## <a name="disabled"></a>Zakázáno

Tento efekt je užitečné pro testování situace nebo pokud má definice zásad parametrizované efekt. Díky této flexibilitě umožňuje zakázat jednotné přiřazení namísto zakázání všech přiřazení této zásady.

## <a name="append"></a>Připojit

Připojte se používá k přidání další pole k požadovanému prostředku během vytváření nebo aktualizace. Běžným příkladem je přidávání klíčových slov na prostředcích, třeba costCenter nebo povoleno zadat IP adresy pro prostředek úložiště.

### <a name="append-evaluation"></a>Přidat hodnocení

Připojit vyhodnotí jako předtím, než požadavek zpracuje přes poskytovatele prostředků během vytváření nebo aktualizaci prostředku. Připojit přidá pole do zdroje při **Pokud** je splněna podmínka pravidla zásad. Je-li přidat efekt by se mělo přepsat hodnotu v původní požadavek s jinou hodnotou, funguje jako efektu zamítnutí a žádost odmítne. Chcete-li přidat novou hodnotu pro existující pole, použijte **[\*]** verzi alias.

Při spuštění definice zásady pomocí efekt připojit jako součást cyklu hodnocení neprovede změny na prostředky, které už existují. Místo toho označí jakémukoli prostředku, který splňuje **Pokud** podmínka vyhodnocena jako nedodržující předpisy.

### <a name="append-properties"></a>Vlastnosti připojení

Přidat efekt má jenom **podrobnosti** pole, které je potřeba. Jako **podrobnosti** je pole, může trvat buď jeden **pole/hodnota** pár nebo násobky. Odkazovat na [struktura definic](definition-structure.md#fields) pro seznam polí, přípustné.

### <a name="append-examples"></a>Přidat příklady

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

Příklad 2: Dvě **pole/hodnota** páry připojit sady značek.

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

Příklad 3: Jeden **pole/hodnota** párovat pomocí non -**[\*]**
[alias](definition-structure.md#aliases) s polem **hodnota** nastavit pravidla IP účet úložiště. Když non -**[\*]** alias je pole, připojí vliv **hodnota** jako celého pole. Pokud pole již existuje, dojde k události odepřít z konflikt.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Příklad 4: Jeden **pole/hodnota** párovat pomocí **[\*]** [alias](definition-structure.md#aliases) s polem **hodnotu** nastavování pravidel IP pro účet úložiště. S použitím **[\*]** připojí alias, vliv **hodnota** na potenciálně existující pole. Pokud pole není ještě neexistuje, vytvoří se.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>Odepřít

Odepřít se používá při prevenci požadavkem na prostředky, které neodpovídá definované standardů prostřednictvím definice zásad a požadavek selže.

### <a name="deny-evaluation"></a>Odepřít hodnocení

Když se vytváří nebo aktualizuje prostředek odpovídající odepřít brání žádost před odesláním u poskytovatele prostředků. Žádost se vrátí jako `403 (Forbidden)`. Na portálu zakázáno zobrazením jako stav nasazení, které zabránily přiřazení zásady.

Během vyhodnocování stávající prostředky prostředky, které splňují definici zásady Odepřít jsou označeny jako nedodržující předpisy.

### <a name="deny-properties"></a>Odepřít vlastnosti

Efektu zamítnutí nemá žádné další vlastnosti pro použití v **pak** podmínku definice zásady.

### <a name="deny-example"></a>Odepřít příklad

Příklad: Použití efektu zamítnutí.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditování

Audit se používá k vytvoření upozorňovací událost v protokolu aktivit při vyhodnocování neodpovídajících prostředků, ale nezastaví požadavku.

### <a name="audit-evaluation"></a>Auditovat hodnocení

Auditování je poslední efekt ověřena pomocí zásad během vytvoření či aktualizaci prostředku. Zásady pak odešle prostředku u poskytovatele prostředků. Audit funguje stejně v případě požadavkem na prostředky a cyklu hodnocení. Zásady se přidá `Microsoft.Authorization/policies/audit/action` operace do protokolu aktivit a označí prostředků jako nedodržující předpisy.

### <a name="audit-properties"></a>Vlastnosti auditu

Audit účinek nemá žádné další vlastnosti pro použití v **pak** podmínku definice zásady.

### <a name="audit-example"></a>Příklad auditu

Příklad: Použití efektu auditu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists povolí auditování pro prostředky, které odpovídají **Pokud** podmínky, ale není součástí zadané v **podrobnosti** z **pak** podmínku.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists hodnocení

AuditIfNotExists běží po poskytovatele prostředků byla zpracována žádost o vytvoření nebo aktualizace prostředků a vrátil stavový kód úspěchu. Audit nastane, pokud neexistují žádné související prostředky nebo pokud prostředky definované **ExistenceCondition** není vyhodnocen na hodnotu true. Přidá zásad `Microsoft.Authorization/policies/audit/action` operace na aktivitu protokolu stejným způsobem jako audit vliv. Když se aktivuje, prostředek, který splnil **Pokud** podmínka je prostředek, který je označeno jako nedodržující předpisy.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

**Podrobnosti** vlastnost efektů AuditIfNotExists má všechny podvlastnosti, které definují související prostředky tak, aby odpovídaly.

- **Typ** [povinné]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Začne pokusu o načtení prostředku pod **Pokud** stavu prostředků a potom dotazy v rámci stejné skupině prostředků jako **Pokud** podmínka vyhodnocena jako prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady pro načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
- **Název skupiny prostředků** (volitelné)
  - Umožňuje odpovídající související prostředek, který pochází z jiné skupiny prostředků.
  - Neplatí, pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Výchozí hodnota je **Pokud** podmínka vyhodnocena jako skupinu prostředků.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví rozsah, kam načíst související prostředků tak, aby odpovídaly z.
  - Neplatí, pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Pro _ResourceGroup_, omezí na **Pokud** skupiny prostředků podmínku prostředek nebo skupina prostředků zadaná v **ResourceGroupName**.
  - Pro _předplatné_, dotazuje celé předplatné pro související prostředek.
  - Výchozí hodnota je _ResourceGroup_.
- **ExistenceCondition** (volitelné)
  - Pokud není zadán, všechna související prostředek **typ** splňuje účinek a neaktivuje auditu.
  - Používá stejný jazyk jako pravidlo zásad pro **Pokud** podmínky, ale se vyhodnocují zvlášť pro jednotlivé související prostředky.
  - Pokud žádné odpovídající prostředek související vyhodnotí jako true, považuje účinek a neaktivuje auditu.
  - [Field()] ke kontrole můžete použít porovnávání s hodnotami v **Pokud** podmínku.
  - Může například použít k ověření, který nadřazený prostředek (v **Pokud** podmínky) je ve stejném umístění jako odpovídající prostředek související prostředek.

### <a name="auditifnotexists-example"></a>Příklad AuditIfNotExists

Příklad: Vyhodnotí virtuálními počítači zjistěte Pokud Antimalwarové rozšíření neexistuje, Audituje v případě, že chybí.

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

Podobně jako AuditIfNotExists DeployIfNotExists provede nasazení šablony při je splněná podmínka.

> [!NOTE]
> [Vnořené šablony](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) podporují **deployIfNotExists**, ale [propojené šablony](../../../azure-resource-manager/resource-group-linked-templates.md) se momentálně nepodporují.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists hodnocení

DeployIfNotExists běží po poskytovatele prostředků byla zpracována žádost o vytvoření nebo aktualizace prostředků a vrátil stavový kód úspěchu. Nasazení šablony nastane, pokud neexistují žádné související prostředky nebo pokud prostředky definované **ExistenceCondition** není vyhodnocen na hodnotu true.

Během cyklu vyhodnocení definice zásad s účinností DeployIfNotExists, které odpovídají prostředky jsou označeny jako nedodržující předpisy, ale na tento prostředek nebyla provedena žádná akce.

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

**Podrobnosti** má vlastnost účinky DeployIfNotExists všechny podvlastnosti, které definují související prostředky tak, aby shodu a ke spuštění nasazení šablony.

- **Typ** [povinné]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Začne pokusu o načtení prostředku pod **Pokud** stavu prostředků a potom dotazy v rámci stejné skupině prostředků jako **Pokud** podmínka vyhodnocena jako prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady pro načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
- **Název skupiny prostředků** (volitelné)
  - Umožňuje odpovídající související prostředek, který pochází z jiné skupiny prostředků.
  - Neplatí, pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Výchozí hodnota je **Pokud** podmínka vyhodnocena jako skupinu prostředků.
  - Pokud je proveden nasazení šablony, se nasadí ve skupině prostředků z této hodnoty.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví rozsah, kam načíst související prostředků tak, aby odpovídaly z.
  - Neplatí, pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Pro _ResourceGroup_, omezí na **Pokud** skupiny prostředků podmínku prostředek nebo skupina prostředků zadaná v **ResourceGroupName**.
  - Pro _předplatné_, dotazuje celé předplatné pro související prostředek.
  - Výchozí hodnota je _ResourceGroup_.
- **ExistenceCondition** (volitelné)
  - Pokud není zadán, všechna související prostředek **typ** splňuje účinek a neaktivuje nasazení.
  - Používá stejný jazyk jako pravidlo zásad pro **Pokud** podmínky, ale se vyhodnocují zvlášť pro jednotlivé související prostředky.
  - Pokud žádné odpovídající prostředek související vyhodnotí jako true, považuje účinek a neaktivuje nasazení.
  - [Field()] ke kontrole můžete použít porovnávání s hodnotami v **Pokud** podmínku.
  - Může například použít k ověření, který nadřazený prostředek (v **Pokud** podmínky) je ve stejném umístění jako odpovídající prostředek související prostředek.
- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí obsahovat pole řetězců, které odpovídají ID role řízení přístupu na základě role přístupné předplatné. Další informace najdete v tématu [nápravy - nakonfigurovat definici zásady](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví typ nasazení se dá spouštět. _Předplatné_ označuje [nasazení na úrovni předplatného](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ indikuje, že nasazení pro skupinu prostředků.
  - A _umístění_ v musí být zadaná vlastnost _nasazení_ při použití úrovně nasazení předplatných.
  - Výchozí hodnota je _ResourceGroup_.
- **Nasazení** [povinné]
  - Tato vlastnost by měla obsahovat úplnou šablonu nasazení, jako by byly předány `Microsoft.Resources/deployments` UMÍSTIT rozhraní API. Další informace najdete v tématu [rozhraní REST API pro nasazení](/rest/api/resources/deployments).

  > [!NOTE]
  > Všechny funkce uvnitř **nasazení** vlastností se vyhodnocují jako součásti šablony, nikoli zásady. Výjimkou je **parametry** vlastnost, která předává hodnoty ze zásad do šablony. **Hodnotu** v této části šablony název parametru slouží k provádění tuto hodnotu předá (viz _fullDbName_ v příkladu DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Příklad DeployIfNotExists

Příklad: Vyhodnotí jako databáze SQL serveru k určení, zda je povoleno transparentDataEncryption. Pokud ne, pak spuštění nasazení povolit.

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
        "roleDefinitionIds": [
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
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

## <a name="layering-policies"></a>Zásady vrstvení

Prostředek může být ovlivněno několik přiřazení. Tato přiřazení může být ve stejném oboru nebo v různých oborech. Každá z těchto přiřazení je pravděpodobně efektu různé definice. Nezávisle na sobě se vyhodnocuje podmínku a účinnosti pro všechny zásady. Příklad:

- Zásady 1
  - Omezuje umístění zdroje pro 'westus'
  - Přiřadit k předplatnému A
  - Odepřít efekt
- Zásady 2
  - Omezuje umístění zdroje pro 'eastus.
  - Přiřazeno předplatné A skupinu prostředků B
  - Auditu
  
Toto nastavení by výsledek je následující:

- Prostředek už ve skupině prostředků B v 'eastus' je k zásadám 2 kompatibilní a nekompatibilní zásady 1
- Nekompatibilní zásady 2 a nekompatibilní zásady 1 není-li v 'westus' je některý z prostředků už ve skupině prostředků B nejsou v "eastus.
- Nové prostředky v předplatném A není v 'westus' byl odepřen zásadami 1
- Nové prostředky v předplatném A a B skupině prostředků v 'westus' je vytvořený a nedodržující předpisy na zásadu 2

Pokud měl projeví i zásady 1 a 2 deny, situace se změní na:

- Nekompatibilní zásady 2 je některý z prostředků už ve skupině prostředků B nejsou v "eastus.
- Prostředek už ve skupině prostředků B není v 'westus' je nekompatibilní zásady 1
- Nové prostředky v předplatném A není v 'westus' byl odepřen zásadami 1
- Žádné nový prostředek ve skupině prostředků B a předplatného je odepřen.

Vyhodnotí se jednotlivě každé přiřazení. V důsledku toho není příležitost pro prostředek do listu prostřednictvím mezera z rozdíly v oboru. Net výsledek vrstvení zásady nebo zásady překrývají se považuje za **kumulativní nejvíce omezující**. Jako příklad pokud obě zásady 1 a 2 efektu zamítnutí prostředku by se zablokovaly překrývající se a konfliktní zásady. Pokud stále potřebujete prostředku vytvořené v cílový obor, zkontrolujte vyloučení na každé přiřazení k ověření správné zásady mají vliv na správné rozsahy.

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md)
- Zkontrolujte [struktura definic zásad](definition-structure.md)
- Pochopit postup [programové vytváření zásad](../how-to/programmatically-create.md)
- Zjistěte, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md)
- Zjistěte, jak [opravit nekompatibilní prostředky](../how-to/remediate-resources.md)
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../../management-groups/overview.md)
