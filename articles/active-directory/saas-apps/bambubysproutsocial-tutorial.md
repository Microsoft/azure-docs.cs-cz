---
title: 'Kurz: Integrace služby Azure Active Directory s Bambu by Sprout Social | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Bambu by Sprout Social.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2b9ddbc-cab7-40d6-aca1-5b171cab4199
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68e20a2551fbd8e937330c565888c18f96642fc8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158937"
---
# <a name="tutorial-azure-active-directory-integration-with-bambu-by-sprout-social"></a>Kurz: Integrace služby Azure Active Directory s Bambu by Sprout Social

V tomto kurzu se dozvíte, jak integrovat Bambu pomocí Sprout Social s Azure Active Directory (Azure AD).
Integrace Bambu pomocí Sprout Social s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Bambu od Sprout Social.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Bambu pomocí Sprout Social (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Bambu by Sprout Social, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Bambu by Sprout Social single sign-on enabled subscription Bambu by Sprout Social single sign-on enabled subscription Bambu by Sprout Social single sign-on enabled subscription Bambu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Bambu od Sprout Sociální podporuje **IDP** zahájila SSO
* Bambu od Sprout Social podporuje **just in time** zřizování uživatelů

## <a name="adding-bambu-by-sprout-social-from-the-gallery"></a>Přidání Bambu od Sprout Social z galerie

Chcete-li nakonfigurovat integraci Bambu pomocí Sprout Social do Azure AD, musíte přidat Bambu pomocí Sprout Social z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Bambu od Sprout Social z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Bambu by Sprout Social**, vyberte **Bambu by Sprout Social** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Bambu by Sprout Social v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Bambu pomocí Sprout Social na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Bambu by Sprout Social.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Bambu by Sprout Social, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Bambu pomocí Sprout Social Single Sign-On](#configure-bambu-by-sprout-social-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte Bambu pomocí sprout sociální test uživatele](#create-bambu-by-sprout-social-test-user)** – mít protějšek Britta Simon v Bambu sprout social, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Bambu by Sprout Social, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Bambu by Sprout Social** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![Bambu od Sprout Sociální domény a URL jednotné přihlašovací informace](common/preintegrated.png)

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit Bambu by Sprout Social** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-bambu-by-sprout-social-single-sign-on"></a>Konfigurace Bambu podle Sprout Social Single Sign-On

Chcete-li nakonfigurovat jednotné přihlašování na **Straně Bambu pomocí Sprout Social,** musíte odeslat stažený **xml metadat federace** a příslušné zkopírované adresy URL z portálu Azure do [Bambu týmem podpory Sprout Social](mailto:support@getbambu.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Bambu sprout social.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Bambu by Sprout Social**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Bambu by Sprout Social**.

    ![Bambu by Sprout Sociální odkaz v seznamu aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-bambu-by-sprout-social-test-user"></a>Vytvořit Bambu pomocí Sprout Sociální test uživatele

V této části je uživatel s názvem Britta Simon vytvořen v Bambu sprout social. Bambu by Sprout Social podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v Bambu od Sprout Social, vytvoří se po ověření nový uživatel.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na bambu pomocí sprout sociální dlaždice na přístupovém panelu, měli byste být automaticky přihlášeni k Bambu pomocí Sprout sociální, pro které nastavíte přistupující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)