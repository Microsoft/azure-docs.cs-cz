---
title: 'Kurz: Azure Active Directory integrace s Uberflip | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Uberflip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 8936c2150c32a22877f9b56736ecc4b8f3424b32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88533125"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Kurz: Azure Active Directory integrace s Uberflip

V tomto kurzu se dozvíte, jak integrovat Uberflip s Azure Active Directory (Azure AD).

Integrace Uberflip s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Uberflip.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Uberflip (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Podrobnosti o integraci aplikací SaaS (software jako služba) se službou Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Uberflip potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Uberflip s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Uberflip podporuje následující funkce:

* SP-iniciované a IDP jednotné přihlašování (SSO).
* Zřizování uživatelů za běhu.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Přidat Uberflip z Azure Marketplace

Pokud chcete nakonfigurovat integraci Uberflip do služby Azure AD, musíte přidat Uberflip z Azure Marketplace do seznamu spravovaných aplikací SaaS:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte **Azure Active Directory**.

   ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

   ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, v horní části podokna vyberte **+ Nová aplikace** .

   ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Uberflip**. Ve výsledcích hledání vyberte **Uberflip**a pak vyberte **Přidat** , aby se aplikace přidala.

   ![Uberflip v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Uberflip na základě testovacího uživatele s názvem **B Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Uberflip.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Uberflip, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , aby mohli vaši uživatelé používat tuto funkci.
1. **[Nakonfigurujte jednotné přihlašování Uberflip](#configure-uberflip-single-sign-on)** ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Vytvořte testovacího uživatele Uberflip](#create-an-uberflip-test-user)** , aby byl uživatel s názvem b. Simon v Uberflip, který je propojený s uživatelem služby Azure AD s názvem b. Simon.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Uberflip, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Uberflip** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte **Upravit** (ikona tužky) a otevřete základní podokno **Konfigurace SAML** .

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** proveďte jeden z následujících kroků v závislosti na tom, který režim jednotného přihlašování chcete nakonfigurovat:

   * Pokud chcete nakonfigurovat aplikaci v režimu jednotného přihlašování IDP, zadejte do pole **Adresa URL odpovědi (adresa URL služby potvrzení)** adresu URL pomocí následujícího vzoru:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informace o jednotném přihlašování v doméně Uberflip a adresách URL](common/both-replyurl.png)

     > [!NOTE]
     > Tato hodnota není skutečná. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat skutečnou hodnotu, obraťte se na [tým podpory Uberflip](mailto:support@uberflip.com). Můžete také odkazovat na vzory zobrazené v podokně **základní konfigurace SAML** v Azure Portal.

   * Chcete-li nakonfigurovat aplikaci v režimu jednotného přihlašování iniciované SP, vyberte možnost **nastavit další adresy URL**a v poli **Adresa URL pro přihlášení** zadejte tuto adresu URL:

     `https://app.uberflip.com/users/login`

     ![Informace o jednotném přihlašování v doméně Uberflip a adresách URL](common/both-signonurl.png)

1. V podokně **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a ze daných možností stáhněte **XML federačních metadat** a uložte ho do svého počítače.

   ![Možnost stažení XML federačních metadat](common/metadataxml.png)

1. V podokně **nastavit Uberflip** zkopírujte adresy URL nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor Azure AD**
   * **Odhlašovací adresa URL**

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurace jednotného přihlašování Uberflip

Ke konfiguraci jednotného přihlašování na straně Uberflip je potřeba odeslat stažený soubor XML federačních metadat a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Uberflip](mailto:support@uberflip.com). Tým Uberflip zajistí, že připojení SAML SSO je na obou stranách správně nastavené.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. V horní části obrazovky vyberte **+ Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    ![Podokno uživatele](common/user-properties.png)

    1. Do pole **název** zadejte **BSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BSimon \@ \<yourcompanydomain> . \<extension> **. Například **BSimon \@ contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Uberflip.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Uberflip**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Uberflip**.

    ![Uberflip v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **Spravovat**vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** v seznamu **Uživatelé** vyberte **B Simon** a v dolní části podokna zvolte **Vybrat** .

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. V dolní části podokna zvolte **možnost vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-uberflip-test-user"></a>Vytvořit testovacího uživatele v Uberflip

V Uberflip se teď vytvoří uživatel s názvem B. Simon. K vytvoření tohoto uživatele nemusíte nic dělat. Uberflip podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. Pokud uživatel s názvem B. Simon ještě v Uberflip neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když vyberete **Uberflip** na portálu moje aplikace, měli byste být automaticky přihlášeni k předplatnému Uberflip, pro které jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
