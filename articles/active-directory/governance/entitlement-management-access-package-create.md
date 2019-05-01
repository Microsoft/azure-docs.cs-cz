---
title: Vytvořit nový balíček pro přístup ve službě Azure AD oprávnění management (Preview) – Azure Active Directory
description: Zjistěte, jak vytvořit nový balíček pro přístup z prostředků, které chcete sdílet ve službě Azure Active Directory management oprávnění (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866423"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Vytvořit nový balíček pro přístup ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Balíček přístup můžete to udělat o jednorázové nastavení zásad a prostředků, které automaticky spravuje přístup po dobu životnosti přístup k balíčku. Tento článek popisuje, jak vytvořit nový balíček pro přístup.

## <a name="overview"></a>Přehled

Všechny balíčky přístupu musíte vložit do kontejneru s názvem katalog. Katalog definuje prostředky můžete přidat do vašeho přístupu k balíčku. Pokud nezadáte katalog, zařadí se do obecné katalogu vašeho balíčku přístup. V současné době nelze přesunout existující balíček pro přístup do různých katalogu.

Všechny balíčky přístupu musí mít aspoň jednu zásadu. Zásady určit, kdo může vyžádat přístup k balíčku a také nastavení schválení a vypršení platnosti. Když vytvoříte nový balíček pro přístup, můžete vytvořit počáteční zásady pro uživatele ve vašem adresáři, pro uživatele není ve vašem adresáři pro správce pouze přímé přiřazení nebo můžete vytvořit zásady později.

Následující diagram znázorňuje proces vysoké úrovně, chcete-li vytvořit nový balíček pro přístup.

![Vytvořit proces přístupu k balíčku](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Začít nový balíček pro přístup

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům**.

    ![Správa nároků na webu Azure Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Klikněte na tlačítko **nový balíček pro přístup k**.

## <a name="basics"></a>Základy

Na **Základy** kartu, pojmenujte balíček přístup a zadejte katalogu, do kterého chcete vytvořit balíček přístup v.

1. Zadejte zobrazovaný název a popis pro přístup k balíčku. Uživatelé uvidí tyto informace při odesílání žádosti o přístup k balíčku.

1. V **katalogu** rozevíracího seznamu vyberte katalogu, kterou chcete vytvořit přístup zabalíte. Například může mít katalogu vlastníka, který spravuje marketingové prostředky, které mohou být požadována. V takovém případě můžete třeba vybrat marketingové katalogu.

    Zobrazí se pouze katalogy máte oprávnění k vytváření balíčků přístup v. Vytvoření balíčku přístup v existující katalog, musí být nejméně jednoho správce uživatelů, katalogu vlastník nebo správce přístupu k balíčku.

    ![Přístup k balíčku – základy](./media/entitlement-management-access-package-create/basics.png)

    Pokud chcete vytvořit váš přístup k balíčku v nový katalog, klikněte na tlačítko **vytvořit nový**. Zadejte název katalogu a popis a potom klikněte na tlačítko **vytvořit**.

    Při vytváření balíčku přístupu a všechny prostředky zahrnuté v ní se přidají do nového katalogu. Kromě toho se automaticky stanete prvním vlastníka do katalogu. Můžete přidat další katalogu vlastníky.

    Pokud chcete vytvořit nový katalog, musí být nejméně jednoho uživatele správce nebo Tvůrce katalogu.

1. Klikněte na **Další**.

## <a name="resource-roles"></a>Role prostředků

Na **role prostředků** kartu, vyberte zdroje, které chcete zahrnout do balíčku přístup.

1. Klikněte na typ prostředku, které chcete přidat (**skupiny**, **aplikací**, nebo **Sharepointové weby**).

1. V podokně vyberte možnost, která se zobrazí vyberte jeden nebo více prostředků ze seznamu.

    ![Přístup k balíčku - role prostředků](./media/entitlement-management-access-package-create/resource-roles.png)

    Pokud vytváříte balíček přístup v obecné katalogu nebo nový katalog, budete moct vybrat prostředek z adresáře, který vlastníte. Musí být nejméně jednoho uživatele správce nebo Tvůrce katalogu.

    Pokud vytváříte balíček přístup v existující katalog, můžete vybrat prostředek, který je již v katalogu nemusíte přitom vlastnit ho.

    Pokud jste uživatele správce nebo vlastníka katalogu, máte další možnost vybrat prostředky, které vlastníte, které nejsou ještě v katalogu. Pokud vyberete v katalogu vybrané prostředky nejsou aktuálně, tyto prostředky budou také přidaných do katalogu pro ostatní správci katalogu sestavovat balíčky přístup s. Pokud chcete vybrat prostředky, které jsou momentálně ve vybrané katalogu, zkontrolujte **vidět jenom** zaškrtnutí políčka v horní části vyberte posouvání.

1. Jakmile vyberete v prostředky, **Role** seznamu, vyberte roli, kterou chcete přiřadit uživatelům pro prostředek.

    ![Přístup k balíčku - výběr role prostředků](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klikněte na **Další**.

## <a name="policy"></a>Zásada

Na **zásady** kartě vytvoříte první zásada k určení, kdo může vyžádat přístup k balíčku a také nastavení schválení a vypršení platnosti. Později můžete vytvořit více zásad další skupiny uživatelů pro vyžádání přístupu k balíčku pomocí vlastních nastavení schválení a vypršení platnosti. Můžete také vytvořit zásady později.

1. Nastavte **vytvořte první zásady** přepnutím **nyní** nebo **později**.

    ![Přístup k balíčku – zásady](./media/entitlement-management-access-package-create/policy.png)

1. Pokud vyberete **později**, přejděte dolů k [revize + vytvořit](#review--create) část, která vytvoří váš přístup k balíčku.

1. Pokud vyberete **nyní**, proveďte kroky v jednom z následujících částí zásad.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Zkontrolovat a vytvořit

Na **zkontrolujte + vytvořit** kartu, můžete zkontrolovat nastavení a vyhledat všechny chyby ověření.

1. Projděte si nastavení přístupu k balíčku

    ![Přístup k balíčku - nastavení zásad pro povolení zásad](./media/entitlement-management-access-package-create/review-create.png)

1. Klikněte na tlačítko **vytvořit** k vytvoření balíčku přístup.

    Nový balíček pro přístup se zobrazí v seznamu balíčků přístup.

## <a name="next-steps"></a>Další postup

- [Úprava a správa existující balíček pro přístup](entitlement-management-access-package-edit.md)
- [Vytvoření a Správa katalogu](entitlement-management-catalog-create.md)
