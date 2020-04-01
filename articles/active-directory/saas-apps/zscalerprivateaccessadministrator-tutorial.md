---
title: 'Kurz: Integrace služby Azure Active Directory se správcem soukromého přístupu Zscaler | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a správcem privátního přístupu Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085642"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Kurz: Integrace služby Azure Active Directory se správcem privátního přístupu Zscaler

V tomto kurzu se dozvíte, jak integrovat správce privátního přístupu Zscaler s Azure Active Directory (Azure AD).
Integrace správce privátního přístupu Zscaler s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscaler správce soukromého přístupu.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke správci soukromého přístupu Zscaler (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se správcem soukromého přístupu Zscaler, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením správce soukromého přístupu Zscaler

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce soukromého přístupu Zscaler podporuje **sp** a **IDP** inicioval sso

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Přidání správce soukromého přístupu Zscaler z galerie

Chcete-li nakonfigurovat integraci správce soukromého přístupu Zscaler do Azure AD, musíte přidat správce soukromého přístupu Zscaler z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat správce soukromého přístupu Zscaler z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Správce soukromého přístupu Zscaler**, z panelu výsledků vyberte **Zscaler Private Access Administrator** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Správce soukromého přístupu Zscaler v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí správce soukromého přístupu Zscaler na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v zscalerovém správci soukromého přístupu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí správce soukromého přístupu Zscaler, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení správce soukromého přístupu Zscaler](#configure-zscaler-private-access-administrator-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte zscaler soukromého správce přístupu testovacího uživatele](#create-zscaler-private-access-administrator-test-user)** – mít protějšek Britta Simon v Zscaler správce soukromého přístupu, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí správce privátního přístupu Zscaler, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Správce privátního přístupu Zscaler** vyberte **jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Zscaler Private Access Administrator Domain and URLs single sign-on information Zscaler Private Access Administrator Domain and URLs single sign-on information Zscaler Private Access Administrator Domain and URLs single sign-on information Zscaler Private Access](common/idp-relay.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klepněte na **tlačítko Nastavit další adresy URL**.

    d. Do textového pole **Stav přenosu** zadejte adresu URL:`idpadminsso`

5.  Chcete-li aplikaci nakonfigurovat v režimu iniciovaném **službou SP,** proveďte následující krok:

    ![Zscaler Private Access Administrator Domain and URLs single sign-on information Zscaler Private Access Administrator Domain and URLs single sign-on information Zscaler Private Access Administrator Domain and URLs single sign-on information Zscaler Private Access](common/both-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se na tým podpory klienta správce [privátního přístupu Zscaler,](https://help.zscaler.com/zpa-submit-ticket) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit správce soukromého přístupu Zscaler** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurace jednotného přihlašování správce soukromého přístupu Zscaler

1. V jiném okně webového prohlížeče se podepište správcem soukromého přístupu Zscaler jako správce.

2. V horní části klikněte na **položku Správa** a přejděte do části **OVĚŘOVÁNÍ** na **položku Konfigurace protokolu IdP**.

    ![Správce soukromého přístupu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. V pravém horním rohu klikněte na **Přidat konfiguraci protokolu IdP**. 

    ![Addidp správce soukromého přístupu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na stránce **Přidat konfiguraci protokolu IdP proveďte** následující kroky:
 
    ![Výběr idpselect správce soukromého přístupu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kliknutím na **Vybrat soubor** nahrajete stažený soubor metadat z Azure AD do pole **Nahrání souboru metadat IdP.**

    b. Přečte **metadata IdP** z Azure AD a naplní všechny informace o polích, jak je znázorněno níže.

    ![Správce soukromého přístupu Zscaler idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Vyberte **možnost Jednotné přihlášení** jako **správce**.

    d. Vyberte svou doménu z pole **Domény.**
    
    e. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu správce soukromého přístupu Zscaler.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Správce soukromého přístupu Zscaler**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku Správce soukromého přístupu Zscaler**.

    ![Odkaz Správce soukromého přístupu Zscaler v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-zscaler-private-access-administrator-test-user"></a>Vytvořit testovacího uživatele správce soukromého přístupu Zscaler

Aby se uživatelé Azure AD mohli přihlásit k privátnímu správci přístupu Zscaler, musí být zřízeni do správce soukromého přístupu Zscaler. V případě Správce soukromého přístupu Zscaler zřizování je ruční úloha.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Správce soukromého přístupu Zscaler jako správce.

2. V horní části klikněte na **položku Správa** a přejděte do části **OVĚŘOVÁNÍ** na **položku Konfigurace protokolu IdP**.

    ![Správce soukromého přístupu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Na **položku Správci** z levé strany nabídky.

    ![Správce soukromého přístupu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. V pravém horním rohu klikněte na **Přidat správce**:

    ![Správce soukromého přístupu Zscaler přidat správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na stránce **Přidat správce** proveďte následující kroky:

    ![Správce soukromého přístupu Zscaler správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Do textového pole **Uživatelské jméno** zadejte BrittaSimon@contoso.come-mail uživatele jako .

    b. Do textového pole **Heslo** zadejte heslo.

    c. Do textového pole **Potvrdit heslo** zadejte heslo.

    d. Vyberte **roli** jako **správce soukromého přístupu Zscaler**.

    e. Do textového pole **E-mail** zadejte e-mail uživatele, který se líbí BrittaSimon@contoso.com.

    f. Do textového pole **Telefon** zadejte telefonní číslo.

    g. V textovém poli **časové pásmo** vyberte časové pásmo.

    h. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Správce soukromého přístupu Zscaler na přístupovém panelu, měli byste být automaticky přihlášeni k zscaler soukromého přístupu správce, u kterého nastavíte přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

