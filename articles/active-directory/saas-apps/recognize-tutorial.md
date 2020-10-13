---
title: 'Kurz: Azure Active Directory integrace s rozpoznáváním | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a rozpoznávat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 86c2c7383f3850b0c51831f94e673c3941894a08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548866"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Kurz: Azure Active Directory integrace s rozpoznáváním

V tomto kurzu se dozvíte, jak integrovat rozpoznávání s Azure Active Directory (Azure AD).
Integrace funkce rozpoznat pomocí Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k rozpoznávání.
* Můžete uživatelům povolit, aby se automaticky přihlásili k rozpoznávání (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s rozpoznáváním potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Rozpoznat předplatné s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Rozpoznávání podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-recognize-from-the-gallery"></a>Přidání rozpoznávání z Galerie

Pokud chcete nakonfigurovat integraci rozpoznávání do Azure AD, musíte přidat rozpoznávání z Galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat rozpoznávání z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **rozpoznávání**, vyberte **rozpoznat** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Rozpoznávání v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí rozpoznávání na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rozpoznávání.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s rozpoznáváním, je potřeba dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte rozpoznávání jednotného přihlašování](#configure-recognize-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření rozpoznávání testovacího uživatele](#create-recognize-test-user)** – Pokud chcete mít v rozpoznávání Britta Simon, které je propojené s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete konfigurovat jednotné přihlašování Azure AD pomocí rozpoznávání, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **rozpoznat** integraci aplikace vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    >[!NOTE]
    >**Soubor metadat poskytovatele služeb** získáte v části **Konfigurace rozpoznávat rozpoznávání jednotného přihlašování** v tomto kurzu.

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **identifikátoru** načte automaticky v základní části Konfigurace SAML.

    ![Rozpoznávání informací o jednotném přihlašování domény a adres URL](common/sp-identifier.png)

     Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Pokud hodnota **identifikátoru** není automaticky vyplněna, zobrazí se hodnota identifikátoru otevřením adresy URL metadat poskytovatele služby z oddílu nastavení jednotného přihlašování, která je vysvětlena dále v části **Konfigurace rozpoznávání rozpoznávat jednotné přihlašování** v tomto kurzu. Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pro získání hodnoty kontaktujte [tým podpory pro klienty](mailto:support@recognizeapp.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit rozlišení** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-recognize-single-sign-on"></a>Konfigurace rozpoznávání jednoduchého Sign-On

1. V jiném okně webového prohlížeče se přihlaste k vašemu rozpoznávacímu klientovi jako správce.

2. V pravém horním rohu klikněte na **nabídku**. Přejít na **Správce společnosti**.
   
    ![Konfigurovat jednu Sign-On na straně aplikace](./media/recognize-tutorial/tutorial_recognize_000.png)

3. V levém navigačním podokně klikněte na **Nastavení**.
   
    ![Konfigurovat jednu Sign-On na straně aplikace](./media/recognize-tutorial/tutorial_recognize_001.png)

4. V části **nastavení jednotného přihlašování** proveďte následující kroky.
   
    ![Konfigurovat jednu Sign-On na straně aplikace](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **Povolit jednotné přihlašování**vyberte **zapnuto**.

    b. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.
    
    c. Do textového pole **Adresa URL cíle jednotného přihlašování** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
    
    d. Do textového pole **Adresa URL cíle na slo** vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal. 
    
    e. Otevřete stažený soubor **certifikátu (Base64)** v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu** .
    
    f. Klikněte na tlačítko **Uložit nastavení** . 

5. Vedle části **nastavení jednotného přihlašování** zkopírujte adresu URL v části **Adresa URL metadat poskytovatele služby**.
   
    ![Konfigurovat jednu Sign-On na straně aplikace](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Otevřete **odkaz metadata URL** v rámci prázdného prohlížeče a Stáhněte si dokument metadat. Pak z tohoto souboru Zkopírujte hodnotu EntityDescriptor (entityID) a vložte ji do textového pole **identifikátoru** v **základní konfiguraci SAML** na Azure Portal.
    
    ![Konfigurovat jednu Sign-On na straně aplikace](./media/recognize-tutorial/tutorial_recognize_004.png)

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k rozpoznávání.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **rozpoznat**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **rozpoznat**.

    ![Odkaz rozpoznat v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-recognize-test-user"></a>Vytvořit rozpoznávání testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k rozpoznávání, musí se zřídit k rozpoznávání. V případě rozpoznávání je zřizování ručním úkolem.

Tato aplikace nepodporuje zřizování SCIM, ale má alternativní synchronizaci uživatele, která zřídí uživatele. 

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k vaší rozpoznávání webu společnosti jako správce.

2. V pravém horním rohu klikněte na **nabídku**. Přejít na **Správce společnosti**.

3. V levém navigačním podokně klikněte na **Nastavení**.

4. V části **synchronizace uživatelů** proveďte následující kroky.
   
    ![Nový uživatel](./media/recognize-tutorial/tutorial_recognize_005.png "Nový uživatel")
   
    a. Jako **zapnutá synchronizace**vyberte **zapnuto**.
   
    b. Jako **zvolit poskytovatele synchronizace**vyberte **Microsoft/Office 365**.
   
    c. Klikněte na **Spustit synchronizaci uživatelů**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici rozpoznávání na přístupovém panelu, měli byste být automaticky přihlášeni k rozpoznávání, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

