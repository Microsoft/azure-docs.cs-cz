---
title: 'Kurz: Integrace Služby Azure Active Directory s pracovištěm autoúlohy | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Autotask Workplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97a68fd74449ea34438348cbc4c9d09c06d46d71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157805"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Kurz: Integrace Azure Active Directory s autotask workplace

V tomto kurzu se dozvíte, jak integrovat autotask workplace s Azure Active Directory (Azure AD).
Integrace autotask workplace s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k Autotask Workplace.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Autotask Workplace (Single Sign-On) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s autotask workplace, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Autotask Pracovní prostředí s povoleným předplatným
* Předplatné s automatickým přihlášením k workplace s jedním přihlášením
* Musíte být správce nebo supersprávce na Workplace.
* Musíte mít účet správce ve službě Azure AD.
* Uživatelé, kteří budou využívat tuto funkci, musí mít účty v rámci workplace a Azure AD a jejich e-mailové adresy pro obě musí odpovídat.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Autotask Workplace podporuje **sp a IDP** inicioval a so

## <a name="adding-autotask-workplace-from-the-gallery"></a>Přidání automatického úkolu na pracovišti z galerie

Chcete-li nakonfigurovat integraci autotask workplace do Azure AD, musíte přidat Autotask Workplace z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat autotask workplace z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Automatické úkoly Na pracovišti**, z panelu výsledků vyberte **Automatické úkolové pracoviště** a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Automatické úkoly Na pracovišti v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí funkce Autotask Workplace na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v aplikaci Autotask Workplace.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí funkce Autotask Workplace, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace automatického automatického přihlašování na workplace](#configure-autotask-workplace-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele autoúlohy na pracovišti](#create-autotask-workplace-test-user)** – chcete-li mít protějšek Britta Simon v autotask workplace, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí funkce Autotask Workplace, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Autotask Workplace** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Automatické zaúkolování domény a adres URL jednotného přihlášení](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Automatické zaúkolování domény a adres URL jednotného přihlášení](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta na pracovišti.](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit automatické úkoly na pracovišti** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-autotask-workplace-single-sign-on"></a>Konfigurace jednotného přihlašování na pracovišti s automatickým úkolem

1. V jiném okně webového prohlížeče se přihlaste k workplace online pomocí přihlašovacích údajů správce.

    > [!Note]
    > Při konfiguraci idp bude nutné zadat subdoménu. Chcete-li potvrdit správnou subdoménu, přihlaste se na Workplace Online. Po přihlášení si poznamenejte subdoménu v adrese URL. Subdoména je část mezi "https://" a ".awp.autotask.net/" a měla by být my, eu, ca, nebo au.

2. Přejděte na**Možnost Jednotné přihlašování ke** **konfiguraci** > a proveďte následující kroky:

    ![Automatická konfigurace jednotného přihlášení](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Vyberte možnost **Soubor metadat XML** a pak nahrajte stažený XML **federačních metadat** z webu Azure Portal.

    b. Klepněte na tlačítko **POVOLIT přisuzu .**

    ![Konfigurace schválení automatického přihlášení s jedním přihlášením](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Zaškrtněte políčko **I confirm this information is correct and I trust this IdP** check box.

    d. Klepněte na **tlačítko SCHVÁLIT**.

> [!Note]
> Pokud potřebujete pomoc s konfigurací automatického úkolu na pracovišti, podívejte se na [tuto stránku,](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) kde získáte pomoc s účtem na Workplace.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Autotask Workplace.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Autotask Workplace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Automatické úkolové pracoviště**.

    ![Odkaz Autotask Workplace v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-autotask-workplace-test-user"></a>Vytvořit testovacího uživatele na pracovišti s automatickým úkolem

V této části vytvoříte uživatele s názvem Britta Simon v autotask workplace. Spolupracujte s [týmem podpory pro autoúlohu na workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) a přidejte uživatele do platformy Autotask Workplace.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Autotask Workplace na přístupovém panelu, měli byste být automaticky přihlášeni k pracovní ploše automatické úlohy, pro kterou nastavíte přistupující přistupování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
