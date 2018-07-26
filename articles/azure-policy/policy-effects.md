---
title: Principy Azure Policy efekty
description: Azure definice zásady mají různé účinky, které určují způsob správy a hlásí dodržování předpisů.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 17ad631e2441e4b8d6314557c17be143fd2f3de0
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248722"
---
# <a name="understanding-policy-effects"></a>Principy zásad efekty

Každá definice zásady ve službě Azure Policy obsahuje jediný efekt, který určuje, co se stane při skenování, kdy **Pokud** segmentu pravidla zásad se vyhodnocuje tak, aby odpovídaly prostředek se má zkontrolovat. Účinky mohou také chovat odlišně, pokud jsou pro nový prostředek, prostředek aktualizované nebo existující prostředek.

Aktuálně nejsou pět efekty, které jsou podporovány v definici zásad:

- Připojit
- Auditování
- AuditIfNotExists
- Odepřít
- DeployIfNotExists (dostupné pouze pro **integrované** zásady)

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Po provedení požadavku se vytvořit nebo aktualizovat prostředek prostřednictvím Azure Resource Manageru, zásada zpracovává několik efektů před předání požadavku příslušné poskytovatele prostředků.
Tím zabráníte zbytečnému zpracování přes poskytovatele prostředků, pokud prostředek nesplňuje ovládací prvky zásad navržených zásad správného řízení. Zásady vytvoří seznam všech definic zásad přiřazené zásady nebo přiřazení iniciativy, které použít obor (bez vyloučení) na prostředek a připraví vyhodnotit prostředků pro každou definici.

- **Připojit** je vyhodnocen jako první. Od té doby připojení může změnit požadavek, změny provedené pomocí připojení může zabránit auditu nebo odepřít efekt spouštět.
- **Odepřít** se pak vyhodnocuje. Vyhodnocením odepření před auditu, je zabráněno double protokolování nežádoucí prostředku.
- **Audit** se pak vyhodnocuje před požadavek na poskytovateli prostředků.

Jakmile se žádost je k dispozici u poskytovatele prostředků a poskytovatele prostředků se vrátí stavový kód úspěchu **AuditIfNotExists** a **DeployIfNotExists** vyhodnocují k určení, zda zpracování je požadováno protokolování dodržování předpisů nebo akce.

## <a name="append"></a>Připojit

Připojte se používá k přidání další pole k požadovanému prostředku během vytváření nebo aktualizace. Může být užitečné pro přidání značek na prostředcích, třeba costCenter nebo povoleno zadat IP adresy pro prostředek úložiště.

### <a name="append-evaluation"></a>Přidat hodnocení

Jak už bylo zmíněno, připojte vyhodnotí před žádost zpracována podle poskytovatele prostředků během vytváření nebo aktualizaci prostředku. Připojit přidá pole do zdroje při **Pokud** je splněna podmínka pravidla zásad. Je-li přidat efekt by se mělo přepsat hodnotu v původní požadavek s jinou hodnotou, funguje jako efektu zamítnutí a zamítnutí žádosti.

Při spuštění definice zásady pomocí efekt připojit jako součást cyklu hodnocení se neprovede změny na prostředky, které už existují. Místo toho označí jakémukoli prostředku, který splňuje **Pokud** podmínka vyhodnocena jako nedodržující předpisy.

### <a name="append-properties"></a>Vlastnosti připojení

Přidat efekt má jenom **podrobnosti** pole, které je potřeba. Jako **podrobnosti** je pole, může trvat buď jeden **pole/hodnota** pár nebo násobky. Odkazovat na [definice zásady](policy-definition.md#fields) pro seznam polí, přípustné.

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

Příklad 2: Více **pole/hodnota** páry připojit sady značek.

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

Příklad 3: Jeden **pole/hodnota** párovat pomocí [alias](policy-definition.md#aliases) s polem **hodnotu** nastavování pravidel IP pro účet úložiště.

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

## <a name="deny"></a>Odepřít

Odepřít se používá při prevenci požadavkem na prostředky, který neodpovídá požadované standardy pomocí definice zásad a požadavek selže.

### <a name="deny-evaluation"></a>Odepřít hodnocení

Když se vytváří nebo aktualizuje prostředek, odepření brání požadavku před odesílání u poskytovatele prostředků. Žádost se vrátí jako 403 (zakázáno). Na portálu zakázáno zobrazením jako stav nasazení, které nebylo možné kvůli přiřazení zásady.

Během cyklu vyhodnocení definice zásad prostřednictvím efektu zamítnutí, které odpovídají prostředky jsou označeny jako nedodržující předpisy, ale na tento prostředek je provedena žádná akce.

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

Auditu se používá k vytvoření upozorňovací událost v protokolu aktivit, když se vyhodnotí jako nevyhovující prostředků, ale nezastaví požadavku.

### <a name="audit-evaluation"></a>Auditovat hodnocení

Efektem auditu je poslední spuštění během vytváření nebo aktualizace prostředku před prostředku je odeslána u poskytovatele prostředků. Audit funguje stejně v případě požadavkem na prostředky a cyklu hodnocení a spustí `Microsoft.Authorization/policies/audit/action` operace k protokolům aktivit. V obou případech se prostředek je označen jako nedodržující předpisy.

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

AuditIfNotExists povolí auditování pro prostředek, který odpovídá **Pokud** podmínky, ale nemá žádné součásti podle **podrobnosti** z **pak** podmínku.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists hodnocení

AuditIfNotExists se spustí poté, co byla zpracována žádost o vytvoření nebo aktualizace prostředku zprostředkovatele prostředků a vrátil stavový kód úspěchu. Účinek se aktivuje, pokud neexistují žádné související prostředky nebo pokud prostředky definované **ExistenceCondition** není vyhodnocen na hodnotu true. Když se aktivuje efekt, `Microsoft.Authorization/policies/audit/action` stejným způsobem jako audit efekt provedením operace k protokolům aktivit. Když se aktivuje, prostředek, který splnil **Pokud** podmínka je prostředek, který je označeno jako nedodržující předpisy.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

**Podrobnosti** vlastnost efektů AuditIfNotExists má všechny podvlastnosti, které definují související prostředky tak, aby odpovídaly.

- **Typ** [povinné]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Začne pokusu o načtení prostředku pod **Pokud** stavu prostředků a potom dotazy v rámci stejné skupině prostředků jako **Pokud** podmínka vyhodnocena jako prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady pro načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
- **Název skupiny prostředků** (volitelné)
  - Umožňuje odpovídající související prostředek, který pochází z jiné skupiny prostředků.
  - Doporučení se netýká Pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Výchozí hodnota je **Pokud** podmínka vyhodnocena jako skupinu prostředků.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví rozsah, kam načíst související prostředků tak, aby odpovídaly z.
  - Doporučení se netýká Pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
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

> [!WARNING]
> Je k dispozici jen DeployIfNotExists **integrované** zásady.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists hodnocení

DeployIfNotExists poběží i po ošetřila poskytovatele prostředků použít příkaz pro vytvoření nebo aktualizace požadavek na prostředek a vrátil stavový kód úspěchu. Účinek se aktivuje, pokud neexistují žádné související prostředky nebo pokud prostředky definované **ExistenceCondition** není vyhodnocen na hodnotu true. Když se aktivuje efekt, je proveden nasazení šablony.

Během cyklu vyhodnocení definice zásad s účinností DeployIfNotExists, které odpovídají prostředky jsou označeny jako nedodržující předpisy, ale na tento prostředek je provedena žádná akce.

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

**Podrobnosti** má vlastnost účinky DeployIfNotExists všechny podvlastnosti, které definují související prostředky tak, aby shodu a ke spuštění nasazení šablony.

- **Typ** [povinné]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Začne pokusu o načtení prostředku pod **Pokud** stavu prostředků a potom dotazy v rámci stejné skupině prostředků jako **Pokud** podmínka vyhodnocena jako prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady pro načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
- **Název skupiny prostředků** (volitelné)
  - Umožňuje odpovídající související prostředek, který pochází z jiné skupiny prostředků.
  - Doporučení se netýká Pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Výchozí hodnota je **Pokud** podmínka vyhodnocena jako skupinu prostředků.
  - Pokud je proveden nasazení šablony, se nasadí ve skupině prostředků z této hodnoty.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _ResourceGroup_.
  - Nastaví rozsah, kam načíst související prostředků tak, aby odpovídaly z.
  - Doporučení se netýká Pokud **typ** je prostředek, který by se nacházela pod složkou **Pokud** podmínka vyhodnocena jako prostředek.
  - Pro _ResourceGroup_, omezí na **Pokud** skupiny prostředků podmínku prostředek nebo skupina prostředků zadaná v **ResourceGroupName**.
  - Pro _předplatné_, dotazuje celé předplatné pro související prostředek.
  - Výchozí hodnota je _ResourceGroup_.
- **ExistenceCondition** (volitelné)
  - Pokud není zadán, všechna související prostředek **typ** splňuje účinek a neaktivuje nasazení.
  - Používá stejný jazyk jako pravidlo zásad pro **Pokud** podmínky, ale se vyhodnocují zvlášť pro jednotlivé související prostředky.
  - Pokud žádné odpovídající prostředek související vyhodnotí jako true, považuje účinek a neaktivuje nasazení.
  - [Field()] ke kontrole můžete použít porovnávání s hodnotami v **Pokud** podmínku.
  - Může například použít k ověření, který nadřazený prostředek (v **Pokud** podmínky) je ve stejném umístění jako odpovídající prostředek související prostředek.
- **Nasazení** [povinné]
  - Tato vlastnost by měl obsahovat úplnou šablonu nasazení, jako by byly předány `Microsoft.Resources/deployments` UMÍSTIT rozhraní API. Další informace najdete v tématu [rozhraní REST API pro nasazení](/rest/api/resources/deployments).

  > [!NOTE]
  > Všechny funkce uvnitř **nasazení** vlastností se vyhodnocují jako součásti šablony, nikoli zásady. Výjimkou je **parametry** vlastnost, která předává hodnoty ze zásad do šablony. **Hodnotu** v této části šablony název parametru slouží k provádění tuto hodnotu předá (viz _fullDbName_ v příkladu DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Příklad DeployIfNotExists

Příklad: Vyhodnotí databáze systému SQL Server k určení, zda je povoleno transparentDataEncryption. Pokud ne, pak je nasazení, aby je proveden.

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

## <a name="layering-policies"></a>Zásady vrstvení

Prostředek může být ovlivněno více přiřazení. Tato přiřazení může být ve stejném oboru (konkrétní prostředek, skupinu prostředků, předplatné nebo skupinu pro správu) nebo v různých oborech. Každá z těchto přiřazení je pravděpodobně efektu různé definice. Bez ohledu na to stavu a účinnosti pro jednotlivé zásady (přiřadit přímo nebo v rámci iniciativy) nezávisle na sobě vyhodnocena. Například pokud má zásada 1 podmínku, která omezuje umístění prostředků pro předplatné A lze vytvořit pouze v 'westus' s efektu zamítnutí a zásady 2 má podmínku, která omezuje pouze umístění prostředku pro skupinu prostředků B (což je v předplatném A) do být vytvoří v "eastus" s efektem auditu jsou přiřazeny, by byl výsledný výsledek::

- Vyhovující zásada 2, ale označeno jako nedodržující předpisy na zásadách 1 je některý z prostředků už ve skupině prostředků B v "eastus".
- Prostředek už ve skupině prostředků B nejsou v "eastus" se označí jako nedodržující předpisy na zásady 2 a by být také označen nedodržující předpisy na zásadách 1, pokud není 'westus'.
- Všechny nové prostředků v předplatném A v 'westus' by odepřen zásadami 1.
- Všechny nové prostředků v předplatném A / B skupinu prostředků v 'westus' by označit jako nedodržující předpisy na zásadu 2, ale by se vytvořil (kompatibilní zásad 1 a 2 je auditovat a neodepírat).

Pokud měl projeví i zásady 1 a 2 deny, se změní situace:

- Prostředek už ve skupině prostředků B nejsou v "eastus" se označí jako nedodržující předpisy na zásady 2.
- Prostředek už ve skupině prostředků B není v 'westus' se označí jako nedodržující předpisy na zásadách 1.
- Všechny nové prostředků v předplatném A v 'westus' by odepřen zásadami 1.
- Všechny nové prostředků v předplatném A / prostředků – skupina B by byla odepřena (protože je jeho umístění může nikdy splňují zásady 1 a 2 zásady).

Jako jednotlivé přiřazení je jednotlivě vyhodnocen, není k dispozici možnost pro prostředek do listu pomocí mezer z důvodu rozdíly v oboru. Proto net výsledek vrstvení zásady nebo zásady překrývají se považuje za **kumulativní nejvíce omezující**. Jinými slovy prostředek, který chcete vytvořit mohla být zablokována kvůli překrývající se a konfliktní zásady, třeba v příkladu výše, pokud obě zásady 1 a 2 měli efektu zamítnutí. Pokud stále potřebujete prostředek, který má být vytvořen v cílový obor, zkontrolujte u každé přiřazení zajistit že správné zásady mají vliv na správné rozsahy vyloučení.

## <a name="next-steps"></a>Další postup

Teď, když máte lepší představu o účinky definice zásad, projděte si ukázky zásad:

- Další příklady najdete v [Ukázkách Azure Policy](json-samples.md).
