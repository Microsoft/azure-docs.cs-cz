---
title: 'Kurz: Integrace služby Azure Active Directory s aplikací SilkRoad Life Suite | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a sadou SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090822"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Kurz: Integrace služby Azure Active Directory s aplikací SilkRoad Life Suite

V tomto kurzu se dozvíte, jak integrovat SilkRoad Life Suite s Azure Active Directory (Azure AD).
Integrace sady SilkRoad Life Suite s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SilkRoad Life Suite.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili ke službě SilkRoad Life Suite (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí sady SilkRoad Life Suite, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s podporou jednotného přihlášení SilkRoad Life Suite

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SilkRoad Life Suite podporuje **SP** zahájila SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Přidání SilkRoad Life Suite z galerie

Chcete-li nakonfigurovat integraci sady SilkRoad Life Suite do služby Azure AD, musíte přidat sadu SilkRoad Life Suite z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat silkroad life suite z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SilkRoad Life Suite**, z panelu výsledků vyberte **SilkRoad Life Suite** a pak klikněte na tlačítko **Přidat** a přidejte aplikaci.

    ![SilkRoad Life Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sady SilkRoad Life Suite na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v sadě SilkRoad Life Suite.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sady SilkRoad Life Suite, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte single sign-on sady SilkRoad Life Suite](#configure-silkroad-life-suite-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** – chcete-li mít protějšek Britta Simon v SilkRoad Life Suite, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí sady SilkRoad Life Suite, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SilkRoad Life Suite** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**, proveďte v části Základní konfigurace **SAML** následující kroky:

    > [!NOTE]
    > Soubor metadat **poskytovatele služeb** bude vysvětlen dále v tomto kurzu.

    a. Klikněte na **Nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL identifikátorů** a **odpovědí** automaticky naplní v části Základní konfigurace SAML:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Pokud hodnoty **adresy URL** **identifikátorů** a odpovědí nejsou automaticky polulated, vyplňte hodnoty ručně podle vašeho požadavku.

    d. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.silkroad-eng.com/Authentication/`

5. Pokud v části **Základní konfigurace SAML** nemáte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    ![SilkRoad Life Suite Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Obraťte se na [tým podpory klienta SilkRoad Life Suite,](https://www.silkroad.com/locations/) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit SilkRoad Life Suite** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurace single sign-On sady SilkRoad Life Suite

1. Přihlaste se na své stránky společnosti SilkRoad jako správce.

    > [!NOTE]
    > Chcete-li získat přístup k aplikaci Ověřování SilkRoad Life Suite pro konfiguraci federace s Microsoft Azure AD, obraťte se na podporu SilkRoad nebo zástupce služeb SilkRoad.

1. Přejděte na **zprostředkovatele služeb**a klepněte na **položku Podrobnosti federace**.

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klikněte na **Stáhnout metadata federace**a uložte soubor metadat do počítače. Metadata stažených federací použijte jako **soubor metadat poskytovatele služeb** v části Základní konfigurace **SAML** na webu Azure Portal.

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. V aplikaci **SilkRoad** klikněte na **Authentication Sources**.

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klepněte na **tlačítko Přidat zdroj ověřování**.

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. V části **Přidat zdroj ověřování** proveďte následující kroky:

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. V **části Možnost 2 – Soubor metadat**klikněte na **Procházet** a nahrajte stažený soubor metadat z webu Azure Portal.
  
    b. Klikněte na **Vytvořit zprostředkovatele identity pomocí dat souboru**.

1. V části **Zdroje ověřování** klepněte na tlačítko **Upravit**.

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. V dialogovém okně **Upravit zdroj ověřování** proveďte následující kroky:

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Funkce **Povoleno**vyberte **možnost Ano**.

    b. V textovém poli **EntityId** vložte hodnotu **identifikátoru Azure AD,** který jste zkopírovali z webu Azure Portal.

    c. Do textového pole **Popis protokolu Idp** zadejte popis konfigurace (například: *Azure AD SSO*).

    d. V textovém poli **Soubor metadat** nahrajte soubor **metadat,** který jste stáhli z webu Azure Portal.
  
    e. Do textového pole **Název protokolu IdP** zadejte název, který je specifický pro vaši konfiguraci (například *Azure SP).*
  
    f. Do textového pole **Adresa URL služby Odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    g. Do textového pole **Adresa URL přihlašovací služby** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.

    h. Klikněte na **Uložit**.

1. Zakažte všechny ostatní zdroje ověřování.

    ![Jednotné přihlašování azure ad](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k SilkRoad Life Suite.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **SilkRoad Life Suite**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SilkRoad Life Suite**.

    ![Odkaz SilkRoad Life Suite v seznamu Aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-silkroad-life-suite-test-user"></a>Vytvořte uživatele testu SilkRoad Life Suite

V této části vytvoříte uživatele s názvem Britta Simon v SilkRoad Life Suite. Spolupracujte s [týmem podpory klienta SilkRoad Life Suite](https://www.silkroad.com/locations/) a přidejte uživatele do platformy SilkRoad Life Suite. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SilkRoad Life Suite na přístupovém panelu, měli byste být automaticky přihlášeni k SilkRoad Life Suite, u kterého nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
