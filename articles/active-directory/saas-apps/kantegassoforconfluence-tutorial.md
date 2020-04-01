---
title: 'Kurz: Integrace služby Azure Active Directory s jednotném přihlašovacím zabezpečení kantega pro soutok | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a jednotném přihlašování Kantega pro confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 27fa0567eefbb50907c0ed6952333230e874c21d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099041"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Kurz: Integrace služby Azure Active Directory s jednotném přihlašovacím zabezpečení kantega pro soutok

V tomto kurzu se dozvíte, jak integrovat zabezpečení přihlašující k kantega pro soutok s Azure Active Directory (Azure AD).
Integrace jednotného přihlašování Kantega pro soutok s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k zabezpečení přihlašovacího systému Kantega pro confluence.
* Můžete povolit uživatelům, aby se automaticky přihlásili k jednotnému přihlašování Kantega pro confluence (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s kantega jednotného přihlašovacího zabezpečení pro confluence, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování kantega pro předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro Confluence podporuje **SP a IDP** inicioval jednotné přihlašovací systém

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Přidání Kantega SSO pro confluence z galerie

Chcete-li nakonfigurovat integraci jednotného přihlašovacího systému Kantega pro confluence do azure ad, je potřeba přidat kantega jednotného přihlašovacího zabezpečení pro confluence z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jednotné přihlašovací systém Kantega pro confluence z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotné přihlašovací služby Kantega pro confluence**, z panelu výsledků vyberte **možnost Kantega Jednotné přihlašovací služby pro soutok** a potom klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![Kantega SSO pro soutok ve výsledkové listině](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro confluence na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v přistupujícím objektu Kantega pro soutok.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro soutok, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Kantega pro jednotné přihlašování soutoku](#configure-kantega-sso-for-confluence-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte kantega jednotného přihlašování pro uživatele test Confluence](#create-kantega-sso-for-confluence-test-user)** – mít protějšek Britta Simon v Kantega jednotného přihlašování pro confluence, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro confluence, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Kantega pro soutok** vyberte **jedno přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Jednotné přihlašování kantega pro doménu confluence a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Jednotné přihlašování kantega pro doménu confluence a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a adresou URL pro přihlášení. Tyto hodnoty jsou přijímány během konfigurace pluginu Confluence, který je vysvětlen dále v kurzu.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V **části Nastavit jednotné přihlašovací služby Kantega pro confluence** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Konfigurace jednotného přihlašování Kantega pro jednotné přihlašování soutoku

1. V jiném okně webového prohlížeče se přihlaste k **portálu pro správu confluence** jako správce.

1. Najeďte přes kolečko a klikněte **na doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Na kartě **ATLASSIAN MARKETPLACE** klikněte na **Najít nové doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon.png)

1. Vyhledejte nového pluginu SAML pomocí jednotného přihlašování k **kantega pro confluence SAML Kerberos** a kliknutím na tlačítko **Nainstalovat** nainstalujte nový modul plug-in SAML.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Spustí se instalace pluginu.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Chcete-li nakonfigurovat nový plugin, klepněte na **tlačítko Konfigurovat.**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Tento nový plugin lze také nalézt v záložce **USERS & SECURITY.**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon36.png)

1. V sekci **SAML.** V rozevíracím seznamu **Přidat zprostředkovatele identity** vyberte **Azure Active Directory (Azure AD).**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Zkopírujte hodnotu **IDENTIFIKÁTOR URI id aplikace** a použijte ji jako **identifikátor, adresu URL pro odpověď a adresu URL přihlášení** v části Základní konfigurace **SAML** na webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Import metadat** proveďte následující kroky: 

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Vpočítači vyberte **soubor metadat**a nahrajte soubor metadat, který jste stáhli z webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Název a umístění přistaň s tezací služby** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Přidejte název zprostředkovatele identity v textovém poli **názvu zprostředkovatele identity** (například Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisový certifikát a klepněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon9.png)

1. V části **Uživatelské účty Confluence** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. V případě potřeby vyberte **vytvořit uživatele ve vnitřním adresáři soutoku** a zadejte příslušný název skupiny pro uživatele (může být vícenásobné ne. skupin oddělených čárkou).

    b. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon11.png)

1. V části **Známé domény pro Azure AD** proveďte následující kroky: 

    ![Konfigurace jednotného přihlašování](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. V levém panelu stránky **vyberte Známé domény.**

    b. Zadejte název domény do textového pole **Známé domény.**

    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k kantega jednotného přihlašování pro confluence.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Kantega Jednotné přihlašování pro confluence**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Kantega Jednotné přihlašování pro soutok**.

    ![Odkaz Kantega SSO pro soutok v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-kantega-sso-for-confluence-test-user"></a>Vytvořit jednotné přihlašovací akce Kantega pro uživatele testu soutoku

Chcete-li povolit uživatelům Azure AD přihlásit se k Confluence, musí být zřízena do Confluence. V případě Kantega jednotného přihlašování pro confluence zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému jednotnému přihlašování Kantega pro web společnosti Confluence jako správce.

1. Najeďte přes kolečko a klikněte na **správu uživatelů**.

    ![Přidat zaměstnance](./media/kantegassoforconfluence-tutorial/user1.png)

1. V části Uživatelé klikněte na kartu **Přidat uživatele.** Na stránce **dialogového** okna Přidat uživatele proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.come-mail uživatele jako .

    b. Do textového pole **Celé jméno** zadejte celé jméno uživatele, jako je Britta Simon.

    c. Do textového pole **E-mail** zadejte Brittasimon@contoso.come-mailovou adresu uživatele, který se líbí .

    d. Do textového pole **Heslo** zadejte heslo pro uživatele.

    e. Klikněte na **Potvrdit heslo** znovu zadat heslo.

    f. Klikněte na **tlačítko Přidat.**

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega jednotného přihlašovacího systému pro soutok na přístupovém panelu, můžete být automaticky přihlášeni k kantega jednotného přihlašovacího systému pro soutoku, pro které nastavíte jednotné přihlašovací. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

