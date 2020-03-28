---
title: 'Kurz: Integrace služby Azure Active Directory se společností Cisco Umbrella | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a službou Cisco Umbrella.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee91ca53e32bfdc387dc20054493d02d506a75da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158638"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Kurz: Integrace Azure Active Directory se společností Cisco Umbrella

V tomto kurzu se dozvíte, jak integrovat Cisco Umbrella s Azure Active Directory (Azure AD).
Integrace Cisco Umbrella s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Cisco Umbrella.
* Uživatelům můžete povolit automatické přihlášení k cisco umbrella (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Cisco Umbrella, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Cisco Umbrella

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cisco Umbrella podporuje **SP a IDP** inicioval SSO

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Přidání Cisco Umbrella z galerie

Chcete-li nakonfigurovat integraci Cisco Umbrella do Azure AD, musíte přidat Cisco Umbrella z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li z galerie přidat cisco umbrella, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco Umbrella**, z panelu výsledků vyberte **Cisco Umbrella** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Cisco Umbrella v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s [Název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v [Název aplikace].

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [Název aplikace], musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení Cisco Umbrella](#configure-cisco-umbrella-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte cisco umbrella testovací ho uživatele](#create-cisco-umbrella-test-user)** – mít protějšek Britta Simon v Cisco Umbrella, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlášení služby Azure AD pomocí [Název aplikace], proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Cisco Umbrella** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![Cisco Umbrella Domény a adresy URL jednotné přihlašovací informace](common/both-preintegrated-signon.png)

    a. Chcete-li aplikaci nakonfigurovat v režimu intiated **SP,** proveďte následující kroky:

    b. Klepněte na **tlačítko Nastavit další adresy URL**.

    c. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://login.umbrella.com/sso`

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte xml **metadat** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit Cisco Umbrella** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-cisco-umbrella-single-sign-on"></a>Konfigurace jednotného přihlášení cisco umbrella

1. V jiném okně prohlížeče se přihlaste k webu společnosti Cisco Umbrella jako správce.

2. V levé části nabídky klikněte na **Správce** a přejděte na **Ověřování** a potom klikněte na **SAML**.

    ![Správce](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Zvolte **Jiné** a klikněte na **NEXT**.

    ![Ostatní](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Na **stránce Metadata deštníku Cisco**klepněte na tlačítko **DALŠÍ**.

    ![Metadata](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Pokud jste na kartě **Nahrát metadata** měli předkonfigurovaný saml, vyberte **možnost Kliknutím sem a** postupujte podle následujících kroků.

    ![Další](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. V **možnosti A: Nahrajte soubor XML**nahrajte soubor **XML metadat federace,** který jste stáhli z portálu Azure, a po nahrání metadat se níže uvedené hodnoty automaticky vyplní a klikněte na **tlačítko NEXT**.

    ![Soubor choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. V části **Ověřit konfiguraci saml** klikněte na **test konfigurace saml**.

    ![The Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Klepněte na tlačítko **ULOŽIT**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Cisco Umbrella.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Cisco **Umbrella**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. Do seznamu aplikací zadejte a vyberte **možnost Cisco Umbrella**.

    ![Odkaz Cisco Umbrella v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-cisco-umbrella-test-user"></a>Vytvořit Cisco Umbrella testovací ho uživatele

Chcete-li povolit uživatelům Azure AD přihlásit se k Cisco Umbrella, musí být zřízena do Cisco Umbrella.  
V případě Cisco Umbrella zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. V jiném okně prohlížeče se přihlaste k webu společnosti Cisco Umbrella jako správce.

2. V levé části nabídky klikněte na **Správce** a přejděte na **Účty**.

    ![Účet](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na stránce **Účty** klikněte na **Přidat** v pravé horní části stránky a proveďte následující kroky.

    ![Uživatel](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Do pole **Křestní jméno** zadejte křestní jméno jako **Britta**.

    b. Do pole **Příjmení** zadejte příjmení jako **simon**.

    c. V části **Zvolit roli delegovaného správce**vyberte svou roli.
  
    d. Do pole **E-mailová adresa** zadejte e-mailovou adresu uživatele, jako **je\@brittasimon contoso.com**.

    e. Do pole **Heslo** zadejte heslo.

    f. Do pole **Potvrdit heslo** znovu zadejte heslo.

    g. Klepněte na tlačítko **VYTVOŘIT**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Cisco Umbrella na přístupovém panelu, můžete by měl být automaticky přihlášeni k Cisco Umbrella, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
