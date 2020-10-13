---
title: 'Kurz: Azure Active Directory integraci se vnímání USA (ne UltiPro) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vnímání USA (ne UltiPro).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 7581dd52cb9affa3295bfbc00c14535ee72de5be
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995469"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Kurz: Azure Active Directory integrace se vnímání USA (ne UltiPro)

V tomto kurzu se dozvíte, jak integrovat USA vnímání (jiný než UltiPro) s Azure Active Directory (Azure AD).
Integrování příUSA příUltiProch) do Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup ke vnímání USA (ne UltiPro).
* Uživatelům můžete povolit, aby se automaticky přihlásili ke vnímání USA (bez UltiPro) (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s vnímání USA (ne UltiPro) potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jedním přihlašováním USA (bez UltiPro)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vnímání USA (non-UltiPro) podporuje **IDPy** iniciované jednotného přihlašování.

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Přidání vnímání USA (bez UltiPro) z Galerie

Pokud chcete nakonfigurovat integraci vnímání USA (non-UltiPro) do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat vnímání USA (non-UltiPro) z galerie.

**Pokud chcete přidat vnímání USA (ne UltiPro) z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **vnímání USA (UltiPro)**, vyberte **vnímání USA (ne UltiPro)** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![USA vnímání (jiný než UltiPro) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se vnímáním USA (ne UltiPro) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v vnímání USA (ne UltiPro).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD se vnímáním USA (ne UltiPro), musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurovat jednotné přihlašování USA vnímání (bez UltiPro)](#configure-perception-united-states-non-ultipro-single-sign-on)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si testovacího uživatele UltiPro USA vnímání](#create-perception-united-states-non-ultipro-test-user)** , abyste měli protějšek Britta Simon v vnímání USA (UltiPro), která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování služby Azure AD pomocí USA vnímání (ne UltiPro), proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce Příznaková integrace aplikace **USA (ne UltiPro)** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně a adresách USA (UltiPro)](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://perception.kanjoya.com/sp`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. Aplikace **vnímání USA (UltiPro)** vyžaduje, aby byla hodnota **identifikátoru Azure AD** jako <entity_id>, kterou získáte v části **nastavení příUltiProho vnímání USA (mimo)** , která bude kódována jako identifikátor URI. K získání hodnoty kódované identifikátorem URI použijte následující odkaz: **http://www.url-encode-decode.com/** .

    d. Po získání hodnoty zakódované identifikátorem URI se bude kombinovat s **adresou URL odpovědi** , jak je uvedeno níže.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Do textového pole **Adresa URL odpovědi** vložte výše uvedenou hodnotu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit vnímání USA (ne UltiPro)** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>USA konfigurace UltiPro (bez) jednoho Sign-On

1. V jiném okně prohlížeče se přihlaste k vašemu firemnímu USA (UltiPro) jako správce.

2. Na hlavním panelu nástrojů klikněte na **Nastavení účtu**.

    ![Snímek obrazovky, který zobrazuje "nastavení účtu" vybrané na hlavním panelu nástrojů.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na stránce **Nastavení účtu** proveďte následující kroky:

    ![Uživatel vnímání USA (ne UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Do textového pole **název společnosti** zadejte název **společnosti**.
    
    b. Do textového pole **název účtu** zadejte název **účtu**.

    c. Do textového pole **výchozí Reply-To e-mailu** zadejte platný **e-mail**.

    d. Jako **SAML 2,0**vyberte **poskytovatele identity SSO** .

4. Na stránce **Konfigurace jednotného přihlašování** proveďte následující kroky:

    ![USA vnímání (ne UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Jako **e-mail**vyberte **NameId typ SAML** .

    b. Do textového pole **název konfigurace jednotného přihlašování** zadejte název vaší **Konfigurace**.
    
    c. Do textového pole **název zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal. 

    d. Do **textového pole doména SAML**zadejte doménu, jako je @contoso.com .

    e. Znovu klikněte na **nahrát** a nahrajte soubor **XML s metadaty** .

    f. Klikněte na **Aktualizovat**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k vnímání USA (ne UltiPro).

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **USA vnímání (ne UltiPro)**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **vnímání USA (ne UltiPro)**.

    ![Odkaz na USA vnímání (jiný než UltiPro) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Vytvořit testovacího uživatele USA vnímání (bez UltiPro)

V této části vytvoříte uživatele s názvem Britta Simon v vnímání USA (ne UltiPro). Pomocí [týmu podpory vnímání USA (UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) můžete přidávat uživatele na UltiProovou platformu pro vnímání USA.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici USA vnímání (UltiPro), měli byste být automaticky přihlášeni ke vnímání USA (ne UltiPro), pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

