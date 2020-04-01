---
title: 'Kurz: Integrace služby Azure Active Directory se zoho one | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086229"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Kurz: Integrace Služby Azure Active Directory se Zoho One

V tomto kurzu se dozvíte, jak integrovat Zoho One s Azure Active Directory (Azure AD).
Integrace Zoho One s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zoho One.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Zoho One (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Zoho One, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Zoho Jedno předplatné s povoleným přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zoho One podporuje **SP** a **IDP** inicioval SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Přidání Zoho One z galerie

Chcete-li nakonfigurovat integraci Zoho One do Azure AD, musíte přidat Zoho One z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Zoho One z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zoho One**, vyberte **Zoho One** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Zoho One v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zoho One na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Zoho One.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zoho One, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace zoho jedno jednotné přihlášení](#configure-zoho-one-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte zoho jeden testovací uživatel](#create-zoho-one-test-user)** – mít protějšek Britta Simon v Zoho One, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Zoho One, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Zoho One** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Zoho One Doména a adresy URL jednotné přihlašovací informace](common/idp-relay.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`one.zoho.com`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Předchozí hodnota **adresy URL odpovědi** není skutečná. Získáte hodnotu `<saml-identifier>` z #step4 **konfigurace Zoho jedno přihlášení sekce** , která je vysvětlena dále v kurzu.

    c. Klepněte na **tlačítko Nastavit další adresy URL**.

    d. Do textového pole **Stav přenosu** zadejte adresu URL:`https://one.zoho.com`

5. Chcete-li aplikaci nakonfigurovat v režimu iniciovaném **službou SP,** proveďte následující krok:


    ![Zoho One Doména a adresy URL jednotné přihlašovací informace](common/both-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Předchozí hodnota **adresy URL přihlášení** není skutečná. Hodnotu aktualizujete skutečnou adresou URL pro přihlášení z oddílu **Configure Zoho One Single Sign-On,** který je vysvětlen dále v kurzu. 

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit Zoho One** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurace jednotného přihlašování Zoho

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Zoho One jako správce.

2. Na kartě **Organizace** klepněte na položku **Nastavení** v části **Ověřování SAML**.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na přeskupovací stránce proveďte následující kroky:

    ![Zoho Jeden sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Do textového pole **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.

    b. Do textového pole **Odhlásit adresu URL** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    c. Kliknutím na **Procházet** nahrajte **certifikát (Base64),** který jste stáhli z webu Azure Portal.

    d. Klikněte na **Uložit**.

4. Po uložení nastavení ověřování SAML zkopírujte hodnotu **identifikátoru SAML** a přidejte `https://accounts.zoho.com/samlresponse/one.zoho.com` ji pomocí adresy URL pro **odpověď** místo `<saml-identifier>`, jako se mi líbí, a vložte vygenerovanou hodnotu do textového pole Adresa URL **odpovědi** v části Základní **konfigurace SAML.**

    ![Zoho Jeden saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Přejděte na kartu **Domény** a klepněte na tlačítko **Přidat doménu**.

    ![Zoho One doména](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na stránce **Přidat doménu** proveďte následující kroky:

    ![Zoho Jeden přidat doménu](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Do textového pole **Název domény** zadejte doménu jako contoso.com.

    b. Klikněte na **Přidat**.

    >[!Note]
    >Po přidání domény ověřte doménu [následujícím](https://www.zoho.com/one/help/admin-guide/domain-verification.html) postupem. Po ověření domény použijte název domény v **přihlašovací adrese URL** v části Základní konfigurace **SAML** na webu Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu Zoho One.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Zoho One**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zoho One**.

    ![Odkaz Zoho One v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-zoho-one-test-user"></a>Vytvořit testovacího uživatele Zoho One

Chcete-li povolit uživatelům Azure AD k přihlášení k Zoho One, musí být zřízena do Zoho One. V Zoho One zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Zoho One jako správce zabezpečení.

2. Na kartě **Uživatelé** klikněte na **uživatelské logo**.

    ![Uživatel Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na stránce **Přidat uživatele** proveďte následující kroky:

    ![Zoho Jeden přidat uživatele](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Do pole **Název** textového pole zadejte jméno uživatele, jako je **Britta simon**.
    
    b. Do textového pole **E-mailová** brittasimon@contoso.comadresa zadejte e-mail uživatele jako .

    >[!Note]
    >Vyberte ověřenou doménu ze seznamu domén.

    c. Klikněte na **Přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Zoho One na přístupovém panelu, můžete by měl být automaticky přihlášeni k Zoho One, pro které nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

