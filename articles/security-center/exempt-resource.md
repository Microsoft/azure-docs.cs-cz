---
title: Vyloučení prostředku z Azure Security Center doporučení zabezpečení a bezpečného skóre
description: Zjistěte, jak vyloučit prostředek z doporučení zabezpečení a zabezpečeného skóre.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 347d5ac9de164f2e96340df71fd3b1b908e607c1
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372740"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Vyloučení prostředku z doporučení a skóre zabezpečení

Základní priorita každého bezpečnostního týmu se snaží zajistit, aby se analytiké mohli soustředit na úkoly a incidenty, které jsou v souvislosti s organizací. Security Center má mnoho funkcí pro přizpůsobení informací, které nastavíte jako prioritní, a zajištění, že vaše zabezpečené skóre představuje platný odraz rozhodnutí zabezpečení vaší organizace. Vyloučení prostředků je taková funkce.

Když prozkoumáte doporučení zabezpečení v Azure Security Center, je jedním z prvních informací, které jste si prozkoumali, seznam ovlivněných prostředků.

V některých případech bude prostředek uvedený jako seznam, který by se neměl zahrnovat. Mohl by být opraven procesem, který není sledován nástrojem Security Center. Nebo možná vaše organizace rozhodla přijmout riziko pro konkrétní prostředek. 

V takových případech můžete vytvořit pravidlo výjimky a ujistit se, že prostředek není v budoucnu uvedený s poškozenými prostředky a nemá vliv na vaše zabezpečené skóre. 

Prostředek bude uvedený jako nepoužitý a důvod se zobrazí jako vyloučený s vybraným odůvodněním.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Stanov|Toto je funkce zásad Azure Premium, která je nabízená pro zákazníky v Azure Defenderu bez dalších nákladů. Pro ostatní uživatele se poplatky můžou v budoucnu uplatňovat.|
|Požadované role a oprávnění:|**Vlastník předplatného** nebo **Přispěvatel zásad** pro vytvoření výjimky<br>Chcete-li vytvořit pravidlo, potřebujete oprávnění k úpravám zásad v Azure Policy.<br>Další informace najdete v [Azure Policy oprávnění služby Azure RBAC](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="create-an-exemption-rule"></a>Vytvořit pravidlo výjimky

1. V seznamu prostředků, které nejsou v pořádku, vyberte nabídku se třemi tečkami (...) pro prostředek, který chcete vyloučit.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Vytvořit možnost výjimky z kontextové nabídky":::

    Otevře se podokno vytvořit výjimku.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Podokno vytvořit výjimku":::

1. Zadejte kritéria a vyberte kritéria pro vyloučení tohoto prostředku:
    - **Zmírňované** – tento problém není relevantní pro prostředek, protože ho zpracovává jiný nástroj nebo proces než ten, který je navržený.
    - **Vypuštění** – přijetí rizika pro tento prostředek
1. Vyberte **Uložit**.
1. Po chvíli (může to trvat až 24 hodin):
    - Prostředek nemá vliv na vaše zabezpečené skóre.
    - Prostředek je uvedený na kartě **netýká** se stránky s podrobnostmi o doporučení.
    - Informační pruh v horní části stránky s podrobnostmi o doporučení uvádí počet vyloučených prostředků:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Počet vyloučených prostředků":::

1. Chcete-li zkontrolovat vyloučené prostředky, otevřete kartu **Nepoužito** .

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Úprava výjimky":::

    Důvody pro každou výjimku jsou obsaženy v tabulce (1).

    Chcete-li změnit nebo odstranit výjimku, vyberte nabídku se třemi tečkami ("..."), jak je zobrazeno (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Projděte si všechna pravidla výjimek v rámci vašeho předplatného.

Pravidla výjimky pomocí zásad Azure vytvoří výjimku pro prostředek na přiřazení zásady.

Pomocí Azure Policy můžete sledovat veškerou výjimku na stránce **výjimka** :

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Stránka s výjimkou Azure Policy":::



## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vyloučit prostředek z doporučení, aby neovlivnilo vaše zabezpečené skóre. Další informace o zabezpečeném skóre najdete v těchto tématech:

- [Skóre zabezpečení ve službě Azure Security Center](secure-score-security-controls.md)