---
title: Vytvoření nového balíčku pro přístup ve správě nároků ve službě Azure AD (Preview) – Azure Active Directory
description: Naučte se, jak vytvořit nový balíček pro přístup k prostředkům, které chcete sdílet, v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/24/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a99aa766ed4e6cacbe22933db226b2037d3e736d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170001"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Vytvoření nového balíčku pro přístup ve správě nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.
> Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Balíček pro přístup vám umožní jednorázovou instalaci prostředků a zásad, které automaticky spravují přístup po dobu života balíčku přístupu. Tento článek popisuje, jak vytvořit nový balíček pro přístup.

## <a name="overview"></a>Přehled

Všechny balíčky přístupu musí být vloženy do kontejneru s názvem Catalog. Katalog definuje prostředky, které můžete přidat do balíčku pro přístup. Pokud nezadáte katalog, balíček pro přístup se vloží do katalogu pro obecné. V současné době nemůžete přesunout existující balíček přístupu do jiného katalogu.

Všechny balíčky pro přístup musí mít aspoň jednu zásadu. Zásady určují, kdo může žádat o přístup k balíčku a také nastavení schválení a vypršení platnosti. Při vytváření nového přístupového balíčku můžete vytvořit počáteční zásady pro uživatele v adresáři, pro uživatele, kteří nejsou ve vašem adresáři, jenom pro přímé přiřazení správců, nebo můžete zvolit vytvoření zásady později.

Následující diagram znázorňuje proces vysoké úrovně pro vytvoření nového přístupového balíčku.

![Vytvoření procesu přístupového balíčku](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Spustit nový balíček pro přístup

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na možnost **přístup k balíčkům**.

    ![Správa nároků v Azure Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Klikněte na **nový balíček pro přístup**.

## <a name="basics"></a>Práce

Na kartě **základy** udělíte přístupovému balíčku název a určíte, ke kterému katalogu se má balíček pro přístup vytvořit.

1. Zadejte zobrazovaný název a Popis balíčku pro přístup. Uživatelům se zobrazí tyto informace, když odešlou žádost o přístup k balíčku.

1. V rozevíracím seznamu **katalog** vyberte katalog, ve kterém chcete vytvořit balíček pro přístup. Můžete mít například vlastníka katalogu, který spravuje všechny prostředky marketingu, které mohou být požadovány. V takovém případě můžete vybrat marketingový katalog.

    Zobrazí se pouze katalogy, které máte oprávnění k vytváření balíčků přístupu v nástroji. Pokud chcete vytvořit balíček přístupu v existujícím katalogu, musíte mít aspoň globálního správce, správce uživatele, vlastníka katalogu v tomto katalogu nebo přístup správce balíčků v katalogu.

    ![Přístup k základům balíčku](./media/entitlement-management-access-package-create/basics.png)

    Pokud jste globální správce nebo Správce uživatelů a chcete vytvořit balíček přístupu v novém katalogu, který není v seznamu, klikněte na **vytvořit nový**. Zadejte název a popis katalogu a pak klikněte na **vytvořit**.

    Balíček přístupu, který vytváříte, a všechny prostředky, které jsou v něm obsažené, se přidají do nového katalogu. Později můžete také přidat další vlastníky katalogu.


1. Klikněte na tlačítko **Další**.

## <a name="resource-roles"></a>Role prostředků

Na kartě **role prostředků** vyberte prostředky, které se mají zahrnout do balíčku pro přístup.  Uživatelé, kteří požadují a obdrží balíček pro přístup, obdrží všechny role prostředků v balíčku pro přístup.

1. Klikněte na typ prostředku, který chcete přidat (**skupiny a týmy**, **aplikace**nebo **weby SharePointu**).

1. V zobrazeném podokně vyberte jeden nebo více prostředků ze seznamu.

    ![Přístup k prostředkům balíčku – role](./media/entitlement-management-access-package-create/resource-roles.png)

    Pokud vytváříte balíček přístupu v katalogu obecné nebo v novém katalogu, budete moci vybrat libovolný prostředek z adresáře, který vlastníte. Musíte mít aspoň globálního správce, Správce uživatelů nebo Tvůrce katalogu.

    Pokud vytváříte balíček přístupu v existujícím katalogu, můžete vybrat libovolný prostředek, který je již v katalogu, aniž by mu byl vlastník.

    Pokud jste globální správce, správce uživatele nebo vlastníka katalogu, máte další možnost výběru nevlastních prostředků, které ještě nejsou v katalogu. Pokud vyberete prostředky, které nejsou aktuálně ve vybraném katalogu, tyto prostředky budou také přidány do katalogu pro jiné Správce katalogu, aby mohli vytvářet balíčky přístupu pomocí nástroje. Pokud chcete pouze vybrat prostředky, které jsou aktuálně ve vybraném katalogu, zaškrtněte políčko **pouze** v horní části okna Vybrat pan.

1. Jakmile vyberete prostředky, v seznamu **role** vyberte roli, kterou chcete přiřadit uživatelům pro daný prostředek.

    ![Přístup k balíčku – výběr role prostředků](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klikněte na tlačítko **Další**.

## <a name="policy"></a>politických

Na kartě **zásady** vytvoříte první zásadu, která určí, kdo může žádat o přístup k balíčku a také nastavení schválení a vypršení platnosti. Později můžete vytvořit další zásady, které umožní více skupinám uživatelů žádat o přístup k balíčku s vlastním nastavením schválení a vypršení platnosti. Zásadu můžete vytvořit také později.

1. Nastavte přepínač **vytvořit první zásadu** na **nyní** nebo **později**.

    ![Přístup k balíčku – zásady](./media/entitlement-management-access-package-create/policy.png)

1. Pokud vyberete **později**, přeskočte na oddíl [Revize + vytvořit](#review--create) a vytvořte balíček přístupu.

1. Pokud vyberete možnost **nyní**, proveďte kroky v jedné z následujících sekcí zásad.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Zkontrolovat a vytvořit

Na kartě **Revize + vytvořit** můžete zkontrolovat nastavení a zkontrolovat chyby ověřování.

1. Kontrola nastavení balíčku přístupu

    ![Přístup k balíčku – nastavení zásad – Povolit zásadu](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknutím na **vytvořit** vytvořte balíček pro přístup.

    Nový balíček pro přístup se zobrazí v seznamu balíčků přístupu.

## <a name="next-steps"></a>Další kroky

- [Úprava a Správa stávajícího balíčku pro přístup](entitlement-management-access-package-edit.md)
