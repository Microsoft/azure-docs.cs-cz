---
title: 'Kurz: Integrace služby Azure Active Directory s klávesovým světlem LockPath | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a klávesovým světlem LockPath.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148c2c46a911088d01ab83fe2d16e8ca81d272ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098777"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Kurz: Integrace služby Azure Active Directory s klávesovým světlem LockPath

V tomto kurzu se dozvíte, jak integrovat LockPath Keylight s Azure Active Directory (Azure AD).
Integrace lockpath keylight s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LockPath Keylight.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě LockPath Keylight (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí aplikace LockPath Keylight, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením LockPath Keylight

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LockPath Keylight podporuje **sp** inicioval asociaci
* LockPath Keylight podporuje zřizování uživatelů **just in time**

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Přidání klávesového světla LockPath z galerie

Chcete-li nakonfigurovat integraci lockpath keylight do Azure AD, musíte přidat LockPath Keylight z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat klávesovou zkratku LockPath z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Klávesové světlo LockPath**, z panelu výsledků vyberte **Zámek Klávesa LockPath** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

    ![LockPath Keylight v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí lockpath keylight na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v aplikaci LockPath Keylight.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí aplikace LockPath Keylight, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte klávesové světlo LockPath Single Sign-On](#configure-lockpath-keylight-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte lockpath keylight test uživatele](#create-lockpath-keylight-test-user)** – mít protějšek Britta Simon v LockPath Keylight, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování služby Azure AD pomocí technologie LockPath Keylight, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **LockPath Keylight** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![LockPath Keylight Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<company name>.keylightgrc.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<company name>.keylightgrc.com`

    c. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL pomocí následujícího vzoru:`https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL, identifikátorem a adresou URL pro odpověď. Obraťte se na [tým podpory keylight klienta LockPath](https://www.lockpath.com/contact/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Nezpracovaný)** z daných možností a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V části **Nastavit klávesovou zkratku LockPath** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-lockpath-keylight-single-sign-on"></a>Konfigurace jednotného přihlašování kláves LockPath

1. Chcete-li povolit přisazovací a toové vlastnosti v klávesovém světle LockPath Keylight, proveďte následující kroky:

    a. Přihlaste se k účtu LockPath Keylight jako správce.

    b. V horní nabídce klikněte na **Osoba**a vyberte **Položku Nastavení klíčových světel**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/401.png)

    c. V zobrazení stromu vlevo klepněte na tlačítko **SAML**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/402.png)

    d. V dialogovém okně **Nastavení SAML** klepněte na tlačítko **Upravit**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/404.png)

1. Na stránce Edit **SAML Settings** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/405.png)

    a. Nastavte **ověřování SAML** na **aktivní**.

    b. V textovém poli **Přihlašovací adresa URL zprostředkovatele identity** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    c. V textovém poli **URL odhlášení zprostředkovatele identity** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z portálu Azure.

    d. Kliknutím na **Vybrat soubor** vyberte stažený certifikát LockPath Keylight a kliknutím na **Otevřít** nahrajte certifikát.

    e. Nastavte **umístění Id uživatele SAML** na **Element NameIdentifier příkazu subjektu**.

    f. Poskytněte **zprostředkovateli služeb Keylight pomocí** následujícího vzoru: `https://<CompanyName>.keylightgrc.com`.

    g. Nastavte **automatické zřizování uživatelů** na **aktivní**.

    h. Nastavte **typ účtu automatického zřizování** na **úplný uživatel**.

    i. Nastavte **roli zabezpečení automatického zřízení**, vyberte možnost Standardní uživatel se **službou SAML**.

    j. Nastavte **konfiguraci zabezpečení automatického zřízení**, vyberte **možnost Standardní konfigurace uživatele**.

    k. Do textového pole Atribut `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **E-mail** zadejte .

    l. Do textového pole Atribut `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`první **název** zadejte .

    m. Do textového pole **Atribut** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`příjmení zadejte .

    n. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k LockPath Keylight.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **LockPath Keylight**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku LockPath Keylight**.

    ![Odkaz LockPath Keylight v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-lockpath-keylight-test-user"></a>Vytvořit uživatele testu kontroly kláves LockPath

V této části je vytvořen uživatel s názvem Britta Simon v LockPath Keylight. LockPath Keylight podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v lockpath keylight, nový je vytvořen po ověření. Pokud potřebujete vytvořit uživatele ručně, je třeba kontaktovat [tým podpory klienta LockPath Keylight](https://www.lockpath.com/contact/)Client .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici LockPath Keylight na přístupovém panelu, měli byste být automaticky přihlášeni k LockPath Keylight, pro které nastavíte přiřazovací ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)