---
title: Vysvětlení oboru v Azure Policy
description: Popisuje pojem oboru v Azure Resource Manager a způsob, jakým se vztahuje na Azure Policy k řízení toho, které prostředky Azure Policy vyhodnotí.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90984427"
---
# <a name="understand-scope-in-azure-policy"></a>Vysvětlení oboru v Azure Policy

K dispozici je několik nastavení, která určují, které prostředky jsou schopné vyhodnocovat a které prostředky vyhodnocují Azure Policy. Primární koncept pro tyto ovládací prvky je _Rozsah_.
Podrobný přehled naleznete [v tématu Scope in Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
Tento článek vysvětluje dopad _oboru_ v Azure Policy a související objekty a vlastnosti.

## <a name="definition-location"></a>Umístění definice

První rozsah instancí, který používá Azure Policy, je při vytvoření definice zásady. Definice se dá uložit buď do skupiny pro správu, nebo do předplatného. Umístění určuje obor, do kterého lze iniciativu nebo zásadu přiřadit. Prostředky musí být v rámci hierarchie prostředků umístění definice k cíli pro přiřazení.

Pokud je umístění definice:

- V rámci tohoto předplatného se dají přiřadit definice zásady jenom pro prostředky s **předplatným** .
- Pro **skupinu pro správu** jsou k disřádek pouze prostředky v rámci podřízených skupin pro správu a podřízených odběrů. Pokud plánujete použít definici zásady pro několik předplatných, umístění musí být skupina pro správu, která obsahuje každé předplatné.

Umístění by mělo být kontejner prostředků, který sdílí všechny prostředky, pro které chcete použít definici zásady. Tento kontejner prostředků je obvykle skupina pro správu poblíž kořenové skupiny pro správu.

## <a name="assignment-scopes"></a>Obory přiřazení

Přiřazení má několik vlastností, které nastavují obor. Použití těchto vlastností určuje, který prostředek pro Azure Policy se má vyhodnotit a které prostředky se budou počítat s dodržováním předpisů. Tyto vlastnosti jsou mapovány na následující koncepty:

- Zahrnutí – hierarchii prostředků nebo jednotlivé prostředky by měly být vyhodnoceny pro dodržování předpisů definicí. `properties.scope`Vlastnost objektu přiřazení určuje, co má být zahrnuto a vyhodnocení kompatibility. Další informace najdete v tématu [definice přiřazení](./assignment-structure.md).

- Vyloučení – hierarchie prostředků nebo individuální prostředek by se neměla vyhodnotit pro dodržování předpisů definicí. Vlastnost `properties.notScopes` _Array_ objektu přiřazení určuje, co se má vyloučit. Prostředky v těchto oborech nejsou vyhodnoceny nebo zahrnuty do počtu dodržování předpisů. Další informace najdete v tématu [vyloučené obory definice přiřazení](./assignment-structure.md#excluded-scopes).

Kromě vlastností přiřazení zásad je objektem [výjimky zásad](./exemption-structure.md) . Výjimky zvyšují příběh oboru tím, že poskytují metodu pro identifikaci části přiřazení pro Nevyhodnocení.

- Výjimka (**Volná verze ve verzi Preview** ) – hierarchii prostředků nebo jednotlivé prostředky by měly být vyhodnoceny pro dodržování předpisů definicí, ale nebudou vyhodnoceny z důvodu nevyhovění nebo zmírnění pomocí jiné metody. Prostředky v tomto stavu se zobrazují jako **vyloučené** v sestavách dodržování předpisů, aby je bylo možné sledovat. Objekt výjimky je vytvořen v hierarchii prostředků nebo v samostatném prostředku jako podřízený objekt, který určuje rozsah výjimky. Hierarchii prostředků nebo jednotlivé prostředky můžou být vyloučené z více přiřazení. Výjimka může být nakonfigurována na vypršení platnosti podle plánu pomocí `expiresOn` Vlastnosti. Další informace najdete v tématu [definice výjimky](./exemption-structure.md).

  > [!NOTE]
  > Z důvodu dopadu udělení výjimky pro hierarchii prostředků nebo na jednotlivé prostředky mají výjimky další bezpečnostní opatření. Kromě vyžadování `Microsoft.Authorization/policyExemptions/write` operace v hierarchii prostředků nebo jednotlivých zdrojích musí mít tvůrce výjimky `exempt/Action` příkaz v cílovém přiřazení.

## <a name="scope-comparison"></a>Porovnání oboru

Následující tabulka představuje porovnání možností oboru:

| | Souborech | Vyloučení (notScopes) | Výjimky |
|---|:---:|:---:|:---:|
|**Vyhodnotí se prostředky.** | &#10004; | - | - |
|**Objekt Správce prostředků** | - | - | &#10004; |
|**Vyžaduje úpravu objektu přiřazení zásad** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [struktuře definic zásad](./definition-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).