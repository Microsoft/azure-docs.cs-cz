---
title: 'Kurz: Integrace služby Azure Active Directory s HackerOne | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a HackerOne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: effd0593384190eb1a1cf261305dd61818c3d0b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120741"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Kurz: Integrace služby Azure Active Directory s HackersOne

V tomto kurzu se dozvíte, jak integrovat HackerOne s Azure Active Directory (Azure AD).
Integrace HackerOne s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k HackerOne.
* Uživatelům můžete povolit automatické přihlášení k hackerské službě HackerOne (jednotné přihlášení) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s HackerOne, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* HackerOne předplatné s povoleným přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* HackerOne podporuje **sp** inicioval sso
* HackerOne podporuje **just in time** zřizování uživatelů

## <a name="adding-hackerone-from-the-gallery"></a>Přidání HackerOne z galerie

Chcete-li nakonfigurovat integraci HackerOne do Azure AD, musíte přidat HackerOne z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat hackera one z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **HackerOne**, z panelu výsledků vyberte **HackerOne** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![HackerOne v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí hackerského systému na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v HackerOne.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí hackerone, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace hackerského jednotného přihlašování](#configure-hackerone-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele HackerOne](#create-hackerone-test-user)** – chcete-li mít protějšek Britta Simon v HackerOne, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování služby Azure AD pomocí hackerského připojení, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **HackerOne** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![HackerOne Doména a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Přihlásit se na adresu URL** zadejte následující:`https://hackerone.com/users/saml/sign_in?email=<configured domain>`

    b. Do textového pole **Identifikátor (ID entity)** zadejte následující:`hackerone.com`

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit hackerajeden** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-hackerone-single-sign-on"></a>Konfigurace jediného přihlášení hackeraOne

1. Přihlaste se ke svému tenantovi HackerOne jako správce.

2. V horní nabídce klikněte na **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_001.png)

3. Přejděte na **možnost Ověřování** a klepněte na tlačítko Přidat **nastavení SAML**.

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_003.png)

4. V dialogovém okně **Nastavení SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_004.png)

    a. Do textového pole **E-mailová doména** zadejte registrovanou doménu.

    b. V textových polích **adresy URL jednotného přihlášení** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    c. Otevřete stažený **soubor certifikátu** z portálu Azure do poznámkového bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát X509.**

    d. Klikněte na **Uložit**.

5. V dialogovém okně Nastavení ověřování proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_005.png)

    a. Klepněte na tlačítko **Spustit test**.

6. Po úspěšném dokončení testu a v poli **Stav** se zobrazí **stav Poslední test: úspěch**, vyberte tlačítko Ověření **požadavku,** které chcete odeslat hackeroviOne ke schválení.

    ![Odeslat hackeroviOne ke schválení](./media/hackerone-tutorial/tutorial-hackerone-006.png)

7. Poté, co HackerOne schválí nastavení, můžete vybrat tlačítko **Migrovat uživatele** vyžadovat ověření při přizpůsobování služeb při přivýběru pro všechny uživatele.

    ![Povolit SAML](./media/hackerone-tutorial/tutorial-hackerone-007.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k HackerOne.

1. Na webu Azure Portal vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **HackerOne**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **hackerone**.

    ![Odkaz HackerOne v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-hackerone-test-user"></a>Vytvořit testovacího uživatele HackerOne

V této části je uživatel s názvem Britta Simon vytvořen v HackerOne. HackerOne podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v HackerOne, nový je vytvořen po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici HackerOne na přístupovém panelu, můžete by měl být automaticky přihlášeni k HackerOne, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
