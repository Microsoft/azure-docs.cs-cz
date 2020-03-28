---
title: 'Kurz: Integrace služby Azure Active Directory s prohlížečem InstaVR Viewer | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a prohlížečem InstaVR Viewer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de94f83c260a86f313a2dd04cdd5a7ae8fc1cda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73155309"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Kurz: Integrace služby Azure Active Directory s prohlížečem InstaVR Viewer

V tomto kurzu se dozvíte, jak integrovat InstaVR Viewer s Azure Active Directory (Azure AD).
Integrace prohlížeče InstaVR s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k InstaVR Viewer.
* Můžete povolit uživatelům, aby se automaticky přihlásili k InstaVR Viewer (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s InstaVR Viewer, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* InstaVR Viewer jednopřihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* InstaVR Viewer podporuje **sp** inicioval SSO
* InstaVR Viewer podporuje zřizování uživatelů **Just In Time**

## <a name="adding-instavr-viewer-from-the-gallery"></a>Přidání prohlížeče InstaVR z galerie

Chcete-li nakonfigurovat integraci prohlížeče InstaVR Viewer do služby Azure AD, je třeba přidat prohlížeč InstaVR z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat prohlížeč InstaVR z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **InstaVR Viewer**, z panelu výsledků vyberte **InstaVR Viewer** a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![Prohlížeč InstaVR v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí prohlížeče InstaVR viewer na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v prohlížeči InstaVR Viewer.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí prohlížeče InstaVR Viewer, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení prohlížeče InstaVR](#configure-instavr-viewer-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele prohlížeče InstaVR](#create-instavr-viewer-test-user)** Viewer – chcete-li mít protějšek Britta Simon v prohlížeči InstaVR Viewer, který je propojený s reprezentací azure ad uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí prohlížeče InstaVR Viewer, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **InstaVR Viewer** možnost Jedno **přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![InstaVR Viewer Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Neexistuje žádný pevný vzor pro přihlášení k adrese URL. Generuje se, když zákazník Prohlížeče InstaVR provádí webové balení. Je jedinečný pro každého zákazníka a balíček. Pro získání přesné přihlašovací adresy URL se musíte přihlásit do instance InstaVR Viewer a provést webové balení.

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Hodnota Identifikátor není skutečná. Aktualizujte tuto hodnotu skutečnou hodnotou Identifikátor, která je vysvětlena dále v tomto kurzu.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **stáhnout** **certifikát (Base64)** a **soubor metadat federace** z daných možností podle vašeho požadavku a uložit jej do počítače.

    ![Odkaz ke stažení certifikátu](common/metadata-certificatebase64.png)

6. V části **Nastavit prohlížeč InstaVR** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-instavr-viewer-single-sign-on"></a>Konfigurace jednotného přihlašování prohlížeče InstaVR

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti InstaVR Viewer jako správce.

2. Klikněte na **ikonu uživatele** a vyberte **účet**.

    ![Konfigurace prohlížeče InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Posuňte se dolů na **saml auth** a proveďte následující kroky:

    ![Konfigurace prohlížeče InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. V textovém poli **adresa URL služby SSO** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    b. Do textového pole **Adresa URL odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z portálu Azure.

    c. V textovém poli **ID entity** vložte hodnotu **Identifikátor azure ad,** kterou jste zkopírovali z portálu Azure.

    d. Chcete-li nahrát stažený soubor certifikátu, klepněte na tlačítko **Aktualizovat**.

    e. Chcete-li nahrát stažený soubor metadat federace, klepněte na tlačítko **Aktualizovat**.

    f. Zkopírujte hodnotu **ID entity** a vložte do textového pole **Identifikátor (ID entity)** v části **Základní konfigurace SAML** na webu Azure Portal.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k InstaVR Viewer.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **InstaVR Viewer**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **InstaVR Viewer**.

    ![Odkaz InstaVR Viewer v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-instavr-viewer-test-user"></a>Vytvořit testovacího uživatele prohlížeče InstaVR

V této části je v prohlížeči InstaVR vytvořen uživatel s názvem Britta Simon. InstaVR Viewer podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel v prohlížeči InstaVR viewer ještě neexistuje, vytvoří se po ověření nový uživatel. Pokud se potýkáte s problémy, obraťte se na [tým podpory InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti InstaVR Viewer jako správce.

2. Z levého navigačního panelu vyberte **Balíček** a vyberte **Vytvořit balíček pro web**.

    ![Konfigurace prohlížeče InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Vyberte **Download** (Stáhnout).

    ![Konfigurace prohlížeče InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Vyberte **Otevřít hostovanou stránku** poté, co bude přesměrována na Azure AD pro přihlášení.

    ![Konfigurace prohlížeče InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Zadejte svoje přihlašovací údaje Azure AD, abyste se mohli úspěšně přihlásit do Azure AD prostřednictvím služby SSO.

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
