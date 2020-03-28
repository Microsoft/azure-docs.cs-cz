---
title: 'Kurz: Integrace Služby Azure Active Directory se službou HappyFox | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HappyFox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e5e43bd32e7e0ddbe0a7b5f63d74a1fd46f68d9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159126"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Kurz: Integrace Azure Active Directory s HappyFox

V tomto kurzu se dozvíte, jak integrovat HappyFox s Azure Active Directory (Azure AD).
Integrace HappyFoxu s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k HappyFox.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě HappyFox (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s HappyFox, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* HappyFox jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* HappyFox podporuje **SP** inicioval SSO


* HappyFox podporuje **just in time** zřizování uživatelů


## <a name="adding-happyfox-from-the-gallery"></a>Přidání HappyFox z galerie

Chcete-li nakonfigurovat integraci HappyFox do Azure AD, musíte přidat HappyFox z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat HappyFox z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **HappyFox**, z panelu výsledků vyberte **Možnost HappyFox** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![HappyFox v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby HappyFox na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve Službě HappyFox.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby HappyFox, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Single Sign-On HappyFox](#configure-happyfox-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele HappyFox](#create-happyfox-test-user)** – chcete-li mít protějšek Britta Simon v HappyFox, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby HappyFox, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **HappyFox** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![HappyFox Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.happyfox.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Kontaktujte [tým podpory happyfox klienta,](https://support.happyfox.com/home) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V sekci **Nastavit HappyFox** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-happyfox-single-sign-on"></a>Konfigurace single sign-on HappyFox

1. V jiném okně webového prohlížeče se přihlaste ke svému klientovi HappyFox jako správce.

2. Přejděte na **Spravovat**, klikněte na kartu **Integrace.**

    ![Konfigurace jednotného přihlašování](./media/happyfox-tutorial/header.png) 

3. Na kartě Integrace **klikněte** v části **Integrace SAML** na otevření nastavení jednotného přihlášení.

    ![Konfigurace jednotného přihlašování](./media/happyfox-tutorial/configure.png)

4. Uvnitř oddílu konfigurace SAML vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure do textového pole **cílová adresa URL při přihlašovacím programu.**

    ![Konfigurace jednotného přihlašování](./media/happyfox-tutorial/targeturl.png)

5. Otevřete certifikát stažený z portálu Azure v poznámkovém bloku a vložte jeho obsah do **oddílu Podpis idp.**

    ![Konfigurace jednotného přihlašování](./media/happyfox-tutorial/cert.png)

6. Klikněte na **tlačítko Uložit nastavení.**

    ![Konfigurace jednotného přihlašování](./media/happyfox-tutorial/savesettings.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k HappyFox.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **HappyFox**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost HappyFox**.

    ![Odkaz HappyFox v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-happyfox-test-user"></a>Vytvořit happyfox test uživatele

V této části je uživatel s názvem Britta Simon vytvořen v HappyFox. HappyFox podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ve Službě HappyFox ještě neexistuje, po ověření se vytvoří nový uživatel.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

1. Když kliknete na dlaždici HappyFox na přístupovém panelu, měli byste se přihlásit stránku aplikace HappyFox. Na přihlašovací stránce by se mělo zobrazit tlačítko **"SAML".**

    ![Plugin](./media/happyfox-tutorial/saml.png)

2. Kliknutím na tlačítko **SAML** se přihlásíte k HappyFoxpomocí svého účtu Azure AD.

Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)