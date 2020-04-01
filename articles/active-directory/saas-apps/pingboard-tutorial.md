---
title: 'Kurz: Integrace služby Azure Active Directory s pingboardem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a Pingboardem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 341d8dd712b858572ec5df76b176258ca87c8857
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094423"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Kurz: Integrace služby Azure Active Directory s pingboardem

V tomto kurzu se dozvíte, jak integrovat Pingboard s Azure Active Directory (Azure AD).
Integrace pingboardu s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Pingboard.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Pingboardu (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí pingboardu, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením pingboard

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pingboard podporuje **sp** a **IDP** inicioval sso

* Pingboard podporuje [automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Přidání Pingboard z galerie

Chcete-li nakonfigurovat integraci Pingboard do Azure AD, musíte přidat Pingboard z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat pingboard z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Pingboard**, z panelu výsledků vyberte **Pingboard** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Pingboard v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí pingboardu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v pingboardu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí pingboardu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace pingboardu jednotného přihlášení](#configure-pingboard-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit pingboard testovací ho uživatele](#create-pingboard-test-user)** – mít protějšek Britta Simon v Pingboard, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad pomocí pingboardu, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikace **Pingboard** **možnost Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Pingboard Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`http://app.pingboard.com/sp`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<entity-id>.pingboard.com/auth/saml/consume`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Pingboard Domény a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL pro odpověď a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta Pingboard.](https://support.pingboard.com/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit pingboard** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-pingboard-single-sign-on"></a>Konfigurace jednotného přihlašování pingboardu

1. Chcete-li nakonfigurovat sso na straně Pingboard, otevřete nové okno prohlížeče a přihlaste se k účtu Pingboard. Chcete-li nastavit jednotné přihlašování, musíte být správcem pingboardu.

2. V horní nabídce vyberte **Aplikace > integrace**

    ![Konfigurace jednotného přihlašování](./media/pingboard-tutorial/Pingboard_integration.png)

3. Na stránce **Integrace** najděte dlaždici **Azure Active Directory** a klikněte na ni.

    ![Pingboard jednotné přihlášení integrace](./media/pingboard-tutorial/Pingboard_aad.png)

4. V modálním, který následuje, klikněte na **tlačítko "Konfigurovat"**

    ![Konfigurační tlačítko Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na následující stránce zjistíte, že "Integrace připínání k opětovnému připínání a zabezpečení Azure je povolena". Otevřete stažený soubor XML metadat v poznámkovém bloku a vložte obsah do **metadat IDP**.

    ![Obrazovka konfigurace s přihlašováním k systému SSO pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Soubor je ověřen a pokud je vše správné, bude nyní povoleno jednotné přihlašování.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu pingboard.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Pingboard**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Pingboard**.

    ![Odkaz Pingboard v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-pingboard-test-user"></a>Vytvořit testovacího uživatele Pingboard

Cílem této části je vytvořit uživatele s názvem Britta Simon v Pingboard. Pingboard podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povoleno. Další podrobnosti najdete [zde](pingboard-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud potřebujete vytvořit uživatele ručně, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Pingboard jako správce.

2. Klikněte na tlačítko **Přidat zaměstnance** na stránce **Adresář.**

    ![Přidat zaměstnance](./media/pingboard-tutorial/create_testuser_add.png)

3. Na stránce dialogového okna **Přidat zaměstnance** proveďte následující kroky:

    ![Pozvání lidí](./media/pingboard-tutorial/create_testuser_name.png)

    a. Do textového pole **Celé jméno** zadejte celé jméno uživatele, jako **je Britta Simon**.

    b. Do textového pole **E-mail** zadejte **brittasimon@contoso.com**e-mailovou adresu uživatele, který se líbí .

    c. Do textového pole **Název úlohy** zadejte název úlohy Britty Simonové.

    d. V rozevíracím souboru **Umístění** vyberte umístění Britty Simonové.

    e. Klikněte na **Přidat**.

4. Potvrzovací obrazovka se objeví pro potvrzení přidání uživatele.

    ![Potvrdit](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a následuje odkaz pro potvrzení svého účtu před tím, než se stane aktivním.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Pingboard na přístupovém panelu, můžete by měl být automaticky přihlášeni k Pingboard, u kterého nastavíte přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
