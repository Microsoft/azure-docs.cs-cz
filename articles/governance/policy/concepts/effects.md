---
title: Vysvětlení fungování efektů
description: Definice Azure Policy mají různé efekty, které určují, jak je dodržování předpisů spravované a nahlášené.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: eb15aa3c6dbe0f4db62a2029a3c97b4475ab53a2
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255881"
---
# <a name="understand-azure-policy-effects"></a>Pochopení Azure Policych efektů

Každá definice zásady v Azure Policy má jeden efekt. Tím se určuje, co se stane, když se pravidlo zásad vyhodnotí tak, aby odpovídalo. Efekty se chovají odlišně, pokud jsou pro nový prostředek, aktualizovaný prostředek nebo existující prostředek.

V definici zásad se v současné době podporují tyto efekty:

- [Příloh](#append)
- [Auditování](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Odmítnout](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled) (Zakázáno)
- [EnforceRegoPolicy](#enforceregopolicy) (Preview)
- [Upravíte](#modify)

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Žádosti o vytvoření nebo aktualizaci prostředku prostřednictvím Azure Resource Manager jsou vyhodnocovány pomocí Azure Policy nejdříve. Azure Policy vytvoří seznam všech přiřazení, která platí pro daný prostředek, a pak vyhodnotí prostředek proti každé definici. Azure Policy zpracovává několik efektů před předáním požadavku příslušnému poskytovateli prostředků. Tím se zabrání zbytečnému zpracování poskytovatele prostředků, když prostředek nesplňuje navržené ovládací prvky zásad správného řízení Azure Policy.

- Políčko **zakázáno** je zaškrtnuté, abyste zjistili, jestli se má vyhodnotit pravidlo zásad.
- Pak se vyhodnotí **připojení** a **Úpravy** . Vzhledem k tomu, že může žádost změnit, může dojít k tomu, že se projeví audit nebo odepření z aktivace.
- Pak se vyhodnotí **zamítnutí** . Vyhodnocením Deny před auditem je znemožněno dvojí protokolování nepotřebného prostředku.
- **Audit** se pak vyhodnotí předtím, než se požadavek navrátí do poskytovatele prostředků.

Jakmile poskytovatel prostředků vrátí kód úspěšnosti, **AuditIfNotExists** a **DeployIfNotExists** se vyhodnotí a určí, jestli je potřeba další protokolování nebo akce dodržování předpisů.

V současné době není k dispozici žádné pořadí vyhodnocení pro **EnforceRegoPolicy** efekt.

## <a name="disabled"></a>Zakázáno

Tento efekt je vhodný pro situace při testování nebo v případě, že definice zásad má vliv na parametry. Díky této flexibilitě je možné zakázat jedno přiřazení místo zakázání všech těchto přiřazení zásad.

Alternativa k zadanému efektu je **enforcementMode** , která je nastavená u přiřazení zásady.
Když je enforcementMode _zakázaný_, prostředky se ještě vyhodnocují. Protokolování, jako jsou protokoly aktivit a vliv zásad, se neprojeví. Další informace najdete v tématu věnovaném [přiřazení zásad – režim vynucení](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Připojit

Při vytváření nebo aktualizaci se k požadovanému prostředku přidají další pole. Běžným příkladem je zadání povolených IP adres pro prostředek úložiště.

> [!IMPORTANT]
> Příkaz append je určen pro použití bez značek. Během přidávání může přidat značky k prostředku během žádosti o vytvoření nebo aktualizaci. místo toho se doporučuje použít efekt [úprav](#modify) pro značky.

### <a name="append-evaluation"></a>Připojit vyhodnocení

Připojit vyhodnocuje vyhodnocené před tím, než požadavek zpracuje poskytovatel prostředků během vytváření nebo aktualizace prostředku. Pokud je splněna podmínka **if** pravidla zásad, přidá do prostředku pole přidat. Pokud by měl efekt připojení přepsat hodnotu v původním požadavku s jinou hodnotou, pak funguje jako nepřístupný efekt a žádost se odmítne. K připojení nové hodnoty k existujícímu poli použijte verzi **[\*]** aliasu.

Když se v rámci zkušebního cyklu spustí definice zásady, která používá efekt připojení, neprovádí změny prostředků, které už existují. Místo toho označí všechny prostředky, které splňují podmínku **if** jako nevyhovující.

### <a name="append-properties"></a>Připojit vlastnosti

Efekt připojení má pouze pole **Details** , které je povinné. Vzhledem k tomu, že se jedná **o** pole, může mít jeden pár **pole/hodnota** nebo násobky. Seznam přijatelných polí naleznete v tématu [Struktura definice](definition-structure.md#fields) .

### <a name="append-examples"></a>Připojit příklady

Příklad 1: dvojice **pole/hodnota** pomocí [aliasu](definition-structure.md#aliases) bez **[\*]** s **hodnotou** pole pro nastavení pravidel protokolu IP v účtu úložiště. Když je alias mimo **[\*]** pole, efekt připojí **hodnotu** jako celé pole. Pokud pole již existuje, dojde ke konfliktu události odepřít.

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

Příklad 2: dvojice s jedním **polem/hodnotou** pomocí [aliasu](definition-structure.md#aliases) **[\*]** s **hodnotou** pole pro nastavení pravidel protokolu IP v účtu úložiště. Pomocí aliasu **[\*]** tento efekt připojí **hodnotu** k potenciálně existujícímu poli. Pokud pole ještě neexistuje, vytvoří se.

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

## <a name="modify"></a>Úpravy

Příkaz Upravit slouží k přidání, aktualizaci nebo odebrání značek prostředku během vytváření nebo aktualizace. Běžným příkladem je aktualizace značek na prostředky, jako je costCenter. Zásada úprav by měla vždy mít `mode` nastavenou na _indexované_. Stávající prostředky, které nedodržují předpisy, lze opravit pomocí [úlohy nápravy](../how-to/remediate-resources.md).
Jediné pravidlo změny může mít libovolný počet operací.

> [!IMPORTANT]
> Upravit je aktuálně pouze pro použití s značkami. Pokud spravujete značky, doporučuje se místo možnosti připojit jako upravit zadat další typy operací a možnost opravit stávající prostředky. Pokud ale nemůžete vytvořit spravovanou identitu, doporučuje se připojení.

### <a name="modify-evaluation"></a>Upravit vyhodnocení

Úprava je vyhodnocena před tím, než je žádost zpracována poskytovatelem prostředků během vytváření nebo aktualizace prostředku. Upravit přidá nebo aktualizuje značky prostředku, pokud je splněna podmínka **if** pravidla zásad.

Když se v rámci zkušebního cyklu spustí definice zásady pomocí efektu změny, neprovádí změny prostředků, které už existují. Místo toho označí všechny prostředky, které splňují podmínku **if** jako nevyhovující.

### <a name="modify-properties"></a>Upravit vlastnosti

Vlastnost **Details** pro efekt úpravy obsahuje všechny podvlastnosti, které definují oprávnění potřebná k nápravě a **operace** používané k přidání, aktualizaci nebo odebrání hodnot značek.

- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí zahrnovat pole řetězců, které odpovídají ID role řízení přístupu na základě rolí přístupné pro předplatné. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
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
            "field": "[parameters('DeptName')]"
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

Příklad 1: přidejte značku `environment` a nahraďte existující značky `environment` slovem "test":

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

Příklad 2: odeberte značku `env` a přidejte značku `environment` nebo nahraďte existující značky `environment` hodnotou parametru:

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

## <a name="deny"></a>Zamítnout

Odepření se používá k tomu, aby se zabránilo požadavku na prostředek, který neodpovídá definovaným standardům prostřednictvím definice zásady, a požadavek se nezdařil.

### <a name="deny-evaluation"></a>Odepřít vyhodnocení

Při vytváření nebo aktualizaci odpovídajícího prostředku znemožní odepření žádosti, než se pošle poskytovateli prostředků. Požadavek se vrátí jako `403 (Forbidden)`. V portálu je zakázaný možné zobrazit jako stav nasazení, které bylo znemožněno přiřazením zásad.

Během hodnocení stávajících prostředků se prostředky, které odpovídají definici zásad odepření, označí jako nedodržující předpisy.

### <a name="deny-properties"></a>Vlastnosti odepření

Nepřístupný efekt nemá žádné další vlastnosti pro použití v **podmínce definice** zásady.

### <a name="deny-example"></a>Příklad zamítnutí

Příklad: použití efektu odepřít.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditování

Audit se používá k vytvoření události upozornění v protokolu aktivit při vyhodnocování nekompatibilního prostředku, ale nezastaví požadavek.

### <a name="audit-evaluation"></a>Vyhodnocení auditu

Audit je poslední efekt, který při vytváření nebo aktualizaci prostředku kontrolovala Azure Policy. Azure Policy pak odešle prostředek poskytovateli prostředků. Audit funguje stejně jako požadavek prostředku a cyklus vyhodnocení. Azure Policy do protokolu aktivit přidá operaci `Microsoft.Authorization/policies/audit/action` a označí prostředek jako nevyhovující.

### <a name="audit-properties"></a>Vlastnosti auditu

V podmínce podmínky definice zásad neexistují žádné další vlastnosti, **které by bylo** potřeba použít.

### <a name="audit-example"></a>Příklad auditu

Příklad: použití efektu auditu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umožňuje auditování prostředků, které se shodují s podmínkou **if** , ale nemá součásti zadané v **podrobnostech** podmínky **then** .

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists vyhodnocování

AuditIfNotExists se spustí poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku a vrátil kód stavu úspěch. K auditu dojde, pokud neexistují žádné související prostředky nebo pokud se prostředky definované pomocí **ExistenceCondition** nevyhodnotí jako true. Azure Policy do protokolu aktivit přidá operaci `Microsoft.Authorization/policies/audit/action` stejným způsobem jako v důsledku auditu. Když se aktivuje, prostředek, který splnil podmínku **if** , je prostředek, který je označený jako nevyhovující.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

Vlastnost **Details** AuditIfNotExists efektů má všechny podvlastnosti, které definují související prostředky, které se shodují.

- **Typ** [povinné]
  - Určuje typ souvisejícího prostředku, který se má shodovat.
  - Pokud **Details. Type** je typ prostředku pod prostředkem podmínky **if** , zásady se dotazují na prostředky tohoto **typu** v rámci oboru vyhodnoceného prostředku. V opačném případě se zásady dotazují ve stejné skupině prostředků jako vyhodnocený prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku, který se má shodovat, a způsobí, že zásada načte jeden konkrétní prostředek místo všech prostředků zadaného typu.
  - Pokud jsou hodnoty podmínek pro **if. Field. Type** a **then. details. Type** matched _a musí_ být `[field('name')]`. Místo toho by se ale měl zvážit efekt [auditu](#audit) .
- **ResourceGroupName** (volitelné)
  - Umožňuje porovnání souvisejícího prostředku s jinou skupinou prostředků.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - Výchozím nastavením je skupina prostředků prostředku podmínky **if** .
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví rozsah, ze kterého se má načíst související prostředek, ze kterého se má porovnat.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - V _případě skupiny prostředků by se_omezila na skupinu prostředků nebo skupinu **prostředků, která** je určená v **ResourceGroupName**.
  - U _předplatného_se dotazuje na celé předplatné souvisejícího prostředku.
  - Výchozí hodnota je _Resource_.
- **ExistenceCondition** (volitelné)
  - Pokud tento parametr nezadáte, všechny související prostředky **typu** vyhovují tomuto efektu a neaktivuje audit.
  - Používá stejný jazyk jako pravidlo zásad pro podmínku **if** , ale je vyhodnocen proti každému souvisejícímu prostředku jednotlivě.
  - Pokud se libovolný odpovídající související prostředek vyhodnotí jako true, projeví se to jako splněné a neaktivuje se audit.
  - Lze použít [Field ()] ke kontrole rovnocennosti s hodnotami v podmínkách **if** .
  - Například lze použít k ověření, zda je nadřazený prostředek (v podmínce **if** ) ve stejném umístění prostředku jako odpovídající související prostředek.

### <a name="auditifnotexists-example"></a>Příklad AuditIfNotExists

Příklad: vyhodnotí Virtual Machines a určí, jestli antimalwarové rozšíření existuje, a pak Audituje, když chybí.

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
> [Vnořené šablony](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) jsou podporovány v **deployIfNotExists**, ale [propojené šablony](../../../azure-resource-manager/resource-group-linked-templates.md) nejsou aktuálně podporovány.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists vyhodnocování

DeployIfNotExists se spustí poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku a vrátil kód stavu úspěch. K nasazení šablony dojde, pokud neexistují žádné související prostředky nebo pokud prostředky definované pomocí **ExistenceCondition** nevyhodnotí hodnotu true.

V průběhu zkušebního cyklu jsou definice zásad s DeployIfNotExists účinkem odpovídajícím prostředkům označeny jako nevyhovující, ale u tohoto prostředku se neprovádí žádná akce.

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

Vlastnost **Details** efektu DeployIfNotExists má všechny podvlastnosti definující související prostředky, které se shodují, a nasazení šablon, které se má spustit.

- **Typ** [povinné]
  - Určuje typ souvisejícího prostředku, který se má shodovat.
  - Spustí se tak, že se pokusí načíst prostředek pod prostředkem podmínky **if** a pak se dotazuje ve stejné skupině prostředků jako prostředek podmínky **if** .
- **Název** (volitelné)
  - Určuje přesný název prostředku, který se má shodovat, a způsobí, že zásada načte jeden konkrétní prostředek místo všech prostředků zadaného typu.
  - Pokud jsou hodnoty podmínek pro **if. Field. Type** a **then. details. Type** matched _a musí_ být `[field('name')]`.
- **ResourceGroupName** (volitelné)
  - Umožňuje porovnání souvisejícího prostředku s jinou skupinou prostředků.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - Výchozím nastavením je skupina prostředků prostředku podmínky **if** .
  - Pokud je provedeno nasazení šablony, je nasazeno ve skupině prostředků této hodnoty.
- **ExistenceScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví rozsah, ze kterého se má načíst související prostředek, ze kterého se má porovnat.
  - Neplatí, pokud **typ** je prostředek, který by byl pod zdrojem podmínky **if** .
  - V _případě skupiny prostředků by se_omezila na skupinu prostředků nebo skupinu **prostředků, která** je určená v **ResourceGroupName**.
  - U _předplatného_se dotazuje na celé předplatné souvisejícího prostředku.
  - Výchozí hodnota je _Resource_.
- **ExistenceCondition** (volitelné)
  - Pokud tento parametr nezadáte, všechny související prostředky **typu** vyhovují tomuto efektu a neaktivuje nasazení.
  - Používá stejný jazyk jako pravidlo zásad pro podmínku **if** , ale je vyhodnocen proti každému souvisejícímu prostředku jednotlivě.
  - Pokud se některý odpovídající související prostředek vyhodnotí jako true, projeví se to jako splněné a neaktivuje se nasazení.
  - Lze použít [Field ()] ke kontrole rovnocennosti s hodnotami v podmínkách **if** .
  - Například lze použít k ověření, zda je nadřazený prostředek (v podmínce **if** ) ve stejném umístění prostředku jako odpovídající související prostředek.
- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí zahrnovat pole řetězců, které odpovídají ID role řízení přístupu na základě rolí přístupné pro předplatné. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví typ nasazení, které se má aktivovat. _Předplatné_ indikuje [nasazení na úrovni předplatného](../../../azure-resource-manager/deploy-to-subscription.md), skupina _Resource_ označuje nasazení do skupiny prostředků.
  - Při použití nasazení na úrovni předplatného musí být v _nasazení_ zadaná vlastnost _umístění_ .
  - Výchozí hodnota je _Resource_.
- **Nasazení** [požadováno]
  - Tato vlastnost by měla zahrnovat nasazení úplné šablony, protože by byla předána rozhraní API pro vložení `Microsoft.Resources/deployments`. Další informace najdete v tématu [nasazení REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Všechny funkce uvnitř vlastnosti **nasazení** jsou vyhodnocovány jako komponenty šablony, nikoli zásady. Výjimkou je vlastnost **Parameters** , která předává hodnoty ze zásad do šablony. **Hodnota** v této části pod názvem parametru šablony se používá k provedení tohoto předávání hodnoty (viz _FullDbName_ v příkladu DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Příklad DeployIfNotExists

Příklad: vyhodnotí SQL Server databáze a určí, jestli je povolený transparentDataEncryption. V takovém případě je prováděno nasazení, které se má povolit.

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

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Tento efekt se používá v *režimu* definice zásad `Microsoft.ContainerService.Data`. Používá se k předávání pravidel řízení přístupu definovaných pomocí [Rego](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego) k [otevření agenta zásad](https://www.openpolicyagent.org/) (Neprů) ve [službě Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy pro Kubernetes](rego-for-aks.md) jsou v Public Preview a podporují jenom předdefinované definice zásad.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy vyhodnocování

Otevřený řadič pro přístup agenta zásad vyhodnocuje všechny nové žádosti v clusteru v reálném čase.
Každých 5 minut se dokončila úplná kontrola clusteru a výsledky nahlásily Azure Policy.

### <a name="enforceregopolicy-properties"></a>Vlastnosti EnforceRegoPolicy

Vlastnost **Details** EnforceRegoPolicyového efektu má podvlastnosti, které popisují pravidlo Rego Admission Control.

- **policyId** [povinné]
  - K pravidlu Rego Admission Control se předal jedinečný název jako parametr.
- **zásady** [povinné]
  - Určuje identifikátor URI pravidla Rego Admission Control.
- **policyParameters** [nepovinné]
  - Definuje všechny parametry a hodnoty, které se mají předat zásadám Rego.

### <a name="enforceregopolicy-example"></a>Příklad EnforceRegoPolicy

Příklad: Rego Admission Control Rule povolí pouze zadané image kontejneru v AKS.

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

Prostředek může být ovlivněn několika přiřazeními. Tato přiřazení můžou být ve stejném oboru nebo v různých oborech. U každého z těchto přiřazení je také pravděpodobně definován jiný efekt. Podmínka a účinek pro jednotlivé zásady jsou nezávisle vyhodnoceny. Například:

- Zásady 1
  - Omezí umístění prostředku na ' westus '.
  - Přiřazeno k předplatnému A
  - Odepřít efekt
- Zásada 2
  - Omezí umístění prostředku na ' eastus '.
  - Přiřazeno ke skupině prostředků B v předplatném A
  - Auditovat efekt
  
Výsledkem tohoto nastavení je následující výsledek:

- Všechny prostředky, které už jsou ve skupině prostředků B v eastus, jsou v souladu se zásadami 2 a nedodržují zásady 1.
- Žádný prostředek, který už je ve skupině prostředků B not v ' eastus ', není kompatibilní se zásadou 2 a nedodržuje zásady 1, pokud není v ' westus '
- Zásady 1 zakázaly všechny nové prostředky v předplatném A ne v ' westus '
- Všechny nové prostředky v předplatném a a skupině prostředků B v ' westus ' se vytvoří a nedodržují předpisy v zásadách 2.

Pokud zásada 1 a zásada 2 měla vliv na zamítnutí, změní se situace na:

- Žádný prostředek, který už ve skupině prostředků B není v ' eastus ', není kompatibilní se zásadou 2
- Žádný prostředek, který už ve skupině prostředků B není v ' westus ', není kompatibilní se zásadou 1
- Zásady 1 zakázaly všechny nové prostředky v předplatném A ne v ' westus '
- Všechny nové prostředky ve skupině prostředků B předplatného A se zamítly.

Každé přiřazení se vyhodnocuje jednotlivě. V takovém případě není k dispozici žádný prostředek, který by měl dodávat mezery z rozdílů v rozsahu. Čistý výsledek překrývání zásad nebo překrytí zásad se považuje za **kumulativní**. Pokud například zásada 1 a 2 měla odepřený efekt, prostředek by byl zablokován překrývajícími se a konfliktními zásadami. Pokud stále potřebujete vytvořit prostředek v cílovém oboru, Prohlédněte si vyloučení u každého přiřazení a ověřte, jestli mají správné zásady vliv na správné obory.

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).