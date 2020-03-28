---
title: 'Kurz: Správa zásad správného řízení značek'
description: V tomto kurzu použijete efekt Upravit zásad azure k vytvoření a vynucení modelu zásad správného řízení značek na nových a stávajících prostředcích.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 5e9cb9a4acb930c117374281a3debaeecce47110
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75965985"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Kurz: Správa zásad ování značek pomocí zásad Azure

[Značky](../../../azure-resource-manager/management/tag-resources.md) jsou klíčovou součástí uspořádání prostředků Azure do taxonomie. Při dodržování [doporučených postupů pro správu značek](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)mohou být značky základem pro použití vašich obchodních zásad pomocí zásad Azure nebo [sledování nákladů pomocí správy nákladů](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources).
Bez ohledu na to, jak nebo proč používáte značky, je důležité, abyste tyto značky mohli rychle přidávat, měnit a odebírat ve svých prostředcích Azure.

Efekt [Úpravy](../concepts/effects.md#modify) zásad Azure je navržený tak, aby pomáhal ve správné úpravě značek bez ohledu na to, v jaké fázi zásad správného řízení prostředků se nacházíte. **Upravit** pomáhá, když:

- Jste v cloudu noví a nemáte žádné zásady zásad správného řízení značek
- Již mají tisíce prostředků bez zásad správného řízení značek
- Již máte existující taxonomii, kterou potřebujete změnit

V tomto kurzu dokončíte následující úkoly:

> [!div class="checklist"]
> - Identifikujte své obchodní požadavky
> - Mapování každého požadavku na definici zásad
> - Seskupení zásad značek do iniciativy

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="identify-requirements"></a>Identifikovat požadavky

Stejně jako každá dobrá implementace kontrol zásad správného řízení by požadavky měly pocházet z vašich obchodních potřeb a měly by být dobře pochopeny před vytvořením technických kontrol. Pro tento scénář kurzu jsou následující položky naše obchodní požadavky:

- Dvě požadované značky na všech zdrojích: _CostCenter_ a _Env_
- _CostCenter_ musí existovat ve všech kontejnerech a jednotlivých zdrojích.
  - Prostředky dědí z kontejneru, ve kterém se vejdou, ale mohou být jednotlivě přepsány
- _Env_ musí existovat na všech kontejnerech a jednotlivých zdrojích.
  - Prostředky určují prostředí podle schématu pojmenování kontejnerů a nemusí být přepsány.
  - Všechny prostředky v kontejneru jsou součástí stejného prostředí

## <a name="configure-the-costcenter-tag"></a>Konfigurace značky CostCenter

Pokud jde o specifické pro prostředí Azure spravované zásadami Azure, požadavky na značku _CostCenter_ vyžadují následující:

- Odepřít skupinám prostředků, které postrádají značku _CostCenter_
- Úprava prostředků pro přidání značky _CostCenter_ z nadřazené skupiny prostředků při chybě

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Odepřít skupinám prostředků, které postrádají značku CostCenter

Vzhledem k tomu, _že centrum nákladů_ pro skupinu prostředků nelze určit názvem skupiny prostředků, musí mít značku definovanou v požadavku na vytvoření skupiny prostředků. Následující pravidlo zásad s efektem [Odepřít](../concepts/effects.md#deny) zabraňuje vytvoření nebo aktualizaci skupin prostředků, které nemají značku _CostCenter:_

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
> Vzhledem k tomu, že toto pravidlo zásad cílí na skupinu prostředků, musí být _režim_ definice zásady "Vše" namísto indexované.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Úprava prostředků, které dědí značku CostCenter, když chybí

Druhá _potřeba CostCenter_ je pro všechny prostředky zdědit značku z nadřazené skupiny prostředků, když chybí. Pokud je značka již definována na prostředku, i když se liší od nadřazené skupiny prostředků, musí být ponechána samostatně. Následující pravidlo zásad používá [změnit](../concepts/effects.md#modify):

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

Toto pravidlo zásad používá operaci **add** namísto **addOrReplace,** protože nechceme měnit hodnotu značky, pokud je k dispozici při [nápravě existujících](../how-to/remediate-resources.md) prostředků. Používá také `[resourcegroup()]` funkci šablony k získání hodnoty značky z nadřazené skupiny prostředků.

> [!NOTE]
> Vzhledem k tomu, že toto pravidlo zásad se zaměřuje na prostředky, které podporují značky, musí být _režim_ definice zásad indexován. Tato konfigurace také zajišťuje, že tato zásada přeskočí skupiny prostředků.

## <a name="configure-the-env-tag"></a>Konfigurace značky Env

Pokud jde o specifické pro prostředí Azure spravované zásadami Azure, požadavky na značku _Env_ vyžadují následující:

- Úprava značky _Env_ ve skupině prostředků na základě schématu pojmenování skupiny prostředků
- Úprava značky _Env_ u všech prostředků ve skupině prostředků je stejná jako nadřazená skupina prostředků.

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Úprava skupin prostředků Značky Env na základě názvu

Zásada [úpravy](../concepts/effects.md#modify) je vyžadována pro každé prostředí, které existuje ve vašem prostředí Azure. Zásady Změnit pro každou z nich vypadá podobně jako tato definice zásad:

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
> Vzhledem k tomu, že toto pravidlo zásad cílí na skupinu prostředků, musí být _režim_ definice zásady "Vše" namísto indexované.

Tato zásada pouze porovnává skupiny prostředků s `prd-`ukázkovým schématem pojmenování používaným pro produkční zdroje společnosti . Složitější schéma pojmenování lze dosáhnout s několika podmínkami **shody** namísto **singlu, jako** je v tomto příkladu.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Úprava prostředků ke zdědění značky Env

Obchodní požadavek vyžaduje, aby všechny prostředky měly značku _Env,_ kterou má jejich nadřazená skupina prostředků. Tuto značku nelze přepsat, takže použijeme operaci **addOrReplace** s efektem [Modify.](../concepts/effects.md#modify) Ukázka změnit zásady vypadá jako následující pravidlo:

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
> Vzhledem k tomu, že toto pravidlo zásad se zaměřuje na prostředky, které podporují značky, musí být _režim_ definice zásad indexován. Tato konfigurace také zajišťuje, že tato zásada přeskočí skupiny prostředků.

Toto pravidlo zásad hledá všechny prostředky, které nemají hodnotu nadřazených skupin prostředků pro značku _Env_ nebo chybí značka _Env._ Odpovídající prostředky mají svou značku _Env_ nastavenou na hodnotu nadřazených skupin prostředků, i když značka již u prostředku existovala, ale s jinou hodnotou.

## <a name="assign-the-initiative-and-remediate-resources"></a>Přiřazení iniciativy a náprava zdrojů

Po vytvoření výše uvedených zásad značek je připojte k jedné iniciativě pro zásady správného řízení značek a přiřaďte je ke skupině pro správu nebo předplatnému. Iniciativa a zahrnuté zásady pak vyhodnocují dodržování stávajících prostředků a mění požadavky na nové nebo aktualizované prostředky, které odpovídají vlastnosti **if** v pravidle zásad. Zásada však automaticky neaktualizuje existující nekompatibilní prostředky s definovanými změnami značek.

Stejně jako [deployIfNotExists zásady](../concepts/effects.md#deployifnotexists) **Modi používá** nápravné úlohy změnit existující nekompatibilní prostředky. Postupujte podle pokynů [na Jak opravit prostředky](../how-to/remediate-resources.md) k identifikaci nekompatibilní **Změnit** prostředky a opravit značky na definované taxonomie.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste práci s prostředky z tohoto kurzu skončili, odstraňte některá z výše vytvořených přiřazení nebo definic pomocí následujících kroků:

1. V části **Vytváření** na levé straně stránky Zásad Azure vyberte **Definice** (nebo **přiřazení,** pokud se pokoušíte odstranit přiřazení).

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="review"></a>Revize

V tomto kurzu jste se dozvěděli o následujících úkolech:

> [!div class="checklist"]
> - Identifikovali jste své obchodní požadavky
> - Mapováno každý požadavek na definici zásad
> - Seskupení zásad značek do iniciativy

## <a name="next-steps"></a>Další kroky

Další informace o strukturách definic zásad najdete v tomto článku:

> [!div class="nextstepaction"]
> [Struktura definic Azure Policy](../concepts/definition-structure.md)