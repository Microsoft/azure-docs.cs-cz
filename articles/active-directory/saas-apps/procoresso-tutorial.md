---
title: 'Kurz: Integrace služby Azure Active Directory s procore sond | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování Procore.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093665"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Kurz: Integrace služby Azure Active Directory s procore sontej so

V tomto kurzu se dozvíte, jak integrovat přihlašující zařízení Procore s Azure Active Directory (Azure AD).
Integrace přihlašování Procore s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k procore přihlašovat.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Jednotné přihlašování Procore (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby Procore SSO, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením procore Jednotné přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Procore SSO podporuje iniciované saso iniciované **IDP**

## <a name="adding-procore-sso-from-the-gallery"></a>Přidání seminsí Procore z galerie

Chcete-li nakonfigurovat integraci přihlašovaného zařízení Procore do služby Azure AD, je třeba přidat procore přiřazované ho z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat přihlašovat procore z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Procore SSO**, z panelu výsledků vyberte **Procore SSO** a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

    ![Procore SSO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Procore na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v jednotném přihlašování Procore.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Procore, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Procore Jednotné přihlašování](#configure-procore-sso-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele procore s přihlašování](#create-procore-sso-test-user)** – chcete-li mít protějšek Britta Simon v procore sondování, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Procore, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **jednotného přihlašování Procore** vyberte **jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![Informace o jednotném přihlašování při přihlašování při řapíku Jednotného přihlašování procore jednotného přihlašování](common/preintegrated.png)

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit přihlašovací služby Procore** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurace jednotného přihlašování procore procore

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně **Jednotného přihlašování Procore,** přihlaste se k webu společnosti procore jako správce.

2. V rozevíracím seznamu nástrojů kliknutím na **Správce** otevřete stránku nastavení přirozeného zabezpečení.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_tool_admin.png)

3. Vložte hodnoty do polí, jak je popsáno níže-

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. V textovém poli **Adresa URL vydavatele jednotného přihlášení** vložte hodnotu **identifikátoru Azure AD,** který jste zkopírovali z portálu Azure.

    b. Do pole **Cílová adresa URL přihlášení saml** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    c. Teď otevřete **xml metadat federací** stažený výše z portálu Azure a zkopírujte certifikát do značky s názvem **X509Certificate**. Vložte zkopírovanou hodnotu do pole **Certifikát jednotného přihlášení x509.**

4. Klikněte na **Save Changes** (Uložit změny).

5. Po těchto nastaveních je třeba odeslat **název domény** (např. **contoso.com),** přes který se přihlašujete do Procore do [týmu podpory Procore](https://support.procore.com/) a oni aktivují federované sso pro tuto doménu.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Jednotné přihlašování Procore.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Procore SSO**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Procore SSO**.

    ![Odkaz procore sso v seznamu aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-procore-sso-test-user"></a>Vytvoření testovacího uživatele s přihlašovat k projádrovým testům

Postupujte podle níže uvedených kroků k vytvoření testovacího uživatele Procore na straně Procore SSO.

1. Přihlaste se k webu společnosti procore jako správce.    

2. V rozevíracím seznamu nástrojů kliknutím na **Adresář** otevřete stránku adresáře společnosti.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klikněte na **Přidat osobu** možnost otevřít formulář a zadejte provést následující možnosti -

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_add.png)

    a. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele jako **Britta**.

    b. Do textového pole **Příjmení** zadejte příjmení uživatele jako **Simon**.

    c. Do textového pole **E-mailová adresa** BrittaSimon@contoso.comzadejte e-mailovou adresu uživatele jako .

    d. Vyberte **šablonu oprávnění** jako **Použít šablonu oprávnění později**.

    e. Klikněte na **Vytvořit**.

4. Zkontrolujte a aktualizujte podrobnosti nově přidaného kontaktu.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_check.png)

5. Klikněte na **Uložit a odeslat pozvánku** (pokud je vyžadována pozvánka prostřednictvím e-mailu) nebo **Uložit** (Uložit přímo) pro dokončení registrace uživatele.
    
    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Procore spři bystří na přístupovém panelu, můžete by měl být automaticky přihlášeni k procore semaforu, u kterého nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

