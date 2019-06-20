---
title: Vytvoření a správa katalog ve správě oprávnění Azure AD (Preview) – Azure Active Directory
description: Zjistěte, jak vytvořit nový kontejner prostředků a balíčky přístup ve službě Azure Active Directory management oprávnění (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/29/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5988f4723f1ef73cf0767ef8ac1b9adf3c1435d
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190246"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Vytvoření a správa katalog ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Vytvoření katalogu

Katalog je kontejner, prostředků a přístup k balíčkům. Můžete vytvořit katalog, když chcete seskupovat související prostředky a přístup k balíčkům. Kdo vytvoří katalog se stane první Vlastník katalogu. Katalog vlastníka můžete přidat další katalogu vlastníky.

**Požadované role:** Uživatel správce nebo Tvůrce katalogu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy**.

    ![Správa katalogů nárok na webu Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klikněte na tlačítko **nový katalog**.

1. Zadejte jedinečný název katalogu a také popis.

    Uživatelům se zobrazí tyto informace v podrobnostech přístup k balíčku.

1. Pokud chcete přístup balíčky v tomto katalogu bude k dispozici pro uživatele k vyžádání, jakmile se vytvoří, nastavte **povoleno** k **Ano**.

1. Pokud chcete povolit uživatelům ve vybrané adresáře externí bude moct požádat o přístup balíčky v tomto katalogu, nastavte **povolená pro externí uživatele** k **Ano**.

    ![Nové podokno katalogu](./media/entitlement-management-catalog-create/new-catalog.png)

1. Klikněte na tlačítko **vytvořit** vytvoření katalogu.

## <a name="add-resources-to-a-catalog"></a>Přidat prostředky do katalogu

Pokud chcete zahrnout prostředky v balíčku přístup, musí existovat prostředky v katalogu. Typy prostředků, které můžete přidat jsou skupiny, aplikace a weby SharePoint Online. Skupiny může být cloudu vytvořené skupiny Office 365 nebo Azure cloud vytvoří skupiny zabezpečení služby AD. Může se jednat o podnikových aplikací Azure AD, včetně aplikací SaaS a vlastním aplikacím Federovaná do služby Azure AD. Weby SharePoint Online nebo SharePoint Online kolekce webů, může být weby.

**Požadované role:** Zobrazit [požadované role, které chcete přidat prostředky do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete katalogu, které chcete přidat prostředky.

1. V nabídce vlevo klikněte na tlačítko **prostředky**.

1. Klikněte na tlačítko **přidat prostředky**.

1. Klikněte na typ prostředku: **Skupiny**, **aplikací**, nebo **Sharepointové weby**.

    Pokud nevidíte prostředek, který chcete přidat nebo nemůžete přidat prostředek, ujistěte se, že máte požadované role adresáře Azure AD a role pro správu oprávnění. Můžete potřebovat mít někdo požadované role přidejte prostředek do katalogu. Další informace najdete v tématu [požadované role, které chcete přidat prostředky do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Vyberte jeden nebo více prostředků typu, který chcete přidat do katalogu.

1. Až budete hotovi, klikněte na tlačítko **přidat**.

    Tyto prostředky mohou být součástí teď přístup balíčky v katalogu.

## <a name="remove-resources-from-a-catalog"></a>Odebrat prostředky z katalogu

Odebrat prostředky z katalogu. Zdroj lze odebrat pouze z katalogu Pokud se nepoužívá v žádném z balíčků přístup do katalogu.

**Požadované role:** Zobrazit [požadované role, které chcete přidat prostředky do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete chcete odebrat prostředky z katalogu.

1. V nabídce vlevo klikněte na tlačítko **prostředky**.

1. Vyberte prostředky, které chcete odebrat.

1. Klikněte na tlačítko **odebrat** (nebo klikněte na tlačítko se třemi tečkami ( **...** ) a potom klikněte na tlačítko **odebrat prostředek**).

## <a name="edit-a-catalog"></a>Upravit katalogu

Můžete upravit název a popis pro katalog. Uživatelé uvidí tyto informace v podrobnostech přístup k balíčku.

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete katalogu, kterou chcete upravit.

1. V katalogu **přehled** klikněte na **upravit**.

1. Upravte název nebo popis do katalogu.

1. Klikněte na **Uložit**.

## <a name="delete-a-catalog"></a>Odstranit katalog

Můžete odstranit katalog, ale pouze pokud nemá žádné balíčky přístup.

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete katalogu, kterou chcete odstranit.

1. V katalogu **přehled**, klikněte na tlačítko **odstranit**.

1. V okně se zprávou, která se zobrazí, klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další postup

- [Přidat katalog creator](entitlement-management-delegate.md#add-a-catalog-creator)
- [Vytvoření a Správa balíčku přístup](entitlement-management-access-package-create.md)
