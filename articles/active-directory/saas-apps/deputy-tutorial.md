---
title: 'Kurz: Azure Active Directory integrace s nástrojem náměstek | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a náměstkem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: 3061a4f0b6a41e5057436e15cabd2db0cbec5c00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454877"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Kurz: Azure Active Directory integrace s nástrojem náměstek

V tomto kurzu se naučíte integrovat náměstek s Azure Active Directory (Azure AD).
Integrování zástupce s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k náměstku.
* Uživatelům můžete povolit, aby se automaticky přihlásili k programu náměstek (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s nástrojem náměstek budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Náměstek podporuje **SP** a **IDP** iniciované jednotné přihlašování.

## <a name="adding-deputy-from-the-gallery"></a>Přidání zástupce z Galerie

Pokud chcete nakonfigurovat integraci programu náměstek do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zástupce z galerie.

**Chcete-li přidat zástupce z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **náměstek**, vyberte **náměstek** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zástupce v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí zástupce na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji náměstek.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zástupce, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování](#configure-deputy-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte zástupce testovacího uživatele](#create-deputy-test-user)** – Pokud chcete mít protějšek Britta Simon v náměstku, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí nástroje náměstek, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **náměstek** Application Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky s oddílem konfigurace "základní S A M L" s zvýrazněným tlačítkem "identifikátor", "adresa URL odpovědi" a "Uložit".](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Zástupce domény a adresy URL jednotného přihlašování](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Přípona zástupce v oblasti je volitelná, nebo by měla používat jednu z těchto možností: au | na | EU | as | La | La | a | prostředník-au | nenáročné na | vše – La | pro \ AF | s jedním z

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienty](https://www.deputy.com/call-centers-customer-support-scheduling-software) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavit zástupce** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-deputy-single-sign-on"></a>Nakonfigurovat jeden Sign-On zástupce

1. Přejděte na následující adresu URL: `https://(your-subdomain).deputy.com/exec/config/system_config` . Přejděte na **nastavení zabezpečení** a klikněte na **Upravit**.
   
    ![Snímek obrazovky se stránkou "systémová konfigurace" se zaškrtnutým tlačítkem "nastavení zabezpečení – Upravit".](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Na stránce **nastavení zabezpečení** proveďte následující kroky.

    ![Konfigurace jednoho Sign-On](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Povolte **sociální přihlášení**.
   
    b. Otevřete certifikát kódovaný v kódování Base64 stažený z Azure Portal v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu OpenSSL** .
   
    c. Do textového pole URL jednotného přihlašování SAML zadejte `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. V textovém poli adresy URL jednotného přihlašování SAML nahraďte `<your subdomain>` subdoménou.
   
    e. V textovém poli adresy URL jednotného přihlašování SAML nahraďte `<saml sso url>` **přihlašovací adresou URL** , kterou jste zkopírovali z Azure Portal.
   
    f. Klikněte na **Uložit nastavení**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k náměstku.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **náměstek**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **zástupce**.

    ![Odkaz na zástupce v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-deputy-test-user"></a>Vytvořit náměstka testovacího uživatele

Aby se uživatelé Azure AD mohli přihlásit k náměstku, musí se zřídit jako náměstek. V případě náměstka je zřizování ručním úkolem.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se ke svému náměstkovi firemnímu webu jako správce.

2. V horním navigačním podokně klikněte na **Uživatelé**.
   
    ![People](./media/deputy-tutorial/tutorial_deputy_001.png "People")

3. Klikněte na tlačítko **Přidat lidi** a pak klikněte na **přidat jednu osobu**.
   
    ![Přidat lidi](./media/deputy-tutorial/tutorial_deputy_002.png "Přidat lidi")

4. Proveďte následující kroky a klikněte na **uložit & pozvat**.
   
    ![Nový uživatel](./media/deputy-tutorial/tutorial_deputy_003.png "Nový uživatel")

    a. Do textového pole **název** zadejte název uživatele, jako je **BrittaSimon**.
   
    b. Do textového pole **e-mail** zadejte e-mailovou adresu účtu Azure AD, který chcete zřídit.
   
    c. Do pole **práce v** textovém poli zadejte název firmy.
   
    d. Klikněte na tlačítko **uložit & tlačítko pozvat** .

5. Držitel účtu Azure AD obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje. K zajištění uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API od zástupce, které poskytuje náměstek.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici náměstek na přístupovém panelu byste měli být automaticky přihlášeni k náměstku, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)