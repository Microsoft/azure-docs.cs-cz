---
title: Vysvětlení fungování efektů
description: Definice Azure Policy mají různé efekty, které určují, jak je dodržování předpisů spravované a nahlášené.
ms.date: 09/15/2020
ms.topic: conceptual
ms.openlocfilehash: b6622796ab0554f692a3b64e0b41d60f49c561b1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91252000"
---
# <a name="understand-azure-policy-effects"></a>Pochopení Azure Policych efektů

Každá definice zásady v Azure Policy má jediný účinek. Tím se určuje, co se stane, když se pravidlo zásad vyhodnotí tak, aby odpovídalo. Efekty se chovají odlišně, pokud jsou pro nový prostředek, aktualizovaný prostředek nebo existující prostředek.

V definici zásad se v současné době podporují tyto efekty:

- [Připojit](#append)
- [Auditovat](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Odepřít](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Zakázáno](#disabled)
- [Upravit](#modify)

Následující důsledky jsou _zastaralé_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> Místo **EnforceOPAConstraint** nebo **EnforceRegoPolicy** efektů použijte _audit_ a _Odepřít_ v režimu poskytovatele prostředků `Microsoft.Kubernetes.Data` . Předdefinované definice zásad se aktualizovaly. Když se upraví existující přiřazení zásad těchto předdefinovaných definic zásad, musí se parametr _efekt_ změnit na hodnotu v aktualizovaném seznamu _allowedValues_ .

## <a name="order-of-evaluation"></a>Pořadí vyhodnocení

Žádosti o vytvoření nebo aktualizaci prostředku se vyhodnocují Azure Policy nejdřív. Azure Policy vytvoří seznam všech přiřazení, která platí pro daný prostředek, a pak vyhodnotí prostředek proti každé definici. V [režimu Správce prostředků](./definition-structure.md#resource-manager-modes)Azure Policy zpracovává několik efektů před předáním požadavku příslušnému poskytovateli prostředků. Toto pořadí zabrání zbytečnému zpracování poskytovatelem prostředků, pokud prostředek nesplňuje navržené ovládací prvky zásad správného řízení Azure Policy. V [režimu poskytovatele](./definition-structure.md#resource-provider-modes)prostředků spravuje poskytovatel prostředků vyhodnocení a výsledek a oznamuje výsledky zpět do Azure Policy.

- Políčko **zakázáno** je zaškrtnuté, abyste zjistili, jestli se má vyhodnotit pravidlo zásad.
- Pak se vyhodnotí **připojení** a **Úpravy** . Vzhledem k tomu, že může žádost změnit, může dojít k tomu, že se projeví audit nebo odepření z aktivace. Tyto efekty jsou k dispozici pouze v režimu Správce prostředků.
- Pak se vyhodnotí **zamítnutí** . Vyhodnocením Deny před auditem je znemožněno dvojí protokolování nepotřebného prostředku.
- **Audit** se vyhodnocuje jako poslední.

Poté, co poskytovatel prostředků vrátí kód úspěšnosti Správce prostředků v **AuditIfNotExists** režimu, vyhodnotí a **DeployIfNotExists** vyhodnotí, jestli je potřeba další protokolování nebo akce dodržování předpisů.

## <a name="append"></a>Připojit

Při vytváření nebo aktualizaci se k požadovanému prostředku přidají další pole. Běžným příkladem je zadání povolených IP adres pro prostředek úložiště.

> [!IMPORTANT]
> Příkaz append je určen pro použití bez značek. Během přidávání může přidat značky k prostředku během žádosti o vytvoření nebo aktualizaci. místo toho se doporučuje použít efekt [úprav](#modify) pro značky.

### <a name="append-evaluation"></a>Připojit vyhodnocení

Připojit vyhodnocuje vyhodnocené před tím, než požadavek zpracuje poskytovatel prostředků během vytváření nebo aktualizace prostředku. Pokud je splněna podmínka **if** pravidla zásad, přidá do prostředku pole přidat. Pokud by měl efekt připojení přepsat hodnotu v původním požadavku s jinou hodnotou, pak funguje jako nepřístupný efekt a žádost se odmítne. Chcete-li připojit novou hodnotu k existujícímu poli, použijte **\[\*\]** verzi alias.

Když se v rámci zkušebního cyklu spustí definice zásady, která používá efekt připojení, neprovádí změny prostředků, které už existují. Místo toho označí všechny prostředky, které splňují podmínku **if** jako nevyhovující.

### <a name="append-properties"></a>Připojit vlastnosti

Efekt připojení má pouze pole **Details** , které je povinné. Vzhledem k tomu, že se jedná **o** pole, může mít jeden pár **pole/hodnota** nebo násobky. Seznam přijatelných polí naleznete v tématu [Struktura definice](definition-structure.md#fields) .

### <a name="append-examples"></a>Připojit příklady

Příklad 1: pár **pole/hodnota** **\[\*\]** s **hodnotou** pole, která pro nastavení pravidel IP v účtu úložiště používá jinou hodnotu než [alias](definition-structure.md#aliases) . Když **\[\*\]** je objekt, který není aliasem pole, efekt připojí **hodnotu** jako celé pole. Pokud pole již existuje, dojde ke konfliktu události odepřít.

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

Příklad 2: dvojice s jedním **polem/hodnotou** pomocí **\[\*\]** [aliasu](definition-structure.md#aliases) s **hodnotou** pole pro nastavení pravidel protokolu IP v účtu úložiště. Pomocí **\[\*\]** aliasu tento efekt připojí **hodnotu** k potenciálně existujícímu poli. Pokud pole ještě neexistuje, vytvoří se.

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

## <a name="audit"></a>Auditování

Audit se používá k vytvoření události upozornění v protokolu aktivit při vyhodnocování nekompatibilního prostředku, ale nezastaví požadavek.

### <a name="audit-evaluation"></a>Vyhodnocení auditu

Audit je poslední efekt, který při vytváření nebo aktualizaci prostředku kontrolovala Azure Policy. V režimu Správce prostředků Azure Policy pak odešle prostředek poskytovateli prostředků. Audit funguje stejně jako požadavek prostředku a cyklus vyhodnocení. Azure Policy přidá `Microsoft.Authorization/policies/audit/action` operaci do protokolu aktivit a označí prostředek jako nevyhovující.

### <a name="audit-properties"></a>Vlastnosti auditu

V případě režimu Správce prostředků nemá efekt auditu žádné další vlastnosti pro použití v **podmínce definice** zásady.

V případě režimu poskytovatele prostředků `Microsoft.Kubernetes.Data` má efekt auditu následující další podvlastnosti **podrobností**.

- **constraintTemplate** (povinné)
  - Šablona omezení CustomResourceDefinition (CRD), která definuje nová omezení. Šablona definuje logiku Rego, schéma omezení a parametry omezení, které jsou předány prostřednictvím **hodnot** z Azure Policy.
- **omezení** (povinné)
  - Implementace šablony omezení CRD. Používá parametry předané prostřednictvím **hodnot** jako `{{ .Values.<valuename> }}` . V příkladu 2 níže jsou tyto hodnoty `{{ .Values.excludedNamespaces }}` a `{{ .Values.allowedContainerImagesRegex }}` .
- **hodnoty** (volitelné)
  - Definuje všechny parametry a hodnoty, které se mají předat omezení. Každá hodnota musí existovat v šabloně omezení CRD.

### <a name="audit-example"></a>Příklad auditu

Příklad 1: použití efektu auditu pro Správce prostředků režimy.

```json
"then": {
    "effect": "audit"
}
```

Příklad 2: použití efektu auditu pro režim poskytovatele prostředků v `Microsoft.Kubernetes.Data` . Další informace v **podrobnostech** definují šablonu omezení a CRD pro použití v Kubernetes k omezení povolených imagí kontejneru.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umožňuje auditování prostředků _souvisejících_ s prostředkem, který odpovídá podmínce **if** , ale nemá vlastnosti zadané v **podrobnostech** podmínky **then** .

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists vyhodnocování

AuditIfNotExists se spustí poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku a vrátil kód stavu úspěch. K auditu dojde, pokud neexistují žádné související prostředky nebo pokud se prostředky definované pomocí **ExistenceCondition** nevyhodnotí jako true. Azure Policy `Microsoft.Authorization/policies/audit/action` do protokolu aktivit přidá operaci stejným způsobem jako v důsledku auditu. Když se aktivuje, prostředek, který splnil podmínku **if** , je prostředek, který je označený jako nevyhovující.

### <a name="auditifnotexists-properties"></a>Vlastnosti AuditIfNotExists

Vlastnost **Details** AuditIfNotExists efektů má všechny podvlastnosti, které definují související prostředky, které se shodují.

- **Typ** (povinné)
  - Určuje typ souvisejícího prostředku, který se má shodovat.
  - Pokud **Details. Type** je typ prostředku pod prostředkem podmínky **if** , zásady se dotazují na prostředky tohoto **typu** v rámci oboru vyhodnoceného prostředku. V opačném případě se zásady dotazují ve stejné skupině prostředků jako vyhodnocený prostředek.
- **Název** (volitelné)
  - Určuje přesný název prostředku, který se má shodovat, a způsobí, že zásada načte jeden konkrétní prostředek místo všech prostředků zadaného typu.
  - Pokud jsou hodnoty podmínek pro **if. Field. Type** a **pak. details. Type** Match, pak se **název** bude _vyžadovat_ a musí být `[field('name')]` nebo `[field('fullName')]` pro podřízený prostředek.
    Místo toho by se ale měl zvážit efekt [auditu](#audit) .
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

## <a name="deny"></a>Odepřít

Odepření se používá k tomu, aby se zabránilo požadavku na prostředek, který neodpovídá definovaným standardům prostřednictvím definice zásady, a požadavek se nezdařil.

### <a name="deny-evaluation"></a>Odepřít vyhodnocení

Při vytváření nebo aktualizaci odpovídajícího prostředku v režimu Správce prostředků zabrání zamítnutí žádosti před odesláním poskytovateli prostředků. Požadavek se vrátí jako `403 (Forbidden)` . V portálu je zakázaný možné zobrazit jako stav nasazení, které bylo znemožněno přiřazením zásad. Pro režim poskytovatele prostředků spravuje poskytovatel prostředků vyhodnocení prostředku.

Během hodnocení stávajících prostředků se prostředky, které odpovídají definici zásad odepření, označí jako nedodržující předpisy.

### <a name="deny-properties"></a>Vlastnosti odepření

V případě režimu Správce prostředků nemá efekt odepřít žádné další vlastnosti pro použití v **podmínce definice** zásady.

V případě režimu poskytovatele prostředků `Microsoft.Kubernetes.Data` má efekt odepření následující další podvlastnosti **podrobností**.

- **constraintTemplate** (povinné)
  - Šablona omezení CustomResourceDefinition (CRD), která definuje nová omezení. Šablona definuje logiku Rego, schéma omezení a parametry omezení, které jsou předány prostřednictvím **hodnot** z Azure Policy.
- **omezení** (povinné)
  - Implementace šablony omezení CRD. Používá parametry předané prostřednictvím **hodnot** jako `{{ .Values.<valuename> }}` . V příkladu 2 níže jsou tyto hodnoty `{{ .Values.excludedNamespaces }}` a `{{ .Values.allowedContainerImagesRegex }}` .
- **hodnoty** (volitelné)
  - Definuje všechny parametry a hodnoty, které se mají předat omezení. Každá hodnota musí existovat v šabloně omezení CRD.

### <a name="deny-example"></a>Příklad zamítnutí

Příklad 1: použití efektu Odepřít pro režimy Správce prostředků.

```json
"then": {
    "effect": "deny"
}
```

Příklad 2: použití efektu Odepřít pro režim poskytovatele prostředků v `Microsoft.Kubernetes.Data` . Další informace v **podrobnostech** definují šablonu omezení a CRD pro použití v Kubernetes k omezení povolených imagí kontejneru.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Podobně jako AuditIfNotExists, definice zásad DeployIfNotExists provede nasazení šablony, když je splněna podmínka.

> [!NOTE]
> [Vnořené šablony](../../../azure-resource-manager/templates/linked-templates.md#nested-template) jsou podporovány v **deployIfNotExists**, ale [propojené šablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nejsou aktuálně podporovány.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists vyhodnocování

DeployIfNotExists spustí asi 15 minut poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku a vrátil kód stavu úspěch. K nasazení šablony dojde, pokud neexistují žádné související prostředky nebo pokud prostředky definované pomocí **ExistenceCondition** nevyhodnotí hodnotu true.
Doba nasazení závisí na složitosti prostředků obsažených v šabloně.

V průběhu zkušebního cyklu jsou definice zásad s DeployIfNotExists účinkem odpovídajícím prostředkům označeny jako nevyhovující, ale u tohoto prostředku se neprovádí žádná akce. Stávající prostředky, které nedodržují předpisy, lze opravit pomocí [úlohy nápravy](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Vlastnosti DeployIfNotExists

Vlastnost **Details** efektu DeployIfNotExists má všechny podvlastnosti definující související prostředky, které se shodují, a nasazení šablon, které se má spustit.

- **Typ** (povinné)
  - Určuje typ souvisejícího prostředku, který se má shodovat.
  - Spustí se tak, že se pokusí načíst prostředek pod prostředkem podmínky **if** a pak se dotazuje ve stejné skupině prostředků jako prostředek podmínky **if** .
- **Název** (volitelné)
  - Určuje přesný název prostředku, který se má shodovat, a způsobí, že zásada načte jeden konkrétní prostředek místo všech prostředků zadaného typu.
  - Pokud jsou hodnoty podmínek pro **if. Field. Type** a **pak. details. Type** Match, pak se **název** bude _vyžadovat_ a musí být `[field('name')]` nebo `[field('fullName')]` pro podřízený prostředek.
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
- **roleDefinitionIds** (povinné)
  - Tato vlastnost musí zahrnovat pole řetězců, které odpovídají ID role řízení přístupu na základě rolí přístupné pro předplatné. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (volitelné)
  - Povolené hodnoty jsou _předplatné_ a _zdroj_.
  - Nastaví typ nasazení, které se má aktivovat. _Předplatné_ indikuje [nasazení na úrovni předplatného](../../../azure-resource-manager/templates/deploy-to-subscription.md), skupina _Resource_ označuje nasazení do skupiny prostředků.
  - Při použití nasazení na úrovni předplatného musí být v _nasazení_ zadaná vlastnost _umístění_ .
  - Výchozí hodnota je _Resource_.
- **Nasazení** (povinné)
  - Tato vlastnost by měla zahrnovat nasazení úplné šablony, protože by byla předána `Microsoft.Resources/deployments` rozhraní API pro vložení. Další informace najdete v tématu [nasazení REST API](/rest/api/resources/deployments).

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

## <a name="disabled"></a>Zakázáno

Tento efekt je vhodný pro situace při testování nebo v případě, že definice zásad má vliv na parametry. Díky této flexibilitě je možné zakázat jedno přiřazení místo zakázání všech těchto přiřazení zásad.

Alternativa k zakázanému efektu je **enforcementMode**, která je nastavená u přiřazení zásady.
Když **enforcementMode** je enforcementMode _zakázaný_, prostředky se ještě vyhodnocují. Protokolování, jako jsou protokoly aktivit a vliv zásad, se neprojeví. Další informace najdete v tématu věnovaném [přiřazení zásad – režim vynucení](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Tento efekt se používá v _režimu_ definice zásad `Microsoft.Kubernetes.Data` . Používá se k předávání pravidel řízení přístupu na serveru gatekeeper V3 definovaných pomocí [architektury omezení neprů](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) k [otevření agenta zásad](https://www.openpolicyagent.org/) (neprů) do clusterů Kubernetes v Azure.

> [!IMPORTANT]
> Definice zásad omezené verze Preview s **EnforceOPAConstraint** efektem a související kategorií **služby Kubernetes** jsou _zastaralé_. Místo toho použijte _audit_ efektů a _Odepřít_ v režimu poskytovatele prostředků `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint vyhodnocování

Otevřený řadič pro přístup agenta zásad vyhodnocuje všechny nové žádosti v clusteru v reálném čase.
Každých 15 minut se dokončila úplná kontrola clusteru a výsledky nahlásily Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Vlastnosti EnforceOPAConstraint

Vlastnost **Details** EnforceOPAConstraintového efektu má podvlastnosti, které popisují pravidlo pro Admission Control na serveru gatekeeper v3.

- **constraintTemplate** (povinné)
  - Šablona omezení CustomResourceDefinition (CRD), která definuje nová omezení. Šablona definuje logiku Rego, schéma omezení a parametry omezení, které jsou předány prostřednictvím **hodnot** z Azure Policy.
- **omezení** (povinné)
  - Implementace šablony omezení CRD. Používá parametry předané prostřednictvím **hodnot** jako `{{ .Values.<valuename> }}` . V následujícím příkladu jsou tyto hodnoty `{{ .Values.cpuLimit }}` a `{{ .Values.memoryLimit }}` .
- **hodnoty** (volitelné)
  - Definuje všechny parametry a hodnoty, které se mají předat omezení. Každá hodnota musí existovat v šabloně omezení CRD.

### <a name="enforceopaconstraint-example"></a>Příklad EnforceOPAConstraint

Příklad: pravidlo pro Admission Control pro gatekeeper v3, které nastaví omezení prostředků procesoru a paměti v Kubernetes.

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

Tento efekt se používá v _režimu_ definice zásad `Microsoft.ContainerService.Data` . Používá se k předávání pravidel řízení přístupu serveru gatekeeper v2 definovaných pomocí [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) k [otevření agenta zásad](https://www.openpolicyagent.org/) (Neprů) ve [službě Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> Definice zásad omezené verze Preview s **EnforceRegoPolicy** efektem a související kategorií **služby Kubernetes** jsou _zastaralé_. Místo toho použijte _audit_ efektů a _Odepřít_ v režimu poskytovatele prostředků `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy vyhodnocování

Otevřený řadič pro přístup agenta zásad vyhodnocuje všechny nové žádosti v clusteru v reálném čase.
Každých 15 minut se dokončila úplná kontrola clusteru a výsledky nahlásily Azure Policy.

### <a name="enforceregopolicy-properties"></a>Vlastnosti EnforceRegoPolicy

Vlastnost **Details** EnforceRegoPolicy efektu má podvlastnosti, které popisují pravidlo pro Admission Control pro rozhraní gatekeeper v2.

- **policyId** (povinné)
  - K pravidlu Rego Admission Control se předal jedinečný název jako parametr.
- **zásada** (povinné)
  - Určuje identifikátor URI pravidla Rego Admission Control.
- **policyParameters** (volitelné)
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

## <a name="modify"></a>Modify

Příkaz Upravit slouží k přidání, aktualizaci nebo odebrání vlastností nebo značek prostředku během vytváření nebo aktualizace.
Běžným příkladem je aktualizace značek na prostředky, jako je costCenter. Stávající prostředky, které nedodržují předpisy, lze opravit pomocí [úlohy nápravy](../how-to/remediate-resources.md). Jediné pravidlo změny může mít libovolný počet operací.

Následující operace jsou podporovány úpravou:

- Přidání, nahrazení nebo odebrání značek prostředků. U značek by měly být zásady úprav `mode` nastavené na _indexované_ , pokud cílový prostředek není skupina prostředků.
- Přidejte nebo nahraďte hodnotu spravovaného typu identity ( `identity.type` ) virtuálních počítačů a sady škálování virtuálních počítačů.
- Přidejte nebo nahraďte hodnoty určitých aliasů (Preview).
  - Použití `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    v Azure PowerShell **4.6.0** nebo vyšší získáte seznam aliasů, které lze použít s úpravou.

> [!IMPORTANT]
> Pokud spravujete značky, doporučuje se místo možnosti připojit jako upravit zadat další typy operací a možnost opravit stávající prostředky. Připojení se ale doporučuje, pokud nemůžete vytvořit spravovanou identitu nebo upravit ještě nepodporují alias pro vlastnost prostředku.

### <a name="modify-evaluation"></a>Upravit vyhodnocení

Úprava je vyhodnocena před tím, než je žádost zpracována poskytovatelem prostředků během vytváření nebo aktualizace prostředku. Operace změny se aplikují na obsah požadavku, pokud je splněna podmínka **if** pravidla zásad. Každá operace úprav může určovat podmínku, která určuje, kdy se použije. Operace s podmínkami, které jsou vyhodnocovány na _hodnotu false_ , se přeskočí.

Když se zadá alias, provedou se následující další kontroly, aby se zajistilo, že operace změny nemění obsah žádosti způsobem, který způsobí, že ho poskytovatel prostředků odmítne:

- Vlastnost, na kterou se alias mapuje, je ve verzi rozhraní API žádosti označená jako "upravitelná".
- Typ tokenu v operaci Modify odpovídá očekávanému typu tokenu pro vlastnost ve verzi rozhraní API žádosti.

Pokud některá z těchto kontrol selže, hodnocení zásad se vrátí zpět na zadaný **conflictEffect**.

> [!IMPORTANT]
> Jedná se o doporučená, který mění definice, které obsahují aliasy, pomocí **efektu konfliktu** _auditu_ , aby nedocházelo k chybám pomocí verzí rozhraní API, kde mapovaná vlastnost není upravitelná. Pokud se stejný alias chová odlišně mezi verzemi rozhraní API, můžete k určení operace změny používané pro jednotlivé verze rozhraní API použít operace podmíněného provádění změn.

Když se v rámci zkušebního cyklu spustí definice zásady pomocí efektu změny, neprovádí změny prostředků, které už existují. Místo toho označí všechny prostředky, které splňují podmínku **if** jako nevyhovující.

### <a name="modify-properties"></a>Upravit vlastnosti

Vlastnost **Details** pro efekt úpravy obsahuje všechny podvlastnosti, které definují oprávnění potřebná k nápravě a **operace** používané k přidání, aktualizaci nebo odebrání hodnot značek.

- **roleDefinitionIds** (povinné)
  - Tato vlastnost musí zahrnovat pole řetězců, které odpovídají ID role řízení přístupu na základě rolí přístupné pro předplatné. Další informace najdete v tématu [náprava – konfigurace definice zásad](../how-to/remediate-resources.md#configure-policy-definition).
  - Definovaná role musí zahrnovat všechny operace udělené roli [přispěvatele](../../../role-based-access-control/built-in-roles.md#contributor) .
- **conflictEffect** (volitelné)
  - Určuje, která definice zásad "WINS" v případě, že více než jedna definice zásad upravuje stejnou vlastnost nebo když operace úpravy nefunguje na zadaném aliasu.
    - U nových nebo aktualizovaných prostředků má přednost definice zásad s _odepřením_ . Definice zásad s _auditem_ přeskočí všechny **operace**. Pokud má _zamítnutí_více než jedna definice zásady, je žádost zamítnuta jako konflikt. Pokud všechny definice zásad mají _audit_, nezpracovávají se žádné **operace** pro konfliktní definice zásad.
    - V případě existujících prostředků, pokud více než jedna definice zásad má _odepření_, je stav dodržování předpisů _konflikt_. Pokud jeden nebo více definic zásad má _zamítnutí_, každé přiřazení vrátí stav dodržování předpisů jako _nevyhovující_.
  - Dostupné hodnoty: _audit_, _Deny_, _zakázáno_.
  - Výchozí hodnota je _Deny_.
- **operace** (povinné)
  - Pole všech operací značek, které mají být dokončeny na vyhovujících prostředcích.
  - Vlastnosti:
    - **operace** (povinné)
      - Definuje akci, která se má provést u odpovídajícího prostředku. Možnosti jsou: _addOrReplace_, _Add_, _Remove_. _Přidat_ se chová podobně jako v efektu [připojit](#append) .
    - **pole** (povinné)
      - Značka, která se má přidat, nahradit nebo odebrat. Názvy značek musí splňovat stejné zásady vytváření názvů pro ostatní [pole](./definition-structure.md#fields).
    - **hodnota** (nepovinná)
      - Hodnota, na kterou má být značka nastavena.
      - Tato vlastnost je povinná, pokud je **operace** _addOrReplace_ nebo _Add_.
    - **Podmínka** (volitelné)
      - Řetězec obsahující Azure Policy výraz jazyka s [funkcemi zásad](./definition-structure.md#policy-functions) , které jsou vyhodnoceny na _hodnotu true_ nebo _false_.
      - Nepodporuje následující funkce zásad: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Úpravy operací

Pole vlastností **Operations** umožňuje změnit několik značek různými způsoby v rámci jedné definice zásady. Každá operace se skládá z vlastností **operace**, **pole**a **hodnoty** . Operace určuje, co je úloha nápravy pro značky, pole určuje, která značka se změní, a hodnota definuje nové nastavení pro tuto značku. Následující příklad provede následující změny značek:

- Nastaví `environment` značku na "test", a to i v případě, že již existuje s jinou hodnotou.
- Odebere značku `TempResource` .
- Nastaví `Dept` značku na parametr zásad, který _DeptName_ je nakonfigurovaný pro přiřazení zásady.

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
|addOrReplace |Přidá do prostředku definovanou vlastnost nebo značku a hodnotu, a to i v případě, že vlastnost nebo značka již existuje s jinou hodnotou. |
|Přidat |Přidá do prostředku definovanou vlastnost nebo značku a hodnotu. |
|Odebrat |Odebere definovanou vlastnost nebo značku z prostředku. |

### <a name="modify-examples"></a>Upravit příklady

Příklad 1: přidejte `environment` značku a nahraďte stávající `environment` značky "test":

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

Příklad 2: odebrání `env` značky a přidání `environment` značky nebo náhrada stávajících `environment` značek s parametrizovanou hodnotou:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
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

Příklad 3: Ujistěte se, že účet úložiště nepovoluje veřejný přístup k objektu blob, operace úpravy se použije jenom při vyhodnocování požadavků s rozhraním API, které je větší nebo rovno hodnotě 2019-04-01:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Definice zásad vrstvení

Prostředek může být ovlivněn několika přiřazeními. Tato přiřazení můžou být ve stejném oboru nebo v různých oborech. U každého z těchto přiřazení je také pravděpodobně definován jiný efekt. Podmínka a účinek pro jednotlivé zásady jsou nezávisle vyhodnoceny. Příklad:

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

Každé přiřazení se vyhodnocuje jednotlivě. V takovém případě není k dispozici žádný prostředek, který by měl dodávat mezery z rozdílů v rozsahu. Čistý výsledek definic zásad vrstvení je považován za **kumulativní**. Pokud například zásada 1 a 2 měla odepřený efekt, prostředek by byl zablokován překrývajícími se a konfliktními definicemi zásad. Pokud stále potřebujete vytvořit prostředek v cílovém oboru, zkontrolujte vyloučení u každého přiřazení, abyste ověřili, že správná přiřazení zásad ovlivňují správné obory.

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](definition-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).