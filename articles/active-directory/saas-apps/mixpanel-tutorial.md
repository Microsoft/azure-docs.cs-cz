---
title: 'Kurz: Integrace služby Azure Active Directory s mixpanelem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a panelem Mix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: 58074d02dfc437a1804784e73fa4e65086b53b9e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160470"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Kurz: Integrace služby Azure Active Directory s panelem Mixpanel

V tomto kurzu se dozvíte, jak integrovat Mixpanel s Azure Active Directory (Azure AD).
Integrace Mixpanelu s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Mixpanel.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke Mixpanelu (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Mixpanelu, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením mixpanelu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mixpanel podporuje **SP** inicioval SSO

## <a name="adding-mixpanel-from-the-gallery"></a>Přidání Mixpanel z galerie

Chcete-li nakonfigurovat integraci Mixpanelu do Azure AD, musíte přidat Mixpanel z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat mixpanel z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mixpanel**, z panelu výsledků vyberte **Mixpanel** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Mixpanel v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí mixpanelu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Mixpanelu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Mixpanelu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace mixpanelu jednotného přihlášení](#configure-mixpanel-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Mixpanelu](#create-mixpanel-test-user)** – chcete-li mít protějšek Britta Simon v Mixpanelu, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí mixpanelu, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Mixpanel** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení do domény mixpanelu a adresy URL](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://mixpanel.com/login/`

    > [!NOTE]
    > Chcete-li [https://mixpanel.com/register/](https://mixpanel.com/register/) nastavit přihlašovací údaje, zaregistrujte se a obraťte se na [tým podpory Mixpanel,](mailto:support@mixpanel.com) abyste povolili nastavení služby SSO pro vašeho tenanta. V případě potřeby můžete také získat hodnotu adresy URL pro přihlášení od týmu podpory Mixpanel. 

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Mixpanel** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-mixpanel-single-sign-on"></a>Konfigurace jednotného přihlašování mixpanelu

1. V jiném okně prohlížeče se přihlaste k aplikaci Mixpanel jako správce.

2. V dolní části stránky klikněte na malou ikonu **ozubeného kola** v levém rohu. 
   
    ![Mixpanel jednotné přihlášení](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Klikněte na kartu **Zabezpečení Access** a potom klikněte na **Změnit nastavení**.
   
    ![Nastavení panelu Mix](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Na stránce Dialogové okno **Změnit certifikát** klikněte na **Vybrat soubor,** chcete-li nahrát stažený certifikát, a potom klikněte na **tlačítko DALŠÍ**.
   
    ![Nastavení panelu Mix](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Do textového pole URL ověřování na stránce **dialogového** okna Změnit ověřovací adresu URL vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal, a klikněte na **tlačítko DALŠÍ**.
   
    ![Nastavení panelu Mix](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Klikněte na **Done** (Hotovo).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Mixpanel.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Mixpanel**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Položku Mixpanel**.

    ![Odkaz Mixpanel v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-mixpanel-test-user"></a>Vytvořit testovacího uživatele Mixpanelu

Cílem této části je vytvořit uživatele s názvem Britta Simon v Mixpanel. 

1. Přihlaste se na firemní web Mixpanel jako správce.

2. V dolní části stránky kliknutím na malé tlačítko ozubeného kola v levém rohu otevřete okno **Nastavení.**

3. Klikněte na kartu **Tým.**

4. Do textového pole **člena týmu** zadejte e-mailovou adresu Britty do Azure.
   
    ![Nastavení panelu Mix](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Klepněte na **tlačítko Pozvat**. 

> [!Note]
> Uživatel obdrží e-mail pro nastavení profilu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Mixpanel na přístupovém panelu, měli byste být automaticky přihlášeni k Mixpanel, pro který nastavíte přispojené k zámesu. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

