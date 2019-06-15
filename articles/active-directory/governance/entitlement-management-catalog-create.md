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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541612"
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

Pokud chcete zahrnout prostředky v balíčku přístup, musí existovat prostředky v katalogu. Typy prostředků, které můžete přidat jsou skupiny, aplikace a weby SharePoint Online.

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete katalogu, které chcete přidat prostředky.

1. V nabídce vlevo klikněte na tlačítko **prostředky**.

1. Klikněte na tlačítko **přidat prostředky**.

1. Klikněte na typ prostředku: **Skupiny**, **aplikací**, nebo **Sharepointové weby**.

    Pokud jste autorem katalogu, můžete přidat jakékoli skupiny Office 365 nebo skupiny zabezpečení Azure AD, které vlastníte do katalogu. Pokud je skupina, kterou chcete přiřadit uživatelům, ale není vlastníkem skupiny, musíte mít uživatele správce přidat tuto skupiny do katalogu.

    Pokud jste autorem katalogu, můžete přidat jakékoli podniková aplikace Azure AD, které vlastníte, včetně aplikací SaaS a vlastním aplikací Federovaná do služby Azure AD, do katalogu. Pokud je aplikace, které chcete přiřadit uživatelům, ale není vlastníkem, musíte mít správce uživatelů, přidání aplikace do katalogu. Jakmile aplikace je součástí katalogu, vyberete některý z role aplikace, přístup k balíčku.

1. Vyberte jeden nebo více prostředků typu, který chcete přidat do katalogu.

1. Až budete hotovi, klikněte na tlačítko **přidat**.

    Tyto prostředky mohou být součástí teď přístup balíčky v katalogu.

## <a name="remove-resources-from-a-catalog"></a>Odebrat prostředky z katalogu

Odebrat prostředky z katalogu. Zdroj lze odebrat pouze z katalogu Pokud se nepoužívá v žádném z balíčků přístup do katalogu.

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete chcete odebrat prostředky z katalogu.

1. V nabídce vlevo klikněte na tlačítko **prostředky**.

1. Vyberte prostředky, které chcete odebrat.

1. Klikněte na tlačítko **odebrat** (nebo klikněte na tlačítko se třemi tečkami ( **...** ) a potom klikněte na tlačítko **odebrat prostředek**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Přidat vlastníky katalogu nebo přístup ke správci balíčků

Pokud chcete delegovat správu katalogu nebo balíčky přístup v katalogu, přidání vlastníků katalogu nebo přístup ke správci balíčků. Kdo vytvoří katalog se stane první Vlastník katalogu.

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete katalogu, které chcete přidat oprávnění správce.

1. V nabídce vlevo klikněte na tlačítko **role a správci**.

1. Klikněte na tlačítko **přidat vlastníky** nebo **přidat přístup správců balíčků** vyberte členy pro tyto role.

1. Klikněte na tlačítko **vyberte** pro přidání těchto členů.

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

- [Vytvoření a Správa balíčku přístup](entitlement-management-access-package-create.md)
