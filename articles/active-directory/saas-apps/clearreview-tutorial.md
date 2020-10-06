---
title: 'Kurz: Azure Active Directory integrace s jasným přezkoumáním | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vymazat kontrolu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 21899ab40836b8ad083c0f005d43084c65c1b6fa
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759885"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Kurz: Azure Active Directory integrace s jasným přezkoumáním

V tomto kurzu se dozvíte, jak integrovat jasné revize pomocí Azure Active Directory (Azure AD).
Integrace jasné recenze s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k jasné kontrole.
* Můžete uživatelům povolit, aby se automaticky přihlásili, a vymazat si kontrolu (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s jasným přezkoumáním potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Vymazat předplatné s povolenou kontrolou jednotného přihlašování

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vymazání revize podporuje **SP a IDP** iniciované jednotné přihlašování.

## <a name="adding-clear-review-from-the-gallery"></a>Přidání jasné recenze z Galerie

Pokud chcete nakonfigurovat integraci jasné revize do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie vymazat kontrolu.

**Chcete-li přidat k vymazání recenze z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **clear recenze**, vyberte možnost **Vymazat recenzi** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Vymazat kontrolu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s jasným přezkoumáním na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba vytvořit odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v jasné kontrole.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD s jasným přezkoumáním, je potřeba dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte možnost Vymazat revize jednotného přihlašování](#configure-clear-review-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit jasný kontrolní uživatel pro testování](#create-clear-review-test-user)** – Pokud chcete mít protějšek Britta Simon v jasné revizi, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s jasným přezkoumáním, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Zrušit kontrolu** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<customer name>.clearreview.com/sso/metadata/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<customer name>.clearreview.com/sso/acs/`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<customer name>.clearreview.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory pro kontrolu zrušení](https://clearreview.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Zaškrtnutí políčka vymazat aplikaci očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Zaškrtnutí políčka vymazat aplikaci očekává, že **NameIdentifier** budou mapovány pomocí **User. mail**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

7. V dialogovém okně **atributy uživatele & deklarací** proveďte následující kroky:

    a. Klikněte na **ikonu Upravit** napravo od **hodnoty identifikátoru názvu**.

    ![Snímek obrazovky zobrazuje atributy uživatele & deklarací identity s vybranou ikonou pro úpravy.](./media/clearreview-tutorial/attribute02.png)

    ![Snímek obrazovky se zobrazí dialogové okno Spravovat deklarace identity uživatelů, kde můžete zadat hodnoty, které jsou popsány.](./media/clearreview-tutorial/attribute01.png)

    b. V seznamu **zdrojový atribut** vyberte pro tento řádek hodnotu atributu **User. mail** .

    c. Klikněte na **Uložit**.

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavení vymazat revizi** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-clear-review-single-sign-on"></a>Konfigurace jasné revize jednotného přihlašování

1. Chcete-li konfigurovat jednotné přihlašování při **zrušení kontroly** , otevřete portál **Vymazat kontrolu** s přihlašovacími údaji správce.

2. V levém navigačním panelu vyberte **správce** .

    ![Snímek obrazovky se zobrazí jako vybraný portál pro kontrolu a správu.](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. V části **integrace** v dolní části stránky klikněte na tlačítko **změnit** napravo od **nastavení jednotného přihlašování**.

    ![Snímek obrazovky se zobrazeným tlačítkem pro změnu jednotného přihlašování.](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Na stránce **nastavení jednotného přihlašování** proveďte následující kroky.

    ![Snímek obrazovky se zobrazí stránka nastavení jednotného přihlašování, kde můžete zadat informace v tomto kroku.](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    b. Do textového pole **koncového bodu SAML** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.  

    c. Do textového pole **koncového bodu slo** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.  

    d. Otevřete stažený certifikát v programu Poznámkový blok a vložte obsah do textového pole **certifikát X. 509** .   

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k jasné kontrole.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Vymazat kontrolu**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Vymazat kontrolu**.

    ![Odkaz Vymazat kontrolu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-clear-review-test-user"></a>Vytvořit jasný kontrolní uživatel pro testování

V této části vytvoříte uživatele s názvem Britta Simon v části vymazat kontrolu. Pokud chcete přidat uživatele na jasné revizi, obraťte se prosím na [tým podpory s jasným přezkoumáním](https://clearreview.com/contact/) .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici vymazat kontrolu na přístupovém panelu byste měli být automaticky přihlášeni k jasné kontrole, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

