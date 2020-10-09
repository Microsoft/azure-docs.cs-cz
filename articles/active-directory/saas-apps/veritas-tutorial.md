---
title: 'Kurz: Azure Active Directory integrace se službou Veritas Enterprise trezor. jednotné přihlašování do cloudu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Veritas Enterprise trezor. jednotné přihlašování pro Cloud.
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
ms.openlocfilehash: cdb3f30813d2650737cfa43507cef2b1d456573d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88532496"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: Azure Active Directory integrace se službou Veritas Enterprise trezor. jednotné přihlašování ke cloudu

V tomto kurzu se dozvíte, jak integrovat Veritas Enterprise trezor. jednotné přihlašování do cloudu pomocí Azure Active Directory (Azure AD).
Integrací programu Veritas Enterprise trezor. cloudové jednotné přihlašování pomocí Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k webu Veritas Enterprise trezor. cloud SSO.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k programu Veritas Enterprise trezor. cloud SSO (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci služby Azure AD s využitím programu Veritas Enterprise trezor. cloud SSO, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Veritas Enterprise trezor. předplatné s povoleným jednotným přihlašováním jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Veritas Enterprise trezor. jednotné přihlašování pro Cloud podporuje **SP** iniciované jednotné přihlašování.

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidává se Veritas Enterprise trezor. cloud SSO z Galerie

Pokud chcete nakonfigurovat integraci aplikace Veritas Enterprise trezor. cloud SSO do Azure AD, musíte přidat Veritas Enterprise trezor. cloud SSO z Galerie do vašeho seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Veritas Enterprise trezor. cloud SSO z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Veritas Enterprise trezor. cloud SSO**, vyberte **Veritas Enterprise trezor. jednotné přihlašování cloudu** z panelu výsledků a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Veritas Enterprise trezor. jednotné přihlašování cloudu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu Veritas Enterprise trezor. jednotné přihlašování ke cloudu na základě testovacího uživatele s názvem **Britta Simon**.
V případě jednotného přihlašování funguje odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji Veritas Enterprise trezor. je třeba zřídit cloud SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Veritas Enterprise trezor. cloud SSO, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte si Veritas Enterprise trezor. jednotné přihlašování](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** pro jednotné přihlašování – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si Veritas Enterprise trezor. cloudový test jednotného přihlašování pro Cloud](#create-veritas-enterprise-vaultcloud-sso-test-user)** , který má protějšek Britta Simon v programu Veritas Enterprise trezor. cloudové jednotné přihlašování, které je propojené s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služby Veritas Enterprise trezor, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Veritas Enterprise trezor. Cloud** Application Integration Application SSO vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Veritas Enterprise trezor. cloudové přihlašovací údaje domény a adresy URL pro jednotné přihlašování](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. V poli **identifikátor** použijte adresu URL dle datového centra:

    | Datové centrum| URL |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. V textovém poli **Adresa URL odpovědi** použijte adresu URL v rámci datového centra:

    | Datové centrum| URL |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Obraťte se na [Společnost Veritas Enterprise trezor. tým podpory klienta jednotného přihlašování pro Cloud](https://www.veritas.com/support/.html) získá tuto hodnotu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Set the Veritas Enterprise trezor. cloud SSO** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Nakonfigurujte si Veritas Enterprise trezor. jednotné jednotné přihlašování pro Cloud Sign-On

Pokud chcete nakonfigurovat jednotné přihlašování na **webu Veritas Enterprise trezor** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [Veritas Enterprise trezoru. tým podpory jednotného přihlašování pro Cloud](https://www.veritas.com/support/.html). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k nástroji Veritas Enterprise trezor. cloud SSO.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **Veritas Enterprise trezor. jednotné přihlašování pro Cloud**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Veritas Enterprise trezor. jednotné přihlašování do cloudu**.

    ![Připojení k webu Veritas Enterprise trezor. cloud SSO v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvořte si Veritas Enterprise trezor. test jednotného přihlašování pro cloudový uživatel

V této části vytvoříte uživatele s názvem Britta Simon v rámci aplikace Veritas Enterprise trezor. cloud SSO. Pracujte s [Veritas Enterprise trezorem. tým podpory jednotného přihlašování pro Cloud](https://www.veritas.com/support/.html) umožňuje přidávat uživatele v rámci platformy Veritas Enterprise trezor. cloud SSO. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Veritas Enterprise trezor. cloud SSO na přístupovém panelu, měli byste být automaticky přihlášení do trezoru Veritas Enterprise. Cloud, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

