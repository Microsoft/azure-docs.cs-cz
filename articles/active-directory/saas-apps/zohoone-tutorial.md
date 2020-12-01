---
title: 'Kurz: Azure Active Directory integrace s Zohoem 1 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho jednu.
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
ms.openlocfilehash: 98c9012969051e4370239a0f4098e0ea1d7a55a0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348309"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Kurz: Azure Active Directory integrace s Zoho 1

V tomto kurzu se naučíte, jak integrovat Zoho s Azure Active Directory (Azure AD).
Integrace Zoho s jednou pomocí Azure AD vám poskytne následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Zoho.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Zoho jednomu (jednotnému přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s Zoho, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Zoho jediné předplatné s povoleným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zoho One podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.

## <a name="adding-zoho-one-from-the-gallery"></a>Přidání Zoho z Galerie

Pokud chcete nakonfigurovat integraci Zoho do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Zoho jednu z galerie.

**Pokud chcete přidat Zoho z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zoho One**, vyberte **Zoho jeden** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zoho jednu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zoho jednoho na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zoho.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zoho, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte Zoho jedno jednotné přihlašování](#configure-zoho-one-single-sign-on)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Zoho jednoho testovacího uživatele](#create-zoho-one-test-user)** – abyste měli protějšek Britta Simon v Zoho, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Zoho, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **jedné aplikace Zoho** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-relay.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `one.zoho.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Hodnota **adresy URL předchozí odpovědi** není reálné číslo. `<saml-identifier>`Hodnota z #step4 **nakonfigurovat jednu část jednotného přihlašování Zoho** , která se vysvětluje později v tomto kurzu.

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL: `https://one.zoho.com`

5. Pokud chcete nakonfigurovat aplikaci v režimu **SP** iniciované, proveďte následující krok:

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/both-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Hodnota předchozí **přihlašovací adresy URL** není skutečná. Tuto hodnotu aktualizujete skutečnou adresou URL Sign-On v části **Konfigurace jednotného přihlašování Zoho** , která je vysvětlena dále v tomto kurzu. 

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení Zoho 1** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurace Zoho jednoho Sign-On

1. V jiném okně webového prohlížeče se přihlaste k Zoho jedné firemní síti jako správce.

2. Na kartě **organizace** klikněte v části **ověřování SAML** na **Nastavení** .

    ![Zoho jednu organizační organizaci](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na místní stránce proveďte následující kroky:

    ![Zoho jeden podpis](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL** pro odhlášení vložte hodnotu URL pro **odhlášení**, kterou jste zkopírovali z Azure Portal.

    c. Klikněte na tlačítko **Procházet** a nahrajte **certifikát (Base64)** , který jste stáhli z Azure Portal.

    d. Klikněte na **Uložit**.

4. Po uložení nastavení ověřování SAML Zkopírujte hodnotu **identifikátoru SAML** a místo toho ji přidejte s **adresou URL odpovědi** `<saml-identifier>` , třeba `https://accounts.zoho.com/samlresponse/one.zoho.com` a vložte vygenerovanou hodnotu do pole **Adresa URL odpovědi** v části **základní konfigurační oddíl SAML** .

    ![Zoho jednu SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Přejděte na kartu **domény** a potom klikněte na **Přidat doménu**.

    ![Zoho jednu doménu](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na stránce **Přidat doménu** proveďte následující kroky:

    ![Zoho jednu doménu přidat](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Do textového pole **název domény** zadejte Domain, jako je contoso.com.

    b. Klikněte na **Přidat**.

    >[!Note]
    >Po přidání domény postupujte podle [těchto](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroků a ověřte svoji doménu. Po ověření domény použijte název domény v **přihlašovací adrese URL** v **základní části konfigurace SAML** v Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zoho One.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte Zoho ( **jedna**).

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zoho jednu**.

    ![Zoho jeden odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-zoho-one-test-user"></a>Vytvořit Zoho jednoho testovacího uživatele

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k Zohou, musí se zřídit do Zoho One. V jednom Zoho je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Zoho jako správce zabezpečení.

2. Na kartě **Uživatelé** klikněte na **logo uživatele**.

    ![Zoho jednoho uživatele](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na stránce **Přidat uživatele** proveďte následující kroky:

    ![Zoho jednoho přidat uživatele](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Do textového pole **název** zadejte jméno uživatele jako **Britta Simon**.
    
    b. Do textového pole **e-mailová adresa** zadejte e-maily uživatele jako brittasimon@contoso.com .

    >[!Note]
    >Vyberte ověřenou doménu ze seznamu domén.

    c. Klikněte na **Přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na jednu dlaždici Zoho, měli byste se automaticky přihlásit k Zoho, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)