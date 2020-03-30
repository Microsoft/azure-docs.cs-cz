---
title: Vytváření & správa prostředků ve správě nároků – Azure AD
description: Zjistěte, jak vytvořit nový kontejner prostředků a získat přístup k balíčkům ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbafb0c506fd34e975efbe08c75c15c8c0b3888e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128941"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Vytvoření a správa katalogu prostředků ve správě nároků Azure AD

## <a name="create-a-catalog"></a>Vytvoření katalogu

Katalog je kontejner prostředků a přístup k balíčkům. Katalog vytvoříte, pokud chcete seskupit související prostředky a získat přístup k balíčkům. Ten, kdo katalog vytvoří, se stane prvním vlastníkem katalogu. Vlastník katalogu můžete přidat další vlastníky katalogu.

**Předpokladová role:** Globální správce, správce uživatele nebo tvůrce katalogu

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klepněte na **položku Katalogy**.

    ![Katalogy pro správu nároků na webu Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klepněte na **položku Nový katalog**.

1. Zadejte jedinečný název katalogu a zadejte popis.

    Uživatelé uvidí tyto informace v podrobnostech přístupového balíčku.

1. Pokud chcete, aby přístupové balíčky v tomto katalogu byly k dispozici uživatelům, aby je mohli požádat ihned po jejich vytvoření, nastavte **možnost Povoleno** na **Ano**.

1. Chcete-li uživatelům ve vybraných externích adresářích povolit, aby mohli v tomto katalogu požadovat přístupové balíčky, nastavte **možnost Povoleno pro externí uživatele** na **ano**.

    ![Podokno Nový katalog](./media/entitlement-management-shared/new-catalog.png)

1. Kliknutím na **Vytvořit** vytvořte katalog.

### <a name="creating-a-catalog-programmatically"></a>Programové vytvoření katalogu

Katalog můžete také vytvořit pomocí aplikace Microsoft Graph.  Uživatel v příslušné roli s aplikací, která `EntitlementManagement.ReadWrite.All` má delegované oprávnění můžete volat rozhraní API [k vytvoření accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Přidání zdrojů do katalogu

Chcete-li zahrnout prostředky do balíčku přístupu, musí existovat prostředky v katalogu. Typy prostředků, které můžete přidat, jsou skupiny, aplikace a weby SharePointu Online. Skupiny můžou být skupiny Office 365 vytvořené v cloudu nebo cloudové skupiny zabezpečení Azure AD. Aplikace mohou být podnikové aplikace Azure AD, včetně aplikací SaaS a vlastních aplikací federovaných do Azure AD. Weby můžou být weby SharePointu Online nebo kolekce webů SharePointu Online.

**Předpokladová role:** Viz [Povinné role pro přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a otevřete katalog, do kterého chcete přidat prostředky.

1. V levé nabídce klepněte na **položku Zdroje**.

1. Klepněte na tlačítko **Přidat zdroje**.

1. Klikněte na typ prostředku: **Skupiny a týmy**, **Aplikace**nebo **Weby služby SharePoint**.

    Pokud nevidíte prostředek, který chcete přidat, nebo nejste schopni přidat prostředek, ujistěte se, že máte požadovanou roli adresáře Azure AD a roli správy nároků. Může být nutné, aby někdo s požadovanými rolemi přidal prostředek do katalogu. Další informace naleznete v [tématu Povinné role pro přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Vyberte jeden nebo více prostředků typu, který chcete přidat do katalogu.

    ![Přidání zdrojů do katalogu](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Po dokončení klepněte na tlačítko **Přidat**.

    Tyto prostředky mohou být nyní zahrnuty v přístupových balíčcích v rámci katalogu.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Programové přidání zdroje do katalogu

Můžete také přidat prostředek do katalogu pomocí aplikace Microsoft Graph.  Uživatel v příslušné roli nebo vlastník katalogu a prostředku s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API k vytvoření [accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Odebrání prostředků z katalogu

Prostředky můžete odebrat z katalogu. Prostředek lze odebrat z katalogu pouze v případě, že není používán v žádném z přístupových balíčků katalogu.

**Předpokladová role:** Viz [Povinné role pro přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a otevřete katalog, ze kterého chcete odebrat prostředky.

1. V levé nabídce klepněte na **položku Zdroje**.

1. Vyberte prostředky, které chcete odebrat.

1. Klikněte na **Odebrat** (nebo klikněte na tři tečky (**...**) a potom klikněte na **Odebrat prostředek**).

## <a name="add-additional-catalog-owners"></a>Přidání dalších vlastníků katalogu

Uživatel, který katalog vytvořil, se stane prvním vlastníkem katalogu. Chcete-li delegovat správu katalogu, přidejte uživatele do role vlastníka katalogu. To pomáhá sdílet odpovědnosti za správu katalogu. 

Chcete-li přiřadit uživatele k roli vlastníka katalogu, postupujte takto:

**Předpokladová role:** Globální správce, správce uživatele nebo vlastník katalogu

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a potom otevřete katalog, do kterého chcete přidat správce.

1. V levé nabídce klikněte na **Role a správci**.

    ![Role katalogů a správci](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknutím na **Přidat vlastníky** vyberte členy pro tyto role.

1. Chcete-li přidat tyto členy, klepněte na **tlačítko Vybrat.**

## <a name="edit-a-catalog"></a>Úprava katalogu

Můžete upravit název a popis katalogu. Uživatelé vidí tyto informace v podrobnostech přístupového balíčku.

**Předpokladová role:** Globální správce, správce uživatele nebo vlastník katalogu

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a otevřete katalog, který chcete upravit.

1. Na stránce **Přehled** katalogu klikněte na **Upravit**.

1. Upravte název, popis nebo povolené nastavení katalogu.

    ![Upravit nastavení katalogu](./media/entitlement-management-shared/catalog-edit.png)

1. Klikněte na **Uložit**.

## <a name="delete-a-catalog"></a>Odstranění katalogu

Katalog můžete odstranit, ale pouze v případě, že nemá žádné přístupové balíčky.

**Předpokladová role:** Globální správce, správce uživatele nebo vlastník katalogu

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a otevřete katalog, který chcete odstranit.

1. V **přehledu**katalogu klepněte na tlačítko **Odstranit**.

1. V posuzovacím poli se zprávou klepněte na tlačítko **Ano**.

### <a name="deleting-a-catalog-programmatically"></a>Programové odstranění katalogu

Katalog můžete také odstranit pomocí aplikace Microsoft Graph.  Uživatel v příslušné roli s aplikací, která `EntitlementManagement.ReadWrite.All` má delegované oprávnění můžete volat rozhraní API [odstranit accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Další kroky

- [Delegovat zásady správného řízení přístupu pro přístup ke správcům balíčků](entitlement-management-delegate-managers.md)
