---
title: Podrobnosti struktury přiřazení zásad
description: Popisuje definici přiřazení zásad, kterou používá Azure Policy k přidružení definic a parametrů zásad k prostředkům pro vyhodnocení.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535880"
---
# <a name="azure-policy-assignment-structure"></a>Struktura přiřazení Azure Policy

Přiřazení zásad používají Azure Policy k definování prostředků, které se přiřazují k jednotlivým zásadám nebo iniciativám. Přiřazení zásad může určit hodnoty parametrů pro danou skupinu prostředků v době přiřazení, což umožňuje znovu použít definice zásad, které řeší stejné vlastnosti prostředku s různými požadavky na dodržování předpisů.

K vytvoření přiřazení zásady použijte JSON. Přiřazení zásady obsahuje prvky pro:

- zobrazované jméno
- description
- zprostředkovatele identity
- režim vynucení
- vyloučené obory
- definice zásad
- zprávy o neshodě
- parameters

Například následující JSON zobrazuje přiřazení zásady v režimu _DoNotEnforce_ s dynamickými parametry:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Všechny ukázky Azure Policy jsou na [Azure Policy Samples](../samples/index.md).

## <a name="display-name-and-description"></a>Zobrazované jméno a popis

K identifikaci přiřazení zásad a zadání kontextu pro jeho použití s konkrétní sadou prostředků použijte **DisplayName** a **Description** . hodnota **DisplayName** má maximální délku _128_ znaků a **popis** nesmí být delší než _512_ znaků.

## <a name="metadata"></a>Metadata

Volitelná `metadata` vlastnost ukládá informace o přiřazení zásady. Zákazníci mohou definovat libovolné vlastnosti a hodnoty, které jsou užitečné pro jejich organizaci v `metadata` . Existují však některé _běžné_ vlastnosti, které používá Azure Policy. Každá `metadata` vlastnost má limit 1024 znaků.

### <a name="common-metadata-properties"></a>Vlastnosti běžných metadat

- `assignedBy` (String): popisný název objektu zabezpečení, který vytvořil přiřazení.
- `createdBy` (String): identifikátor GUID objektu zabezpečení, který vytvořil přiřazení.
- `createdOn` (String): Formát DateTime Universal ISO 8601 pro čas vytvoření přiřazení.
- `parameterScopes` (Object): kolekce párů klíč-hodnota, kde klíč odpovídá [strongType](./definition-structure.md#strongtype) nakonfigurovanému názvu parametru a hodnota definuje obor prostředků používaný na portálu k poskytnutí seznamu dostupných prostředků podle odpovídajících _strongType_. Portál tuto hodnotu nastaví, pokud se rozsah liší od rozsahu přiřazení. Pokud je tato hodnota nastavena, úprava přiřazení zásady na portálu automaticky nastaví obor pro parametr na tuto hodnotu. Rozsah ale není pevně spojený s hodnotou a může být změněn na jiný obor.

  Následující příklad `parameterScopes` je pro parametr _strongType_ s názvem **backupPolicyId** , který nastaví obor pro výběr prostředku při úpravách přiřazení na portálu.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (String): popisný název objektu zabezpečení, který aktualizoval přiřazení, pokud existuje.
- `updatedOn` (String): formát formátu data a času Universal ISO 8601 pro čas aktualizace přiřazení, pokud existuje.

## <a name="enforcement-mode"></a>Režim vynucení

Vlastnost **enforcementMode** poskytuje zákazníkům možnost Testovat výsledek zásad u existujících prostředků bez zahájení platnosti zásad nebo aktivace záznamů v [protokolu aktivit Azure](../../../azure-monitor/essentials/platform-logs-overview.md). Tento scénář se běžně označuje jako "What If" a je v souladu s postupy bezpečného nasazení. **enforcementMode** se liší od [zakázaného](./effects.md#disabled) efektu, protože to zabrání v tom, aby vyhodnocování prostředků probíhají vůbec.

Tato vlastnost má následující hodnoty:

|Režim |Hodnota JSON |Typ |Opravit ručně |Položka protokolu aktivit |Popis |
|-|-|-|-|-|-|
|Povoleno |Výchozí |řetězec |Yes |Yes |Účinek zásad se vynutil při vytváření nebo aktualizaci prostředku. |
|Zakázáno |DoNotEnforce |řetězec |Yes |No | Při vytváření nebo aktualizaci prostředku není uplatněna zásada. |

Pokud není v definici zásady nebo iniciativy zadaný **enforcementMode** , použije se _výchozí_ hodnota. Pro zásady [deployIfNotExists](./effects.md#deployifnotexists) se dají spouštět [úlohy nápravy](../how-to/remediate-resources.md) , a to i v případě, že **EnforcementMode** je nastavená na _DoNotEnforce_.

## <a name="excluded-scopes"></a>Vyloučené obory

**Rozsah** přiřazení zahrnuje všechny podřízené kontejnery prostředků a podřízené prostředky. Pokud nadřazený kontejner prostředků nebo podřízený prostředek nemá použitu definici, každý z nich může být _vyloučen_ z vyhodnocení nastavením **notScopes**. Tato vlastnost je pole, které umožňuje vyloučit jeden nebo více kontejnerů prostředků nebo prostředků ze vyhodnocení. **notScopes** se dá přidat nebo aktualizovat po vytvoření počátečního přiřazení.

> [!NOTE]
> _Vyloučený_ prostředek se liší od _vyňatého_ prostředku. Další informace najdete v tématu [vysvětlení oboru v Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>ID definice zásady

Toto pole musí být úplný název cesty buď definice zásady, nebo definice iniciativy.
`policyDefinitionId` je řetězec, nikoli pole. Doporučuje se, aby se místo toho k použití [iniciativy](./initiative-definition-structure.md) používala i v případě, že je často přiřazováno více zásad.

## <a name="non-compliance-messages"></a>Zprávy o neshodě

Chcete-li nastavit vlastní zprávu, která popisuje, proč prostředek není kompatibilní s definicí zásady nebo iniciativou, nastavte `nonComplianceMessages` v definici přiřazení. Tento uzel je pole `message` záznamů. Tato vlastní zpráva je kromě výchozí chybové zprávy při nedodržení předpisů a volitelná.

> [!IMPORTANT]
> Vlastní zprávy o nedodržení předpisů se podporují jenom v definicích nebo iniciativách s definicemi [Správce prostředků režimů](./definition-structure.md#resource-manager-modes) .

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Pokud je přiřazení v iniciativě, můžete pro každou definici zásad v iniciativě nakonfigurovat různé zprávy. Zprávy používají `policyDefinitionReferenceId` hodnotu nakonfigurovanou v definici iniciativy. Podrobnosti najdete v tématu [vlastnosti definic zásad](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parametry

Tento segment přiřazení zásad poskytuje hodnoty pro parametry definované v definici [zásady nebo definici iniciativy](./definition-structure.md#parameters). Tento návrh umožňuje znovu použít definici zásad nebo iniciativ s různými prostředky, ale kontrolovat různé obchodní hodnoty nebo výsledky.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

V tomto příkladu jsou parametry dříve definované v definici zásad `prefix` a `suffix` . Toto přiřazení konkrétní zásady nastavuje `prefix` **oddělení** a `suffix` na **-LC**. Stejná definice zásad se znovu používá s jinou sadou parametrů pro jiné oddělení, což snižuje duplicity a složitost definic zásad a zároveň nabízí flexibilitu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [struktuře definic zásad](./definition-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
