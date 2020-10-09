---
title: 'Kurz: Azure Active Directory integrace se správcem certifikátů Sectigo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sectigo správcem certifikátů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: d68e5335fff0341d8808e581061519977e1bb517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543272"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Kurz: Azure Active Directory integrace se správcem certifikátů Sectigo

V tomto kurzu se naučíte integrovat Sectigo Certificate Manageru s Azure Active Directory (Azure AD).

Integrace Sectigo Certificate Manageru s Azure AD přináší následující výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k Sectigo správce certifikátů.
* Uživatelé můžou být přihlášeni automaticky k Sectigo správce certifikátů pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí Správce certifikátů Sectigo potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Sectigo předplatné správce certifikátů s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujte správce certifikátů Sectigo s Azure AD.

Správce certifikátů Sectigo podporuje následující funkce:

* **Jednotné přihlašování inicializované v SP**
* **Jednotné přihlašování iniciované IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Přidat správce certifikátů Sectigo do Azure Portal

Pokud chcete integrovat správce certifikátů Sectigo s Azure AD, musíte přidat správce certifikátů Sectigo do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Sectigo Certificate Manager**. Ve výsledcích hledání vyberte **Správce certifikátů Sectigo**a pak vyberte **Přidat**.

    ![Správce certifikátů Sectigo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Sectigo správce certifikátů na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem ve Správci certifikátů Sectigo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo, musíte dokončit tyto stavební bloky:

| Úloha | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování správce certifikátů Sectigo](#configure-sectigo-certificate-manager-single-sign-on)** | Nakonfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Povolí službě Britta Simon používat jednotné přihlašování Azure AD. |
| **[Vytvoření testovacího uživatele správce certifikátů Sectigo](#create-a-sectigo-certificate-manager-test-user)** | Vytvoří protějšek Britta Simon ve Správci certifikátů Sectigo, který je propojený s reprezentacemi uživatele v Azure AD. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo ve Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)v podokně integrace aplikace **Správce certifikátů Sectigo** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML** nebo **SAML/WS-nakrmený** režim pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte **Upravit** (ikona tužky) a otevřete základní podokno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** nakonfigurujte *režim iniciované IDP*a proveďte následující kroky:

    1. Do pole **identifikátor** zadejte jednu z těchto adres URL:
       * https: \/ /CERT-Manager.com/Shibboleth
       * https: \/ /Hard.CERT-Manager.com/Shibboleth

    1. Do pole **Adresa URL odpovědi** zadejte jednu z těchto adres URL:
        * https: \/ /CERT-Manager.com/Shibboleth.SSO/Saml2/post
        * https: \/ /Hard.CERT-Manager.com/Shibboleth.SSO/Saml2/post

    1. Vyberte **nastavit další adresy URL**.

    1. Do pole **stav přenosu** zadejte jednu z těchto adres URL:
       * https: \/ /CERT-Manager.com/Customer/SSLSupport/IDP
       * https: \/ /Hard.CERT-Manager.com/Customer/SSLSupport/IDP

    ![Sectigo informace o jednotném přihlašování domén správce certifikátů a adres URL](common/idp-relay.png)

1.  Chcete-li nakonfigurovat aplikaci v *režimu iniciované v režimu SP*, proveďte následující kroky:

    * Do pole **přihlašovací adresa URL** zadejte jednu z těchto adres URL:
      * https: \/ /CERT-Manager.com/Shibboleth.SSO/Login
      * https: \/ /Hard.CERT-Manager.com/Shibboleth.SSO/Login

      ![Sectigo informace o jednotném přihlašování domén správce certifikátů a adres URL](common/both-signonurl.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** další **certifikát (Base64)**. Vyberte možnost stažení podle vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu (Base64)](common/certificatebase64.png)

1. V části **nastavit správce certifikátů Sectigo** zkopírujte na základě vašich požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Odhlašovací adresa URL

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurace jednotného přihlašování správce certifikátů Sectigo

Ke konfiguraci jednotného přihlašování na straně správce certifikátů Sectigo odešlete soubor stažený certifikát (Base64) a příslušné adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory správce certifikátů Sectigo](https://sectigo.com/support). Tým podpory správce certifikátů Sectigo používá informace, které odesíláte, aby bylo zajištěno, že připojení jednotného přihlašování pomocí protokolu SAML je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **brittasimon \@ \<your-company-domain> . \<extension\> **. Například **brittasimon \@ contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

    ![Podokno uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k Sectigo Správci certifikátů, aby mohli používat jednotné přihlašování Azure.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Sectigo Certificate Manager**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Sectigo správce certifikátů**.

    ![Správce certifikátů Sectigo v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. Pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** . Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Vytvoření testovacího uživatele správce certifikátů Sectigo

V této části vytvoříte uživatele s názvem Britta Simon ve Správci certifikátů Sectigo. Pokud chcete přidat uživatele na platformě Sectigo správce certifikátů, pracujte s [týmem podpory správce certifikátů Sectigo](https://sectigo.com/support) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Po nastavení jednotného přihlašování vyberete **Sectigo správce certifikátů** na portálu moje aplikace a automaticky jste přihlášeni k Sectigo správce certifikátů. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si tyto články:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


