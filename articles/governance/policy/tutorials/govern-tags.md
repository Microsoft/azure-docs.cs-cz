---
title: 'Kurz: Správa zásad správného řízení značek'
description: V tomto kurzu použijete efekt změny Azure Policy k vytvoření a vykonání modelu zásad správného řízení pro nové a stávající prostředky.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: 9efeb27151cd3a32741f1bdb6d1d90d3304c5874
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876273"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Kurz: Správa řízení značek pomocí Azure Policy

[Značky](../../../azure-resource-manager/management/tag-resources.md) jsou důležitou součástí uspořádání prostředků Azure do taxonomie. Při dodržování [osvědčených postupů pro správu značek](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)může být značkami základem pro použití obchodních zásad s Azure Policy nebo [sledováním nákladů pomocí cost management](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources).
Bez ohledu na to, jak nebo proč používáte značky, je důležité, abyste tyto značky mohli rychle přidávat, měnit a odebírat ve svých prostředcích Azure. Pokud chcete zjistit, jestli váš prostředek Azure podporuje označování, přečtěte si téma [Podpora značek](../../../azure-resource-manager/management/tag-support.md).

Efekt [změny](../concepts/effects.md#modify) Azure Policy je navržený tak, aby se v rámci zásad správného řízení značek bez ohledu na to, ve které fázi zásad správného řízení prostředků nejednalo. **Upravit** pomáhá v těchto případech:

- Nejste v cloudu a nemusíte mít žádné zásady správného řízení značek.
- Už máte tisíce prostředků bez zásad správného řízení značek.
- Už máte existující taxonomii, kterou potřebujete změnit.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> - Určení podnikových požadavků
> - Namapujte každý požadavek na definici zásady.
> - Seskupení zásad značek k iniciativě

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="identify-requirements"></a>Identifikace požadavků

Stejně jako jakákoli dobrá implementace řízení zásad správného řízení by měly požadavky přijít z vašich obchodních potřeb a být dobře pochopitelné před vytvořením technických ovládacích prvků. V tomto scénáři se jedná o naše obchodní požadavky na následující položky:

- Dvě požadované značky pro všechny prostředky: _CostCenter_ a _ENV_
- _CostCenter_ musí existovat na všech kontejnerech a jednotlivých prostředcích.
  - Prostředky dědí z kontejneru, ve kterém jsou, ale můžou být individuálně přepsané.
- _Obálka_ musí existovat na všech kontejnerech a jednotlivých prostředcích.
  - Prostředky určují prostředí podle schématu pojmenování kontejnerů a nedají se přepsat.
  - Všechny prostředky v kontejneru jsou součástí stejného prostředí.

## <a name="configure-the-costcenter-tag"></a>Konfigurace značky CostCenter

V závislosti na prostředí Azure spravovaném pomocí Azure Policy požadavky na značku _CostCenter_ volají následující výsledky:

- Odepření skupin prostředků chybí značka _CostCenter_ .
- Upravit prostředky pro přidání značky _CostCenter_ z nadřazené skupiny prostředků, když chybí

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Odepření skupin prostředků chybí značka CostCenter.

Vzhledem k tomu, že _CostCenter_ pro skupinu prostředků nejde určit podle názvu skupiny prostředků, musí mít pro vytvoření skupiny prostředků značku definovanou v žádosti. Následující pravidlo zásad s efektem [odepření](../concepts/effects.md#deny) zabraňuje vytvoření nebo aktualizaci skupin prostředků, které nemají značku _CostCenter_ :

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Vzhledem k tomu, že toto pravidlo zásad cílí na skupinu prostředků, musí být _režim_ definice zásady "vše" místo "indexovaný".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>V případě chybějících prostředků upravit prostředky pro dědění značky CostCenter

Druhý _CostCenter_ vyžaduje, aby všechny prostředky dědily značku z nadřazené skupiny prostředků, když chybí. Pokud je značka již definována v prostředku, i když se liší od nadřazené skupiny prostředků, musí být ponechána samostatně. Následující pravidlo zásad používá [úpravu](../concepts/effects.md#modify):

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Toto pravidlo zásady používá operaci **Přidání** místo **addOrReplace** , protože nechceme změnit hodnotu značky, pokud je přítomna při [Oprava](../how-to/remediate-resources.md) stávajících prostředcích. Používá také `[resourcegroup()]` funkci Template k získání hodnoty značky z nadřazené skupiny prostředků.

> [!NOTE]
> Vzhledem k tomu, že toto pravidlo zásad cílí na prostředky, které podporují značky, musí být _režim_ definice zásady indexovaný. Tato konfigurace taky zajišťuje, že tyto zásady přeskočí skupiny prostředků.

## <a name="configure-the-env-tag"></a>Konfigurace značky ENV

V souvislosti s prostředím Azure spravovaným pomocí Azure Policy se požadavky na značku _ENV_ zavolají do následujících výsledků:

- Upravte značku _ENV_ pro skupinu prostředků na základě schématu pojmenování skupiny prostředků.
- Upravte značku _ENV_ u všech prostředků ve skupině prostředků na stejnou jako nadřazená skupina prostředků.

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Upravit značku ENV skupin prostředků podle názvu

Pro každé prostředí, které existuje ve vašem prostředí Azure, se vyžadují zásady [úprav](../concepts/effects.md#modify) . Zásady úprav pro každou z nich vypadají jako tato definice zásad:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Vzhledem k tomu, že toto pravidlo zásad cílí na skupinu prostředků, musí být _režim_ definice zásady "vše" místo "indexovaný".

Tato zásada se shoduje jenom se skupinami prostředků s ukázkovým schématem pojmenovávání, které se používá pro produkční prostředky `prd-` . Složitější schéma pojmenování je možné dosáhnout s několika podmínkami **shody** namísto jediného, **jako** v tomto příkladu.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Úprava prostředků pro dědění značky ENV

Požadavek na podnik vyžaduje, aby všechny prostředky měly značku _ENV_ , která má svou nadřazenou skupinu prostředků. Tuto značku nejde přepsat, takže použijeme operaci **addOrReplace** s efektem [úprav](../concepts/effects.md#modify) . Ukázková zásada úprav vypadá jako v následujícím pravidle:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Vzhledem k tomu, že toto pravidlo zásad cílí na prostředky, které podporují značky, musí být _režim_ definice zásady indexovaný. Tato konfigurace taky zajišťuje, že tyto zásady přeskočí skupiny prostředků.

Toto pravidlo zásad vyhledá všechny prostředky, které nemají hodnotu nadřazených skupin prostředků pro značku _ENV_ nebo chybějící značku _ENV_ . Odpovídající prostředky mají svou značku _ENV_ nastavenou na hodnotu nadřazených skupin prostředků, a to i v případě, že značka již existovala v prostředku, ale s jinou hodnotou.

## <a name="assign-the-initiative-and-remediate-resources"></a>Přiřazení iniciativy a nápravy prostředků

Po vytvoření výše uvedených zásad značek je připojte k jedné iniciativě pro řízení značek a přiřaďte je ke skupině nebo předplatnému správy. Iniciativa a zahrnuté zásady pak vyhodnocují shodu stávajících prostředků a mění požadavky na nové nebo aktualizované prostředky, které odpovídají vlastnosti **if** v pravidle zásad. Zásady ale neaktualizují existující prostředky, které nedodržují předpisy, automaticky pomocí definovaných změn značek.

Podobně jako zásady [deployIfNotExists](../concepts/effects.md#deployifnotexists) používají zásady **úprav** ke změně existujících prostředků, které nedodržují předpisy, úlohy nápravy. Postupujte podle pokynů k [nápravě prostředků](../how-to/remediate-resources.md) , abyste identifikovali nekompatibilní prostředky pro **Úpravy** a opravili značky pro vaši definovanou taxonomii.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste dokončili práci s prostředky z tohoto kurzu, pomocí následujícího postupu odstraňte všechna přiřazení a definice, které jste vytvořili výše:

1. Vyberte **definice** (nebo **přiřazení** , pokud se pokoušíte odstranit přiřazení) v části **vytváření obsahu** v levé části stránky Azure Policy.

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="review"></a>Opakování

V tomto kurzu jste se dozvěděli o následujících úlohách:

> [!div class="checklist"]
> - Identifikujte vaše podnikové požadavky.
> - Namapovaný každý požadavek na definici zásady
> - Seskupení zásad značek do iniciativy

## <a name="next-steps"></a>Další kroky

Další informace o strukturách definic zásad najdete v tomto článku:

> [!div class="nextstepaction"]
> [Struktura definic Azure Policy](../concepts/definition-structure.md)