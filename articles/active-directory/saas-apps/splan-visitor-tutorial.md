---
title: 'Kurz: Integrace Azure Active Directory jednotného přihlašování (SSO) k návštěvníkům Splan | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a návštěvníkem Splan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644676"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Kurz: Integrace Azure Active Directory jednotného přihlašování (SSO) k návštěvníkovi Splan

V tomto kurzu se dozvíte, jak integrovat Splan návštěvníky s Azure Active Directory (Azure AD). Když integrujete návštěvníky Splan s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup k Splan návštěvníkovi.
* Umožněte uživatelům, aby se k Splan návštěvníka automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr s povoleným jednotným přihlašováním Splan návštěvníka (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Splan návštěvník podporuje jednotné přihlašování iniciované IdP.

## <a name="add-splan-visitor-from-the-gallery"></a>Přidání návštěvníka Splan z Galerie

Pokud chcete nakonfigurovat integraci Splan návštěvníka do Azure AD, přidejte do seznamu spravovaných aplikací pro SaaS Splan návštěvníka z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Splan (návštěvník** ).
1. Na panelu výsledků vyberte **návštěvníka Splan** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Konfigurace a testování jednotného přihlašování Azure AD pro návštěvníky Splan

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Splan návštěvníka pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Splan návštěvníkovi.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Splan návštěvníka, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** , který otestuje jednotné přihlašování Azure AD s testovacím uživatelem B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Nakonfigurujte](#configure-splan-visitor-sso)** jednotné přihlašování Splan pro návštěvníky a nakonfigurujte nastavení jednotného přihlašování pomocí návštěvníka Splan.
    1. **[Vytvořte testovacího uživatele Splan návštěvníka](#create-a-splan-visitor-test-user)** , který bude mít protějšek B. Simon v Splan návštěvníku, který je propojený s reprezentací uživatele Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V Azure Portal na stránce integrace aplikace pro **návštěvníky Splan** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte pro **konfiguraci základní konfigurace SAML** ikonu **Upravit/pero** , která nastavení upraví.

   ![Snímek obrazovky se zvýrazněním ikony úprav/pera pro základní konfiguraci SAML.](common/edit-urls.png)

1. V **základní části Konfigurace SAML** je aplikace předem nakonfigurovaná a nezbytné adresy URL se předem naplní v Azure. Kliknutím na tlačítko **Uložit** uložte konfiguraci.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML federačních metadat**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Snímek obrazovky se zvýrazněným odkazem na stažení XML federačních metadat.](common/metadataxml.png)

1. V části **nastavit návštěvníky Splan** zkopírujte na základě vašeho požadavku příslušnou adresu URL nebo adresy URL.

    ![Snímek obrazovky s zvýrazněním oddílu adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte své uživatelské jméno ve _username@companydomain.extension_ formátu. Zadejte například **B.Simon@contoso.com** .
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Splan návštěvníkovi.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **návštěvníka Splan** a otevřete Přehled aplikace.
1. Vyhledejte část **Správa** a pak vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a potom v dolní části obrazovky klikněte na **Vybrat** .
1. Pokud má uživatel přiřazenou roli, vyberte ho z rozevírací nabídky **Vybrat roli** . Pokud pro tuto aplikaci nebyla nastavena žádná role, ponechte vybranou možnost **výchozí role přístupu** .
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-splan-visitor-sso"></a>Konfigurace jednotného přihlašování návštěvníků Splan

Pokud chcete nakonfigurovat jednotné přihlašování s Splan návštěvníkem, odešlete stažený **kód XML federačních metadat** , který jste stáhli, a příslušné adresy url od Azure Portal do [týmu podpory Splan pro návštěvníky](mailto:support@splan.com). Tím se zajistí, že se na obou stranách správně nastaví připojení SAML SSO.

### <a name="create-a-splan-visitor-test-user"></a>Vytvoření testovacího uživatele Splan návštěvníka

Vytvořte testovacího uživatele s názvem **Britta Simon** v Splan návštěvníkovi. Spolupracovat s [týmem podpory Splan návštěvníka](mailto:support@splan.com) pro přidání uživatele do Splan návštěvníka. Před použitím jednotného přihlašování musíte uživatele vytvořit a aktivovat.

## <a name="test-sso"></a>Test SSO

Otestujte konfiguraci jednotného přihlašování Azure AD pomocí jedné z následujících možností:

* **Azure Portal**: vyberte možnost **testovat tuto aplikaci** , chcete-li se automaticky přihlásit k Splan návštěvníkovi, pro který jste nastavili jednotné přihlašování.
* **Portál Microsoft moje aplikace**: Vyberte dlaždici **návštěvníka Splan** , která se automaticky přihlásí k Splan návštěvníkovi, pro který jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Splan návštěvníka se můžete [dozvědět, jak vynutili řízení relací v Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app). Ovládací prvky relace vám pomůžou chránit exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup.