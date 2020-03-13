---
title: Vysvětlení fungování efektů
description: Definice Azure Policy mají různé efekty, které určují, jak je dodržování předpisů spravované a nahlášené.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 502c8a87c4e915ebd1fd764915daa9c89a307097
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281181"
---
# <a name="understand-azure-policy-effects"></a>Principy Azure Policy efekty

Každá definice zásady ve službě Azure Policy obsahuje jediný efekt. Tento efekt Určuje, co se stane, když se pravidlo zásad vyhodnotí tak, aby odpovídaly. Účinky chovat jinak, pokud jsou pro nový prostředek, prostředek aktualizované nebo existující prostředek.

V definici zásad se v současné době podporují tyto efekty:

- [Příloh](#append)
- [Auditování](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Odmítnout](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled) (Zakázáno)
- [EnforceOPAConstraint](#enforceopaconstraint) (Preview)
- [EnforceRegoPolicy](#enforceregopolicy) (Preview)
- [Upravíte](#modify)

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Žádosti o vytvoření nebo aktualizaci prostředku prostřednictvím Azure Resource Manager jsou vyhodnocovány pomocí Azure Policy nejdříve. Azure Policy vytvoří seznam všech přiřazení, která platí pro daný prostředek, a pak vyhodnotí prostředek proti každé definici. Azure Policy zpracovává několik efektů před předáním požadavku příslušnému poskytovateli prostředků. Tím se zabrání zbytečnému zpracování poskytovatele prostředků, když prostředek nesplňuje navržené ovládací prvky zásad správného řízení Azure Policy.

- Políčko **zakázáno** je zaškrtnuté, abyste zjistili, jestli se má vyhodnotit pravidlo zásad.
- Pak se vyhodnotí **připojení** a **Úpravy** . Vzhledem k tomu, že může žádost změnit, může dojít k tomu, že se projeví audit nebo odepření z aktivace.
- Pak se vyhodnotí **zamítnutí** . Vyhodnocením odepření před auditu, je zabráněno double protokolování nežádoucí prostředku.
- **Audit** se pak vyhodnotí předtím, než se požadavek navrátí do poskytovatele prostředků.

Jakmile poskytovatel prostředků vrátí kód úspěšnosti, **AuditIfNotExists** a **DeployIfNotExists** se vyhodnotí a určí, jestli je potřeba další protokolování nebo akce dodržování předpisů.

V současné době není k dispozici žádné pořadí vyhodnocení pro efekty **EnforceOPAConstraint** nebo **EnforceRegoPolicy** .

## <a name="disabled"></a>Zakázáno

Tento efekt je užitečné pro testování situace nebo pokud má definice zásad parametrizované efekt. Díky této flexibilitě umožňuje zakázat jednotné přiřazení namísto zakázání všech přiřazení této zásady.

Alternativa k zadanému efektu je **enforcementMode** , která je nastavená u přiřazení zásady.
Když je enforcementMode _zakázaný_, prostředky se ještě vyhodnocují. Protokolování, jako jsou protokoly aktivit a vliv zásad, se neprojeví. Další informace najdete v tématu věnovaném [přiřazení zásad – režim vynucení](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Připojit

Připojte se používá k přidání další pole k požadovanému prostředku během vytváření nebo aktualizace. Běžným příkladem je zadání povolených IP adres pro prostředek úložiště.

> [!IMPORTANT]
> Příkaz append je určen pro použití bez značek. Během přidávání může přidat značky k prostředku během žádosti o vytvoření nebo aktualizaci. místo toho se doporučuje použít efekt [úprav](#modify) pro značky.

### <a name="append-evaluation"></a>Přidat hodnocení

Připojit vyhodnotí jako předtím, než požadavek zpracuje přes poskytovatele prostředků během vytváření nebo aktualizaci prostředku. Pokud je splněna podmínka **if** pravidla zásad, přidá do prostředku pole přidat. Je-li přidat efekt by se mělo přepsat hodnotu v původní požadavek s jinou hodnotou, funguje jako efektu zamítnutí a žádost odmítne. K připojení nové hodnoty k existujícímu poli použijte verzi **[\*]** aliasu.

Při spuštění definice zásady pomocí efekt připojit jako součást cyklu hodnocení neprovede změny na prostředky, které už existují. Místo toho označí všechny prostředky, které splňují podmínku **if** jako nevyhovující.

### <a name="append-properties"></a>Vlastnosti připojení

Efekt připojení má pouze pole **Details** , které je povinné. Vzhledem k tomu, že se jedná **o** pole, může mít jeden pár **pole/hodnota** nebo násobky. Seznam přijatelných polí naleznete v tématu [Struktura definice](definition-structure.md#fields) .

### <a name="append-examples"></a>Přidat příklady

Příklad 1: dvojice **pole/hodnota** pomocí [aliasu](definition-structure.md#aliases) bez **[\*]** s **hodnotou** pole pro nastavení pravidel protokolu IP v účtu úložiště. Když je alias bez **[\*]** pole, efekt připojí **hodnotu** jako celé pole. Pokud pole již existuje, dojde ke konfliktu události odepřít.

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

Příklad 2: dvojice s jedním **polem/hodnotou** pomocí [aliasu](definition-structure.md#aliases) **[\*]** s **hodnotou** pole pro nastavení pravidel protokolu IP v účtu úložiště. Pomocí aliasu **[\*]** , efekt připojí **hodnotu** k potenciálně existujícímu poli. Pokud pole ještě neexistuje, vytvoří se.

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

## <a name="modify"></a>Změnit

Příkaz Upravit slouží k přidání, aktualizaci nebo odebrání značek prostředku během vytváření nebo aktualizace. Běžným příkladem je aktualizace značek na prostředky, jako je costCenter. Zásada úprav by měla být vždy `mode` nastavena na hodnotu _indexováno_ , pokud cílový prostředek není skupina prostředků. Stávající prostředky, které nedodržují předpisy, lze opravit pomocí [úlohy nápravy](../how-to/remediate-resources.md). Jediné pravidlo změny může mít libovolný počet operací.

> [!IMPORTANT]
> Upravit je aktuálně pouze pro použití s značkami. Pokud spravujete značky, doporučuje se místo možnosti připojit jako upravit zadat další typy operací a možnost opravit stávající prostředky. Pokud ale nemůžete vytvořit spravovanou identitu, doporučuje se připojení.

### <a name="modify-evaluation"></a>Upravit vyhodnocení

Úprava je vyhodnocena před tím, než je žádost zpracována poskytovatelem prostředků během vytváření nebo aktualizace prostředku. Upravit přidá nebo aktualizuje značky prostředku, pokud je splněna podmínka **if** pravidla zásad.

Když se v rámci zkušebního cyklu spustí definice zásady pomocí efektu změny, neprovádí změny prostředků, které už existují. Místo toho označí všechny prostředky, které splňují podmínku **if** jako nevyhovující.

### <a name="modify-properties"></a>Upravit vlastnosti

Vlastnost **Details** pro efekt úpravy obsahuje všechny podvlastnosti, které definují oprávnění potřebná k nápravě a **operace** používané k přidání, aktualizaci nebo odebrání hodnot značek.

- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí obsahovat pole řetězců, které odpovídají ID role řízení přístupu na základě role přístupné předplatné. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
  - Definovaná role musí zahrnovat všechny operace udělené roli [přispěvatele](../../../role-based-access-control/built-in-roles.md#contributor) .
- **operace** [povinné]
  - Pole všech operací značek, které mají být dokončeny na vyhovujících prostředcích.
  - Vlastnosti
    - **operace** [povinné]
      - Definuje akci, která se má provést u odpovídajícího prostředku. Možnosti jsou: _addOrReplace_, _Add_, _Remove_. _Přidat_ se chová podobně jako v efektu [připojit](#append) .
    - **pole** [povinné]
      - Značka, která se má přidat, nahradit nebo odebrat. Názvy značek musí splňovat stejné zásady vytváření názvů pro ostatní [pole](./definition-structure.md#fields).
    - **hodnota** (nepovinná)
      - Hodnota, na kterou má být značka nastavena.
      - Tato vlastnost je povinná, pokud je **operace** _addOrReplace_ nebo _Add_.

### <a name="modify-operations"></a>Úpravy operací

Pole vlastností **Operations** umožňuje změnit několik značek různými způsoby v rámci jedné definice zásady. Každá operace se skládá z vlastností **operace**, **pole**a **hodnoty** . Operace určuje, co je úloha nápravy pro značky, pole určuje, která značka se změní, a hodnota definuje nové nastavení pro tuto značku. Následující příklad provede následující změny značek:

- Nastaví značku `environment` na "test", a to i v případě, že již existuje s jinou hodnotou.
- Odebere značku `TempResource`.
- Nastaví značku `Dept` _na parametr zásad_ , který je nakonfigurovaný pro přiřazení zásady.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Vlastnost **Operation** má následující možnosti:

|Operace |Popis |
|-|-|
|addOrReplace |Přidá do prostředku definovanou značku a hodnotu, i když značka již existuje s jinou hodnotou. |
|Přidat |Přidá do prostředku definovanou značku a hodnotu. |
|Odebrat |Odebere z prostředku definovanou značku. |

### <a name="modify-examples"></a>Upravit příklady

Příklad 1: přidejte značku `environment` a nahraďte existující značky `environment` "test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Příklad 2: odeberte značku `env` a přidejte značku `environment` nebo nahraďte existující značky `environment` hodnotou parametrizovanou:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Odepřít

Odepřít se používá při prevenci požadavkem na prostředky, které neodpovídá definované standardů prostřednictvím definice zásad a požadavek selže.

### <a name="deny-evaluation"></a>Odepřít hodnocení

Když se vytváří nebo aktualizuje prostředek odpovídající odepřít brání žádost před odesláním u poskytovatele prostředků. Požadavek se vrátí jako `403 (Forbidden)`. Na portálu zakázáno zobrazením jako stav nasazení, které zabránily přiřazení zásady.

Během vyhodnocování stávající prostředky prostředky, které splňují definici zásady Odepřít jsou označeny jako nedodržující předpisy.

### <a name="deny-properties"></a>Odepřít vlastnosti

Nepřístupný efekt nemá žádné další vlastnosti pro použití v **podmínce definice** zásady.

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

Audit je poslední efekt, který při vytváření nebo aktualizaci prostředku kontrolovala Azure Policy. Azure Policy pak odešle prostředek poskytovateli prostředků. Audit funguje stejně v případě požadavkem na prostředky a cyklu hodnocení. Azure Policy přidá operaci `Microsoft.Authorization/policies/audit/action` do protokolu aktivit a označí prostředek jako nevyhovující.

### <a name="audit-properties"></a>Vlastnosti auditu

V podmínce podmínky definice zásad neexistují žádné další vlastnosti, **které by bylo** potřeba použít.

### <a name="audit-example"></a>Příklad auditu

Příklad: Použití efektu auditu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umožňuje auditování prostředků, které se shodují s podmínkou **if** , ale nemá součásti zadané v **podrobnostech** podmínky **then** .

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists hodnocení

AuditIfNotExists běží po poskytovatele prostředků byla zpracována žádost o vytvoření nebo aktualizace prostředků a vrátil stavový kód úspěchu. K auditu dojde, pokud neexistují žádné související prostředky nebo pokud se prostředky definované pomocí **ExistenceCondition** nevyhodnotí jako true. Azure Policy do protokolu aktivit přidá operaci `Microsoft.Authorization/policies/audit/action` stejným způsobem jako v důsledku auditu. Když se aktivuje, prostředek, který splnil podmínku **if** , je prostředek, který je označený jako nevyhovující.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

Vlastnost **Details** AuditIfNotExists efektů má všechny podvlastnosti, které definují související prostředky, které se shodují.

- **Typ** [povinné]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Pokud **Details. Type** je typ prostředku pod prostředkem podmínky **if** , zásady se dotazují na prostředky tohoto **typu** v rámci oboru vyhodnoceného prostředku. V opačném případě se zásady dotazují ve stejné skupině prostředků jako vyhodnocený prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady pro načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
  - Pokud jsou hodnoty podmínek pro **if. Field. Type** a **then. details. Type** matched _a musí_ být `[field('name')]`. Místo toho by se ale měl zvážit efekt [auditu](#audit) .
- **ResourceGroupName** (volitelné)
  - Umožňuje odpovídající související prostředek, který pochází z jiné skupiny prostředků.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - Výchozím nastavením je skupina prostředků prostředku podmínky **if** .
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví rozsah, kam načíst související prostředků tak, aby odpovídaly z.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - V _případě skupiny prostředků by se_omezila na skupinu prostředků nebo skupinu **prostředků, která** je určená v **ResourceGroupName**.
  - U _předplatného_se dotazuje na celé předplatné souvisejícího prostředku.
  - Výchozí hodnota je _Resource_.
- **ExistenceCondition** (volitelné)
  - Pokud tento parametr nezadáte, všechny související prostředky **typu** vyhovují tomuto efektu a neaktivuje audit.
  - Používá stejný jazyk jako pravidlo zásad pro podmínku **if** , ale je vyhodnocen proti každému souvisejícímu prostředku jednotlivě.
  - Pokud žádné odpovídající prostředek související vyhodnotí jako true, považuje účinek a neaktivuje auditu.
  - Lze použít [Field ()] ke kontrole rovnocennosti s hodnotami v podmínkách **if** .
  - Například lze použít k ověření, zda je nadřazený prostředek (v podmínce **if** ) ve stejném umístění prostředku jako odpovídající související prostředek.

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

Podobně jako AuditIfNotExists, definice zásad DeployIfNotExists provede nasazení šablony, když je splněna podmínka.

> [!NOTE]
> [Vnořené šablony](../../../azure-resource-manager/templates/linked-templates.md#nested-template) jsou podporovány v **deployIfNotExists**, ale [propojené šablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nejsou aktuálně podporovány.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists hodnocení

DeployIfNotExists spustí asi 15 minut poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku a vrátil kód stavu úspěch. K nasazení šablony dojde, pokud neexistují žádné související prostředky nebo pokud prostředky definované pomocí **ExistenceCondition** nevyhodnotí hodnotu true.
Doba nasazení závisí na složitosti prostředků obsažených v šabloně.

Během cyklu vyhodnocení definice zásad s účinností DeployIfNotExists, které odpovídají prostředky jsou označeny jako nedodržující předpisy, ale na tento prostředek nebyla provedena žádná akce.

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

Vlastnost **Details** efektu DeployIfNotExists má všechny podvlastnosti definující související prostředky, které se shodují, a nasazení šablon, které se má spustit.

- **Typ** [povinné]
  - Určuje typ souvisejících prostředků tak, aby odpovídaly.
  - Spustí se tak, že se pokusí načíst prostředek pod prostředkem podmínky **if** a pak se dotazuje ve stejné skupině prostředků jako prostředek podmínky **if** .
- **Název** (volitelné)
  - Určuje přesný název prostředku tak, aby odpovídaly a způsobí, že zásady pro načtení jedné konkrétní prostředek místo všechny prostředky zadaného typu.
  - Pokud jsou hodnoty podmínek pro **if. Field. Type** a **then. details. Type** matched _a musí_ být `[field('name')]`.
- **ResourceGroupName** (volitelné)
  - Umožňuje odpovídající související prostředek, který pochází z jiné skupiny prostředků.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - Výchozím nastavením je skupina prostředků prostředku podmínky **if** .
  - Pokud je proveden nasazení šablony, se nasadí ve skupině prostředků z této hodnoty.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví rozsah, kam načíst související prostředků tak, aby odpovídaly z.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - V _případě skupiny prostředků by se_omezila na skupinu prostředků nebo skupinu **prostředků, která** je určená v **ResourceGroupName**.
  - U _předplatného_se dotazuje na celé předplatné souvisejícího prostředku.
  - Výchozí hodnota je _Resource_.
- **ExistenceCondition** (volitelné)
  - Pokud tento parametr nezadáte, všechny související prostředky **typu** vyhovují tomuto efektu a neaktivuje nasazení.
  - Používá stejný jazyk jako pravidlo zásad pro podmínku **if** , ale je vyhodnocen proti každému souvisejícímu prostředku jednotlivě.
  - Pokud žádné odpovídající prostředek související vyhodnotí jako true, považuje účinek a neaktivuje nasazení.
  - Lze použít [Field ()] ke kontrole rovnocennosti s hodnotami v podmínkách **if** .
  - Například lze použít k ověření, zda je nadřazený prostředek (v podmínce **if** ) ve stejném umístění prostředku jako odpovídající související prostředek.
- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí obsahovat pole řetězců, které odpovídají ID role řízení přístupu na základě role přístupné předplatné. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví typ nasazení, které se má aktivovat. _Předplatné_ indikuje [nasazení na úrovni předplatného](../../../azure-resource-manager/templates/deploy-to-subscription.md), skupina _Resource_ označuje nasazení do skupiny prostředků.
  - Při použití nasazení na úrovni předplatného musí být v _nasazení_ zadaná vlastnost _umístění_ .
  - Výchozí hodnota je _Resource_.
- **Nasazení** [požadováno]
  - Tato vlastnost by měla zahrnovat nasazení úplné šablony, protože by byla předána `Microsoft.Resources/deployments` rozhraní API pro vložení. Další informace najdete v tématu [nasazení REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Všechny funkce uvnitř vlastnosti **nasazení** jsou vyhodnocovány jako komponenty šablony, nikoli zásady. Výjimkou je vlastnost **Parameters** , která předává hodnoty ze zásad do šablony. **Hodnota** v této části pod názvem parametru šablony se používá k provedení tohoto předávání hodnoty (viz _FullDbName_ v příkladu DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Příklad DeployIfNotExists

Příklad: Vyhodnotí databáze systému SQL Server k určení, zda je povoleno transparentDataEncryption. V takovém případě je prováděno nasazení, které se má povolit.

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
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
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
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Tento efekt se používá v *režimu* definice zásad `Microsoft.Kubernetes.Data`. Používá se k předávání pravidel řízení přístupu na serveru gatekeeper V3 definovaných pomocí [architektury omezení neprů](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) k [otevření agenta zásad](https://www.openpolicyagent.org/) (neprů) pro samoobslužně spravované clustery Kubernetes v Azure.

> [!NOTE]
> [Azure Policy pro modul AKS](aks-engine.md) je v Public Preview a podporuje pouze předdefinované definice zásad.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint vyhodnocování

Otevřený řadič pro přístup agenta zásad vyhodnocuje všechny nové žádosti v clusteru v reálném čase.
Každých 5 minut se dokončila úplná kontrola clusteru a výsledky nahlásily Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Vlastnosti EnforceOPAConstraint

Vlastnost **Details** EnforceOPAConstraintového efektu má podvlastnosti, které popisují pravidlo pro Admission Control na serveru gatekeeper v3.

- **constraintTemplate** [povinné]
  - Šablona omezení CustomResourceDefinition (CRD), která definuje nová omezení. Šablona definuje logiku Rego, schéma omezení a parametry omezení, které jsou předány prostřednictvím **hodnot** z Azure Policy.
- **omezení** [povinné]
  - Implementace šablony omezení CRD. Používá parametry předané prostřednictvím **hodnot** jako `{{ .Values.<valuename> }}`. V následujícím příkladu `{{ .Values.cpuLimit }}` a `{{ .Values.memoryLimit }}`.
- **hodnoty** [nepovinné]
  - Definuje všechny parametry a hodnoty, které se mají předat omezení. Každá hodnota musí existovat v šabloně omezení CRD.

### <a name="enforceregopolicy-example"></a>Příklad EnforceRegoPolicy

Příklad: pravidlo pro Admission Control pro gatekeeper v3, které nastaví omezení prostředků procesoru a paměti v AKS Engine.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Tento efekt se používá v *režimu* definice zásad `Microsoft.ContainerService.Data`. Používá se k předávání pravidel řízení přístupu serveru gatekeeper v2 definovaných pomocí [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) k [otevření agenta zásad](https://www.openpolicyagent.org/) (Neprů) ve [službě Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy pro AKS](rego-for-aks.md) je ve verzi omezené verze Preview a podporuje jenom integrované definice zásad.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy vyhodnocování

Otevřený řadič pro přístup agenta zásad vyhodnocuje všechny nové žádosti v clusteru v reálném čase.
Každých 5 minut se dokončila úplná kontrola clusteru a výsledky nahlásily Azure Policy.

### <a name="enforceregopolicy-properties"></a>Vlastnosti EnforceRegoPolicy

Vlastnost **Details** EnforceRegoPolicy efektu má podvlastnosti, které popisují pravidlo pro Admission Control pro rozhraní gatekeeper v2.

- **policyId** [povinné]
  - K pravidlu Rego Admission Control se předal jedinečný název jako parametr.
- **zásady** [povinné]
  - Určuje identifikátor URI pravidla Rego Admission Control.
- **policyParameters** [nepovinné]
  - Definuje všechny parametry a hodnoty, které se mají předat zásadám Rego.

### <a name="enforceregopolicy-example"></a>Příklad EnforceRegoPolicy

Příklad: pravidlo pro Admission Control pro gatekeeper v2, které povoluje pouze zadané image kontejneru v AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
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

Vyhodnotí se jednotlivě každé přiřazení. V důsledku toho není příležitost pro prostředek do listu prostřednictvím mezera z rozdíly v oboru. Čistý výsledek překrývání zásad nebo překrytí zásad se považuje za **kumulativní**. Jako příklad pokud obě zásady 1 a 2 efektu zamítnutí prostředku by se zablokovaly překrývající se a konfliktní zásady. Pokud stále potřebujete prostředku vytvořené v cílový obor, zkontrolujte vyloučení na každé přiřazení k ověření správné zásady mají vliv na správné rozsahy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
