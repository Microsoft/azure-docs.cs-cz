---
title: Vytvoření & Správa katalogu prostředků v rámci správy nároků – Azure AD
description: Naučte se vytvořit nový kontejner prostředků a přistupovat k balíčkům v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c6f37a1b5cc16c80af8316cfeae164d58ea9796
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552163"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Vytvoření a správa katalogu prostředků v Azure AD – Správa nároků

## <a name="create-a-catalog"></a>Vytvoření katalogu

Katalog je kontejner prostředků a přístup k balíčkům. Katalog můžete vytvořit v případě, že chcete seskupit související prostředky a přistupovat k balíčkům. Ten vytvoří katalog jako prvního vlastníka katalogu. Vlastník katalogu může přidat další vlastníky katalogu.

**Požadovaná role:** Globální správce, Správce uživatelů nebo Tvůrce katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy**.

    ![Katalogy správy nároků v Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klikněte na **Nový katalog**.

1. Zadejte jedinečný název pro katalog a zadejte popis.

    Uživatelé uvidí tyto informace v podrobnostech balíčku přístupu.

1. Pokud chcete, aby byly balíčky pro přístup v tomto katalogu k dispozici uživatelům, aby si vyžádali, jakmile budou vytvořeny, nastavte možnost **povoleno** na **Ano**.

1. Pokud chcete povolit uživatelům ve vybraných externích adresářích, aby mohli vyžadovat přístup k balíčkům v tomto katalogu, nastavte možnost **povoleno pro externí uživatele** na **Ano**.

    ![Podokno nového katalogu](./media/entitlement-management-shared/new-catalog.png)

1. Kliknutím na **vytvořit** vytvořte katalog.

### <a name="creating-a-catalog-programmatically"></a>Programové vytvoření katalogu

Katalog můžete vytvořit také pomocí Microsoft Graph.  Uživatel v příslušné roli s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API a [vytvořit accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Přidání prostředků do katalogu

Aby bylo možné zahrnout prostředky do balíčku pro přístup, musí tyto prostředky existovat v katalogu. Typy prostředků, které můžete přidat, jsou skupiny, aplikace a weby SharePointu Online. Skupiny můžou být cloudové Microsoft 365 skupiny nebo cloudové skupiny zabezpečení Azure AD. Aplikace můžou být Azure AD podnikové aplikace, včetně aplikací SaaS a vašich vlastních aplikací, které jsou federované do Azure AD. Weby můžou být weby SharePointu Online nebo kolekce webů SharePointu Online.

**Požadovaná role:** [Přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) najdete v tématu požadované role.

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, do kterého chcete přidat prostředky.

1. V nabídce vlevo klikněte na **prostředky**.

1. Klikněte na **Přidat prostředky**.

1. Klikněte na typ prostředku: **skupiny a týmy**, **aplikace** nebo **weby služby SharePoint**.

    Pokud nevidíte prostředek, který chcete přidat, nebo nemůžete přidat prostředek, ujistěte se, že máte požadovanou roli Azure AD Directory a správu oprávnění. Je možné, že budete potřebovat někoho, kdo s požadovanými rolemi přidá prostředek do vašeho katalogu. Další informace najdete v tématu [požadované role pro přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Vyberte jeden nebo více prostředků typu, který chcete přidat do katalogu.

    ![Přidání prostředků do katalogu](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Po dokončení klikněte na tlačítko **Přidat**.

    Tyto prostředky teď můžou být zahrnuté do balíčků přístupu v katalogu.

### <a name="add-a-multi-geo-sharepoint-site"></a>Přidat web s více geografickými weby služby SharePoint

1. Pokud máte [více geograficky](https://docs.microsoft.com/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) povolených pro SharePoint, vyberte prostředí, ze kterého chcete vybrat weby.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Přístup k balíčku – přidání rolí prostředků – výběr více geografických webů SharePointu":::

1. Pak vyberte lokality, které chcete přidat do katalogu. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Programové přidání prostředku do katalogu

Prostředek můžete také přidat do katalogu pomocí Microsoft Graph.  Uživatel v příslušné roli nebo v katalogu a vlastníka prostředků s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API a [vytvořit accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Odebrání prostředků z katalogu

Prostředky můžete odebrat z katalogu. Prostředek se dá z katalogu odebrat jedině v případě, že se nepoužívá v žádném z balíčků pro přístup ke katalogu.

**Požadovaná role:** [Přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) najdete v tématu požadované role.

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, ze kterého chcete odebrat prostředky.

1. V nabídce vlevo klikněte na **prostředky**.

1. Vyberte prostředky, které chcete odebrat.

1. Klikněte na **Odebrat** (nebo klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **Odebrat prostředek**).


## <a name="add-additional-catalog-owners"></a>Přidat další vlastníky katalogu

Uživatel, který vytvořil katalog, se stal prvním vlastníkem katalogu. Chcete-li delegovat správu katalogu, přidejte uživatele do role vlastníka katalogu. To pomáhá sdílet zodpovědnost za správu katalogu. 

Pomocí těchto kroků přiřaďte uživatele k roli vlastníka katalogu:

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, do kterého chcete přidat správce.

1. V nabídce vlevo klikněte na **role a správci**.

    ![Role a správci katalogů](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknutím na **Přidat vlastníky** můžete vybrat členy pro tyto role.

1. Kliknutím na **Vybrat** přidejte tyto členy.

## <a name="edit-a-catalog"></a>Úprava katalogu

Můžete upravit název a popis katalogu. Uživatelé uvidí tyto informace v podrobnostech balíčku přístupu.

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, který chcete upravit.

1. Na stránce **Přehled** katalogu klikněte na **Upravit**.

1. Upravte název, popis nebo povolené nastavení katalogu.

    ![Upravit nastavení katalogu](./media/entitlement-management-shared/catalog-edit.png)

1. Klikněte na **Uložit**.

## <a name="delete-a-catalog"></a>Odstranění katalogu

Katalog můžete odstranit, ale pouze v případě, že nemá žádné balíčky přístupu.

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, který chcete odstranit.

1. V **přehledu** katalogu klikněte na **Odstranit**.

1. V zobrazeném okně se zprávou klikněte na **Ano**.

### <a name="deleting-a-catalog-programmatically"></a>Programové odstranění katalogu

Katalog můžete také odstranit pomocí Microsoft Graph.  Uživatel v příslušné roli s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API a [Odstranit accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Další kroky

- [Zásady správného řízení přístupu pro přístup k správcům balíčků](entitlement-management-delegate-managers.md)