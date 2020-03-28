---
title: 'Kurz: Integrace Azure Active Directory se softwarem OneTrust Privacy Management Software | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a softwarem onetrustové správy ochrany osobních údajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 5fbec8b6a1a21826896f8e2499b1e8b7237d0ff9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095900"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Kurz: Integrace Azure Active Directory se softwarem onetrust pro správu ochrany osobních údajů

V tomto kurzu se dozvíte, jak integrovat OneTrust Software pro správu ochrany osobních údajů s Azure Active Directory (Azure AD).
Integrace softwaru pro správu ochrany osobních údajů OneTrust s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k OneTrust Software pro správu ochrany osobních údajů.
* Uživatelům můžete povolit automatické přihlášení k onetrustovému softwaru pro správu ochrany osobních údajů (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí softwaru onetrust pro správu ochrany osobních údajů, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* OneTrust Software pro správu ochrany osobních údajů s povoleným jednorázovým přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OneTrust Privacy Management Software podporuje **SP** a **IDP** inicioval SSO

* OneTrust Privacy Management Software podporuje **just in time** zřizování uživatelů

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Přidání softwaru onetrustpro správu ochrany osobních údajů z galerie

Chcete-li nakonfigurovat integraci softwaru onetrust pro správu ochrany osobních údajů do Azure AD, musíte přidat software onetrust ochrany osobních údajů z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat software onetrust pro správu ochrany osobních údajů z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **OneTrust Privacy Management Software**, z panelu výsledků vyberte **OneTrust Privacy Management Software** a potom klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Software pro správu ochrany osobních údajů OneTrust v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru onetrust pro správu ochrany osobních údajů na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v softwaru onetrust ové správy ochrany osobních údajů.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí softwaru onetrust ochrany osobních údajů, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte onetrustový software pro správu osobních údajů single sign-on](#configure-onetrust-privacy-management-software-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu softwaru onetrustové správy ochrany osobních údajů](#create-onetrust-privacy-management-software-test-user)** – chcete-li mít protějšek Britty Simonové v softwaru pro správu ochrany osobních údajů OneTrust, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí softwaru onetrust pro správu ochrany osobních údajů, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **OneTrust Privacy Management Software** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![OneTrust Privacy Management Software Domain and URLs single sign-on information OneTrust Privacy Management Software Domain and URLs single sign-on information OneTrust Privacy Management Software Domain and URLs single sign-on information OneTrust Privacy Management Software](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`https://www.onetrust.com/saml2`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.onetrust.com/auth/consumerservice`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![OneTrust Privacy Management Software Domain and URLs single sign-on information OneTrust Privacy Management Software Domain and URLs single sign-on information OneTrust Privacy Management Software Domain and URLs single sign-on information OneTrust Privacy Management Software](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL pro odpověď a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta onetrustového softwaru pro správu ochrany osobních údajů.](mailto:support@onetrust.com) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části Nastavit software pro **správu ochrany osobních údajů OneTrust** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-onetrust-privacy-management-software-single-sign-on"></a>Konfigurace jednotného přihlášení softwaru OneTrust pro správu ochrany osobních údajů

Chcete-li nakonfigurovat jednotné přihlašování na straně **OneTrust Privacy Management Software,** musíte odeslat stažený **xml metadat federace** a příslušné zkopírované adresy URL z portálu Azure do týmu podpory softwaru [OneTrust Privacy Management Software](mailto:support@onetrust.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k OneTrust Software pro správu ochrany osobních údajů.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **OneTrust Software pro správu ochrany osobních údajů**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **OneTrust Software pro správu ochrany osobních údajů**.

    ![Odkaz OneTrust Privacy Management Software v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-onetrust-privacy-management-software-test-user"></a>Vytvoření testovacího softwaru onetrustového softwaru pro správu osobních údajů

V této části je uživatel s názvem Britta Simon vytvořen v OneTrust Software pro správu osobních údajů. OneTrust Privacy Management Software podporuje just-in-time zřizování uživatelů, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel v softwaru onetrustu pro správu ochrany osobních údajů ještě neexistuje, vytvoří se po ověření nový uživatel.

>[!Note]
>Pokud potřebujete vytvořit uživatele ručně, obraťte se na [tým podpory softwaru onetrust privacy management software](mailto:support@onetrust.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici OneTrust Privacy Management Software na přístupovém panelu, měli byste být automaticky přihlášeni k onetrustu pro správu osobních údajů software, pro který nastavíte přiřazování osobních údajů. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

