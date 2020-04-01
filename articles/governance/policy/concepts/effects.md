---
title: Pochopte, jak efekty fungují
description: Definice zásad Azure mají různé efekty, které určují, jak se řídí dodržování předpisů a jak se oznamuje.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422455"
---
# <a name="understand-azure-policy-effects"></a>Principy efektů zásad Azure

Každá definice zásady v Azure Policy má jediný účinek. Tento efekt určuje, co se stane, když je pravidlo zásad vyhodnoceno tak, aby odpovídalo. Efekty se chovají odlišně, pokud se jedná o nový prostředek, aktualizovaný prostředek nebo existující prostředek.

Tyto účinky jsou v současné době podporovány v definici zásad:

- [Připojit](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Odepřít](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled) (Zakázáno)
- [EnforceOPAConstraint](#enforceopaconstraint) (náhled)
- [EnforceRegoPolicy](#enforceregopolicy) (náhled)
- [Úprava](#modify)

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Požadavky na vytvoření nebo aktualizaci prostředku prostřednictvím Azure Resource Manageru vyhodnotí nejprve služba Azure Policy. Azure Policy vytvoří seznam všech přiřazení, která se vztahují k prostředku a pak vyhodnotí prostředek proti každé definici. Zásady Azure zpracuje několik efektů před předáním požadavku příslušnému zprostředkovateli prostředků. Tím zabráníte zbytečnému zpracování zprostředkovatelem prostředků, když prostředek nesplňuje navržené ovládací prvky zásad správného řízení zásad Azure.

- **Zakázáno** je nejprve zkontrolováno, aby bylo možné určit, zda má být vyhodnoceno pravidlo zásad.
- **Připojit** **a změnit** jsou pak vyhodnoceny. Vzhledem k tomu, že buď může změnit požadavek, provedená změna může zabránit aktivaci auditu nebo odepřít efekt.
- **Deny** je pak vyhodnocena. Vyhodnocením odepřít před auditem, dvojité protokolování nežádoucí prostředek je zabráněno.
- **Audit** je pak vyhodnocen před tím, než žádost přejde poskytovateli prostředků.

Poté, co zprostředkovatel prostředků vrátí kód úspěchu, **AuditIfNotExists** a **DeployIfNotExists** vyhodnotit zjistit, zda je požadováno další dodržování předpisů protokolování nebo akce.

V současné době neexistuje žádné pořadí hodnocení pro **EnforceOPAConstraint** nebo **EnforceRegoPolicy** účinky.

## <a name="disabled"></a>Zakázáno

Tento efekt je užitečný pro testování situací nebo při definici zásady parametrizoval efekt. Tato flexibilita umožňuje zakázat jedno přiřazení namísto zakázání všech přiřazení této zásady.

Alternativou k disabled efekt je **enforcementMode,** který je nastaven na přiřazení zásad.
Pokud je _zakázán_režim **vynucení** , prostředky jsou stále vyhodnocovány. Protokolování, jako jsou protokoly aktivit a efekt zásad nedochází. Další informace naleznete v [tématu přiřazení zásad - režim vynucení](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Připojit

Připojit se používá k přidání dalších polí k požadovanému prostředku během vytváření nebo aktualizace. Běžným příkladem je určení povolených IP adresy pro prostředek úložiště.

> [!IMPORTANT]
> Připojit je určen pro použití s vlastnostmi bez značky. Zatímco připojit můžete přidat značky do prostředku během vytvoření nebo aktualizace požadavku, doporučuje se použít [upravit](#modify) efekt pro značky místo.

### <a name="append-evaluation"></a>Hodnocení připojení

Připojit vyhodnocuje před požadavek získá zpracovány zprostředkovatelem prostředků při vytváření nebo aktualizaci prostředku. Připojit přidá pole k prostředku při splnění podmínky **if** pravidla zásady. Pokud by efekt připojení přepsal hodnotu v původním požadavku jinou hodnotou, bude působit jako efekt odepření a požadavek odmítne. Chcete-li připojit novou hodnotu k existujícímu poli, použijte verzi **aliasu [\*].**

Pokud je definice zásad používající efekt připojení spuštěna jako součást cyklu hodnocení, neprovede změny prostředků, které již existují. Místo toho označí všechny prostředky, které splňuje **podmínka if** jako nekompatibilní.

### <a name="append-properties"></a>Připojit vlastnosti

Efekt připojení má pouze pole **podrobností,** které je povinné. Jako **podrobnosti** je pole, může trvat buď jeden **pár pole/hodnota** nebo násobky. Seznam přijatelných polí naleznete v [definiční struktuře.](definition-structure.md#fields)

### <a name="append-examples"></a>Připojit příklady

Příklad 1: **Jednopole/hodnota** dvojice pomocí non-**[\*]** [alias](definition-structure.md#aliases) s **hodnotou** pole nastavit pravidla IP na účet úložiště. Pokud je alias non-**[\*]** matice, efekt připojí **hodnotu** jako celé pole. Pokud pole již existuje, dojde k události odepřít z konfliktu.

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

Příklad 2: Jedno **pole/hodnota** dvojice pomocí **\*[ ]** [alias](definition-structure.md#aliases) s **hodnotou** pole nastavit pravidla IP na účet úložiště. Pomocí aliasu **[\*]** efekt připojí **hodnotu** k potenciálně již existujícímu poli. Pokud pole ještě neexistuje, bude vytvořeno.

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

## <a name="modify"></a>Modify

Úpravy se používají k přidání, aktualizaci nebo odebrání značek na prostředku během vytváření nebo aktualizace. Běžným příkladem je aktualizace značek na prostředcích, jako je například costCenter. Zásada změny by `mode` měla mít vždy nastavena na _Indexováno,_ pokud cílový prostředek je skupina prostředků. Existující nekompatibilní prostředky lze napravit [nápravnou úlohou](../how-to/remediate-resources.md). Jedno pravidlo změnit může mít libovolný počet operací.

> [!IMPORTANT]
> Změna je v současné době pouze pro použití se značkami. Pokud spravujete značky, doporučujeme použít použít změnit místo Připojit jako Změnit poskytuje další typy operací a možnost napravit existující prostředky. Append se však doporučuje, pokud nejste schopni vytvořit spravovanou identitu.

### <a name="modify-evaluation"></a>Změnit vyhodnocení

Upravte vyhodnocování před tím, než bude požadavek zpracován poskytovatelem prostředků během vytváření nebo aktualizace prostředku. Úprava značek přidá nebo aktualizuje prostředek, pokud je splněna podmínka **if** pravidla zásady.

Pokud je definice zásad pomocí efektu Změnit spuštěna jako součást cyklu hodnocení, neprovede změny prostředků, které již existují. Místo toho označí všechny prostředky, které splňuje **podmínka if** jako nekompatibilní.

### <a name="modify-properties"></a>Úprava vlastností

Vlastnost **podrobností** efektu Změnit má všechny podvlastnosti, které definují oprávnění potřebná pro nápravu a **operace** použité k přidání, aktualizaci nebo odebrání hodnot značek.

- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí obsahovat pole řetězců, které odpovídají ID role na základě role řízení přístupu přístupné odběr. Další informace naleznete v [tématu náprava - konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
  - Definovaná role musí zahrnovat všechny operace udělené roli [přispěvatele.](../../../role-based-access-control/built-in-roles.md#contributor)
- **operace** [povinné]
  - Pole všech operací značky, které mají být dokončeny na odpovídající prostředky.
  - Vlastnosti:
    - **operace** [povinné]
      - Definuje, jakou akci provést s odpovídajícím zdrojem. Možnosti jsou: _addOrReplace_, _Přidat_, _Odebrat_. _Přidat_ se chová podobně jako efekt [Připojit.](#append)
    - **pole** [povinné]
      - Značka, kterou chcete přidat, nahradit nebo odebrat. Názvy značek musí splňovat stejnou konvenci pojmenování pro jiná [pole](./definition-structure.md#fields).
    - **hodnota** (nepovinné)
      - Hodnota pro nastavení značky.
      - Tato vlastnost je vyžadována, pokud je **operace** _addOrReplace_ nebo _Add_.

### <a name="modify-operations"></a>Upravit operace

Pole **vlastností operations** umožňuje změnit několik značek různými způsoby z jedné definice zásad. Každá operace se skládá z **vlastností operace**, **pole**a **hodnoty.** Operace určuje, co nápravná úloha udělá se značkami, pole určuje, která značka je změněna, a hodnota definuje nové nastavení pro tuto značku. Následující příklad provádí následující změny značek:

- Nastaví `environment` značku na "Test", i když již existuje s jinou hodnotou.
- Odebere značku `TempResource`.
- Nastaví `Dept` značku na parametr _zásady DeptName_ nakonfigurovaný v přiřazení zásad.

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

Vlastnost **operation** má následující možnosti:

|Operace |Popis |
|-|-|
|addOrReplace |Přidá definovanou značku a hodnotu k prostředku, i když značka již existuje s jinou hodnotou. |
|Přidat |Přidá definovanou značku a hodnotu k prostředku. |
|Odebrat |Odebere definovanou značku ze zdroje. |

### <a name="modify-examples"></a>Změnit příklady

Příklad 1: `environment` Přidejte značku `environment` a nahraďte existující značky "Test":

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

Příklad 2: `env` Odeberte `environment` značku a `environment` přidejte značku nebo nahraďte existující značky parametrizovanou hodnotou:

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

Odepřít se používá k zabránění požadavku na prostředky, který neodpovídá definovaným standardům prostřednictvím definice zásadami a požadavek se nezdaří.

### <a name="deny-evaluation"></a>Odepřít hodnocení

Při vytváření nebo aktualizaci odpovídajícího prostředku příkaz deny zabrání požadavku před odesláním poskytovateli prostředků. Požadavek je vrácen `403 (Forbidden)`jako . Na portálu zakázané lze zobrazit jako stav na nasazení, které bylo zabráněno přiřazení zásad.

Během hodnocení existujících prostředků jsou prostředky, které odpovídají definici zásady odepření, označeny jako nekompatibilní.

### <a name="deny-properties"></a>Odepřít vlastnosti

Efekt odepřít nemá žádné další vlastnosti pro použití v **pak** podmínku definice zásady.

### <a name="deny-example"></a>Příklad zamítnutí

Příklad: Použití efektu odepřít.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditování

Audit se používá k vytvoření události upozornění v protokolu aktivit při vyhodnocování nekompatibilní prostředek, ale nezastaví požadavek.

### <a name="audit-evaluation"></a>Hodnocení auditu

Auditování je poslední efekt kontrolovaný zásadou Azure během vytváření nebo aktualizace prostředku. Zásady Azure pak odešle prostředek zprostředkovateli prostředků. Audit funguje stejně pro požadavek na zdroj a cyklus hodnocení. Azure Policy `Microsoft.Authorization/policies/audit/action` přidá operaci do protokolu aktivit a označí prostředek jako nekompatibilní.

### <a name="audit-properties"></a>Vlastnosti auditu

Efekt auditu nemá žádné další vlastnosti pro použití v **pak** podmínku definice zásady.

### <a name="audit-example"></a>Příklad auditu

Příklad: Použití efektu auditu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umožňuje auditování prostředků, které odpovídají **podmínce if,** ale nemá součásti zadané v **podrobnostech** **podmínky then.**

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists hodnocení

AuditIfNotExists se spustí poté, co zprostředkovatel prostředků zpracoval požadavek na vytvoření nebo aktualizaci prostředků a vrátil kód stavu úspěchu. Audit dochází, pokud neexistují žádné související prostředky nebo pokud prostředky definované **Existencí Nejsou** vyhodnoceny jako true. Azure Policy `Microsoft.Authorization/policies/audit/action` přidá operaci do protokolu aktivit stejným způsobem jako efekt auditu. Při aktivaci prostředek, který splnil **podmínka if,** je prostředek, který je označen jako nekompatibilní.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

**Vlastnost podrobnosti** AuditIfNotExists má všechny podvlastnosti, které definují související prostředky tak, aby odpovídaly.

- **Typ** [povinné]
  - Určuje typ souvisejícího prostředku, který se má shodovat.
  - Pokud **details.type** je typ prostředku pod **if** podmínku prostředku, zásady dotazy pro prostředky tohoto **typu** v rámci rozsahu vyhodnocovaného prostředku. V opačném případě dotazy zásad v rámci stejné skupiny prostředků jako vyhodnocován prostředek.
- **Jméno** (nepovinné)
  - Určuje přesný název prostředku, který se má shodovat, a způsobí, že zásada načte jeden konkrétní prostředek namísto všech prostředků zadaného typu.
  - Pokud se hodnoty podmínky pro **if.field.type** a **then.details.type** shodují, potom **Name** se stane _povinným_ a musí být `[field('name')]`. Místo toho by však měl být zvážen [auditní](#audit) efekt.
- **ResourceGroupName** (volitelné)
  - Umožňuje, aby sladění souvisejícího prostředku pocházelo z jiné skupiny prostředků.
  - Neplatí, pokud **je typ** prostředek, který by byl pod zdrojem podmínky **if.**
  - Výchozí je skupina prostředků prostředku **podmínce if.**
- **ExistenceScope** (nepovinné)
  - Povolené hodnoty jsou _Odběr_ a _ResourceGroup_.
  - Nastaví obor, odkud má být načíst související prostředek, ze kterého se má shodovat.
  - Neplatí, pokud **je typ** prostředek, který by byl pod zdrojem podmínky **if.**
  - V _poli Skupina zdrojů_by se omezila na skupinu prostředků prostředku **podpodmínku if** nebo na skupinu prostředků zadanou v poli **ResourceGroupName**.
  - V _případě předplatného_se dotazuje celého předplatného pro související prostředek.
  - Výchozí hodnota je _Skupina prostředků_.
- **ExistencePodmínka** (nepovinné)
  - Pokud není zadán, všechny související zdroj **typu** splňuje efekt a neaktivuje audit.
  - Používá stejný jazyk jako pravidlo zásad pro podmínku **if,** ale je vyhodnocen a každý související prostředek jednotlivě.
  - Pokud se jakýkoli odpovídající související prostředek vyhodnotí jako true, efekt je splněn a neaktivuje audit.
  - Lze použít [field()] ke kontrole rovnocennosti s hodnotami v podmínce **if.**
  - Lze například ověřit, zda je nadřazený prostředek (v podmínce **if)** ve stejném umístění prostředku jako odpovídající související prostředek.

### <a name="auditifnotexists-example"></a>Příklad AuditIfNotExists

Příklad: Vyhodnotí virtuální počítače, aby zjistil, zda existuje rozšíření antimalwaru, a pak audituje, když chybí.

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

Podobně jako AuditIfNotExists, deployIfNotExists definice zásad spustí nasazení šablony při splnění podmínky.

> [!NOTE]
> [Vnořené šablony](../../../azure-resource-manager/templates/linked-templates.md#nested-template) jsou podporovány **pomocí deployIfNotExists**, ale [propojené šablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nejsou aktuálně podporovány.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists hodnocení

DeployIfNotExists se spustí přibližně 15 minut poté, co zprostředkovatel prostředků zpracoval požadavek na vytvoření nebo aktualizaci prostředků a vrátil stavový kód úspěchu. Nasazení šablony dojde, pokud neexistují žádné související prostředky nebo pokud prostředky definované **Existencí Není** vyhodnocena na hodnotu true.
Doba trvání nasazení závisí na složitosti prostředků zahrnutých v šabloně.

Během cyklu hodnocení definice zásad s DeployIfNotExists efekt, který odpovídá prostředky jsou označeny jako nedodržující, ale žádná akce je provedena na tento prostředek. Existující nekompatibilní prostředky lze napravit [nápravnou úlohou](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

Vlastnost **details** efektu DeployIfNotExists má všechny podvlastnosti, které definují související prostředky, které se mají shodovat, a nasazení šablony ke spuštění.

- **Typ** [povinné]
  - Určuje typ souvisejícího prostředku, který se má shodovat.
  - Začíná pokusem o načtení prostředku pod prostředek **podmínky if** a poté se dotazuje v rámci stejné skupiny prostředků jako prostředek podmínky **if.**
- **Jméno** (nepovinné)
  - Určuje přesný název prostředku, který se má shodovat, a způsobí, že zásada načte jeden konkrétní prostředek namísto všech prostředků zadaného typu.
  - Pokud se hodnoty podmínky pro **if.field.type** a **then.details.type** shodují, potom **Name** se stane _povinným_ a musí být `[field('name')]`.
- **ResourceGroupName** (volitelné)
  - Umožňuje, aby sladění souvisejícího prostředku pocházelo z jiné skupiny prostředků.
  - Neplatí, pokud **je typ** prostředek, který by byl pod zdrojem podmínky **if.**
  - Výchozí je skupina prostředků prostředku **podmínce if.**
  - Pokud je nasazení šablony spuštěno, nasadí se ve skupině prostředků této hodnoty.
- **ExistenceScope** (nepovinné)
  - Povolené hodnoty jsou _Odběr_ a _ResourceGroup_.
  - Nastaví obor, odkud má být načíst související prostředek, ze kterého se má shodovat.
  - Neplatí, pokud **je typ** prostředek, který by byl pod zdrojem podmínky **if.**
  - V _poli Skupina zdrojů_by se omezila na skupinu prostředků prostředku **podpodmínku if** nebo na skupinu prostředků zadanou v poli **ResourceGroupName**.
  - V _případě předplatného_se dotazuje celého předplatného pro související prostředek.
  - Výchozí hodnota je _Skupina prostředků_.
- **ExistencePodmínka** (nepovinné)
  - Pokud není zadán, všechny související prostředek **typu** splňuje efekt a neaktivuje nasazení.
  - Používá stejný jazyk jako pravidlo zásad pro podmínku **if,** ale je vyhodnocen a každý související prostředek jednotlivě.
  - Pokud všechny odpovídající související prostředek vyhodnotí na true, efekt je splněna a neaktivuje nasazení.
  - Lze použít [field()] ke kontrole rovnocennosti s hodnotami v podmínce **if.**
  - Lze například ověřit, zda je nadřazený prostředek (v podmínce **if)** ve stejném umístění prostředku jako odpovídající související prostředek.
- **roleDefinitionIds** [povinné]
  - Tato vlastnost musí obsahovat pole řetězců, které odpovídají ID role na základě role řízení přístupu přístupné odběr. Další informace naleznete v [tématu náprava - konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (volitelné)
  - Povolené hodnoty jsou _Odběr_ a _ResourceGroup_.
  - Nastaví typ nasazení, které má být spuštěno. _Předplatné_ označuje [nasazení na úrovni předplatného](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ označuje nasazení do skupiny prostředků.
  - Vlastnost _umístění_ musí být zadána v _nasazení_ při použití nasazení na úrovni předplatného.
  - Výchozí hodnota je _Skupina prostředků_.
- **Nasazení** [povinné]
  - Tato vlastnost by měla obsahovat úplné nasazení `Microsoft.Resources/deployments` šablony, protože by byla předána rozhraní API PUT. Další informace naleznete v [tématu Nasazení rozhraní REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Všechny funkce uvnitř **Deployment** vlastnost jsou vyhodnocovány jako součásti šablony, nikoli zásady. Výjimkou je **vlastnost parameters,** která předává hodnoty ze zásady do šablony. **Hodnota** v této části pod názvem parametru šablony se používá k provedení této hodnoty předávání (viz _fullDbName_ v deployifnotexists příkladu).

### <a name="deployifnotexists-example"></a>Příklad DeployIfNotExists

Příklad: Vyhodnocuje databáze serveru SQL Server k určení, zda je povoleno transparentní šifrování DatEncryption. Pokud ne, je spuštěno nasazení, které chcete povolit.

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

## <a name="enforceopaconstraint"></a>EnforceOPAconstraint

Tento efekt se používá s `Microsoft.Kubernetes.Data` *režimem* definice zásad . Používá se k předání gatekeeper v3 pravidla kontroly přístupu definované s [OPA omezení rámce](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) [otevřít agenta zásad](https://www.openpolicyagent.org/) (OPA) na samořízené Clustery Kubernetes v Azure.

> [!NOTE]
> [Azure Policy for AKS Engine](aks-engine.md) je ve verzi Public Preview a podporuje jenom předdefinované definice zásad.

### <a name="enforceopaconstraint-evaluation"></a>Vyhodnocení enforceOPAConstraint

Řadič přijetí agenta otevřené politiky vyhodnotí všechny nové požadavky v clusteru v reálném čase.
Každých 5 minut je dokončena úplná prohledávací služba clusteru a výsledky nahlášené zásadám Azure.

### <a name="enforceopaconstraint-properties"></a>Vlastnosti EnforceOPAConstraint

**Podrobnosti** vlastnost EnforceOPAConstraint efekt má podvlastnosti, které popisují Gatekeeper v3 pravidlo kontroly přijetí.

- **constraintTemplate** [povinné]
  - Šablona omezení CustomResourceDefinition (CRD), která definuje nová omezení. Šablona definuje logiku Rego, schéma omezení a parametry omezení, které jsou předávány prostřednictvím **hodnot** z Azure Policy.
- **omezení** [povinné]
  - Implementace crd šablony omezení. Používá parametry **values** předané `{{ .Values.<valuename> }}`prostřednictvím hodnot jako . V níže uvedeném příkladu by to bylo `{{ .Values.cpuLimit }}` a `{{ .Values.memoryLimit }}`.
- **hodnoty** [nepovinné]
  - Definuje všechny parametry a hodnoty, které mají být předávány omezení. Každá hodnota musí existovat v šabloně omezení CRD.

### <a name="enforceopaconstraint-example"></a>Příklad EnforceOPAConstraint

Příklad: Gatekeeper v3 pravidlo kontroly přijetí nastavit omezení procesoru kontejneru a paměti v AKS Engine.

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

## <a name="enforceregopolicy"></a>Zásady vynucení Rego

Tento efekt se používá s `Microsoft.ContainerService.Data` *režimem* definice zásad . Používá se k předání pravidel kontroly přístupu Gatekeeper v2 definovaných pomocí [agenta zásad Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) to [Open (OPA)](https://www.openpolicyagent.org/) ve službě Azure [Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Zásady Azure pro AKS](rego-for-aks.md) jsou v omezenéverzi Preview a podporují jenom integrované definice zásad.

### <a name="enforceregopolicy-evaluation"></a>VyhodnoceníRegoPolicy hodnocení

Řadič přijetí agenta otevřené politiky vyhodnotí všechny nové požadavky v clusteru v reálném čase.
Každých 5 minut je dokončena úplná prohledávací služba clusteru a výsledky nahlášené zásadám Azure.

### <a name="enforceregopolicy-properties"></a>Vlastnosti EnforceRegoPolicy

**Podrobnosti** vlastnost EnforceRegoPolicy efekt má podvlastnosti, které popisují Gatekeeper v2 pravidlo kontroly přijetí.

- **policyId** [povinné]
  - Jedinečný název předaný jako parametr pravidlu řízení přijetí Rego.
- **politika** [povinné]
  - Určuje identifikátor URI pravidla řízení přijetí rego.
- **policyParameters** [nepovinné]
  - Definuje všechny parametry a hodnoty, které mají být předávány zásadě rego.

### <a name="enforceregopolicy-example"></a>Příklad EnforceRegoPolicy

Příklad: Gatekeeper v2 pravidlo kontroly přijetí povolit pouze zadané image kontejneru v AKS.

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

Zdroj může být ovlivněn několika přiřazeními. Tato přiřazení mohou být ve stejném oboru nebo v různých oborech. Každé z těchto přiřazení je také pravděpodobné, že mají jiný účinek definován. Podmínka a účinek pro každou zásadu je nezávisle vyhodnocena. Například:

- Zásady 1
  - Omezuje umístění prostředků na westus.
  - Přiřazeno k předplatnému A
  - Odepřít efekt
- Zásady 2
  - Omezuje umístění prostředků na "eastus"
  - Přiřazeno ke skupině prostředků B v předplatném A
  - Efekt auditu
  
Toto nastavení by mělo za následek následující výsledek:

- Všechny prostředky, které jsou již ve skupině prostředků B v "eastus", jsou v souladu se zásadou 2 a nedodržují zásady 1.
- Všechny prostředky, které jsou již ve skupině prostředků B, které nejsou v "eastus", nejsou kompatibilní se zásadou 2 a nejsou v souladu se zásadami 1, pokud nejsou v "westus"
- Všechny nové prostředky v předplatném A, které nejsou v "westus", jsou odmítnuty zásadami 1.
- Všechny nové prostředky v předplatném A a skupině prostředků B v "westus" je vytvořen a nevyhovující zásadám 2

Pokud politika 1 i politika 2 měly účinek odmítnutí, situace se změní na:

- Všechny prostředky, které jsou již ve skupině prostředků B, které nejsou v zásadě eastus, nejsou kompatibilní se zásadami 2.
- Všechny prostředky, které jsou již ve skupině prostředků B, které nejsou v režimu westus, nejsou kompatibilní se zásadami 1.
- Všechny nové prostředky v předplatném A, které nejsou v "westus", jsou odmítnuty zásadami 1.
- Všechny nové prostředky ve skupině prostředků B předplatného A jsou odepřeny.

Každé přiřazení je vyhodnoceno individuálně. Jako takový, není příležitost pro zdroj proklouznout mezeru od rozdílů v rozsahu. Čistý výsledek zásad vrstvení nebo překrývání zásad je považován za **kumulativní nejvíce omezující**. Například pokud zásady 1 a 2 měl odepřít účinek, prostředek by blokován překrývající se a konfliktní zásady. Pokud stále potřebujete prostředek, který má být vytvořen v cílovém oboru, zkontrolujte vyloučení na každé přiřazení k ověření správné zásady mají vliv na správné obory.

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
