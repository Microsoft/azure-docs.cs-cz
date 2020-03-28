---
title: 'Kurz: Integrace služby Azure Active Directory s hostovaným grafitem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a hostovaným grafitem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6c5b689d00bd1adad820043840c43f49666655c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158076"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Kurz: Integrace služby Azure Active Directory s hostovaným grafitem

V tomto kurzu se dozvíte, jak integrovat hostovaný grafit s Azure Active Directory (Azure AD).
Integrace hostovaného grafitu s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k hostované grafitu.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k hostovanému grafitu (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s hostovaným grafitem, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Hostované předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Hostovaný grafit podporuje **sp a IDP** iniciované sso
* Hostovaný grafit podporuje zřizování uživatelů **just in time**

## <a name="adding-hosted-graphite-from-the-gallery"></a>Přidání hostovaného grafitu z galerie

Chcete-li nakonfigurovat integraci hostovaného grafitu do Azure AD, musíte přidat hostovaný grafit z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat hostovaný grafit z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Hostovaný grafit**, z panelu výsledků vyberte **Hostovaný grafit** a pak klepnutím na **tlačítko Přidat** aplikaci přidejte.

     ![Hostovaný grafit v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí hostovaného grafitu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v hostovaném grafitu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí hostovaného grafitu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace hostovaného rychlého jednotného přihlášení](#configure-hosted-graphite-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte hostovaného uživatele testu graphite](#create-hosted-graphite-test-user)** – chcete-li mít protějšek Britta Simon v hostovanégrafitu, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí hostovaného grafitu, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce Integrace aplikací **hostovaný grafit** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Hostované informace o doménách graphite a adresách URL s jedním přihlášením](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://www.hostedgraphite.com/metadata/<user id>`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.hostedgraphite.com/complete/saml/<user id>`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Hostované informace o doménách graphite a adresách URL s jedním přihlášením](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Vezměte prosím na vědomí, že to nejsou skutečné hodnoty. Tyto hodnoty je třeba aktualizovat skutečným identifikátorem, adresou URL pro odpověď a adresou URL přihlášení. Chcete-li získat tyto hodnoty, můžete přejít na nastavení aplikace >SAML na straně aplikace nebo kontaktovat [tým podpory hostovaného grafitu](mailto:help@hostedgraphite.com).

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit hostovaný grafit zkopírujte** příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-hosted-graphite-single-sign-on"></a>Konfigurace jednotného přihlašování s hostitelem graphitu

1. Přihlaste se k hostovanému klientovi graphite jako správce.

2. Přejděte na **stránku Nastavení SAML** na postranním panelu **(aplikace Access -> nastavení SAML**).

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Potvrďte, že tyto hlavní uživatele odpovídají vaší konfiguraci provedené v části **Základní konfigurace SAML** na webu Azure Portal.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. V textových polích entity **nebo ID vystavitela** a **přihlašovací adresy URL při přiřazovacím čísle** vložte hodnotu **identifikátoru azure reklamy** a **přihlašovací adresy URL,** které jste zkopírovali z webu Azure Portal.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Jako **výchozí roli uživatele**vyberte možnost Jen pro **čtení** .

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku staženém z webu Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát X.509.**

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Klikněte na **tlačítko Uložit.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k hostované graphite.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Hostovaný grafit**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Hostovaný grafit**.

    ![Odkaz Hostovaný grafit v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-hosted-graphite-test-user"></a>Vytvořit hostovaného uživatele testu graphitu

V této části je vytvořen uživatel s názvem Britta Simon v hostovanégrafit. Hostovaný grafit podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v hostovaném grafitu, vytvoří se po ověření nový uživatel.

> [!NOTE]
> Pokud potřebujete vytvořit uživatele ručně, musíte kontaktovat tým podpory hostovaného <mailto:help@hostedgraphite.com>grafitu prostřednictvím .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Hosted Graphite na přístupovém panelu, můžete by měl být automaticky přihlášeni k hostované grafitu, pro které nastavíte přivytváření přistupujícího stavu. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

