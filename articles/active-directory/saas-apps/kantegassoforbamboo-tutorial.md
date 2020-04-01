---
title: 'Kurz: Integrace služby Azure Active Directory s připoněmování montou Kantega pro bambus | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování Kantega pro bamboo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 8c951d7f5f1629447b1b5c1fc6e8a1c202246d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099105"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Kurz: Integrace služby Azure Active Directory s připoněmování mandatním zabezpečením kantega pro bambus

V tomto kurzu se dozvíte, jak integrovat zabezpečení připřipojetí kantega pro bambus s Azure Active Directory (Azure AD).
Integrace jednotného přihlavního zabezpečení Kantega pro bamboo s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k přistupuje k Kantega jednotného připojování pro Bamboo.
* Můžete povolit uživatelům, aby se automaticky přihlásili k přihlašování kantega jednotného přihlašování pro Bamboo (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí jednotného připojetí kantega pro bamboo, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování Kantega pro předplatné s povoleným jedním přihlášením bamboo

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro Bamboo podporuje **SP a IDP** inicioval jednotné přihlašování

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Přidání Kantega SSO pro Bamboo z galerie

Chcete-li nakonfigurovat integraci jednotného přijmutí kantega pro Bamboo do Azure AD, musíte přidat Kantega jednotnépřibrádu pro Bamboo z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jednotné přistupování Kantega pro bamboo z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Kantega Jednotné přihlašování pro Bamboo**, z panelu výsledků vyberte **Možnost Kantega Jednotné ho schodišti pro bambus** a pak kliknutím na tlačítko **Přidat** aplikaci přidejte.

    ![Kantega SSO pro Bamboo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Bamboo na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v přihlašování k systému zabezpečení Kantega pro bamboo.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro bamboo, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Kantega pro jednotné přihlašování bamboo](#configure-kantega-sso-for-bamboo-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte kantega jednotného přihlašování pro uživatele testování Bamboo](#create-kantega-sso-for-bamboo-test-user)** – mít protějšek Britta Simon v Kantega jednotného přihlašování pro bamboo, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro bamboo, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Kantega Pro bambus** vyberte jedno **přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Jednotné přihlašování kantega pro doménu Bamboo a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Jednotné přihlašování kantega pro doménu Bamboo a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a adresou URL pro přihlášení. Tyto hodnoty jsou přijímány během konfigurace pluginu Bamboo, který je vysvětlen dále v tutoriálu.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V **části Nastavit jednotné přistupování Kantega pro bamboo** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Konfigurace jednotného přihlašování Kantega pro jednotné přihlašování bamboo

1. V jiném okně webového prohlížeče se přihlaste k místnímu serveru Bamboo jako správce.

1. Najeďte přes kolečko a klikněte **na doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon1.png)

1. V části Karta Doplňky klikněte na **Najít nové doplňky**. Vyhledejte při spojené **spotřebě Pro bambus (SAML & Kerberos)** a kliknutím na tlačítko **Nainstalovat** nainstalujte nový plugin SAML.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon2.png)

1. Spustí se instalace pluginu.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Chcete-li nakonfigurovat nový plugin, klepněte na **tlačítko Konfigurovat.**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon3.png)

1. V sekci **SAML.** V rozevíracím seznamu **Přidat zprostředkovatele identity** vyberte **Azure Active Directory (Azure AD).**

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Zkopírujte hodnotu **IDENTIFIKÁTOR URI id aplikace** a použijte ji jako **identifikátor, adresu URL pro odpověď a adresu URL přihlášení** v části Základní konfigurace **SAML** na webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Import metadat** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Vpočítači vyberte **soubor metadat**a nahrajte soubor metadat, který jste stáhli z webu Azure Portal.

    b. Klikněte na **Další**.

1. V části **Název a umístění přistaň s tezací služby** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Přidejte název zprostředkovatele identity v textovém poli **názvu zprostředkovatele identity** (například Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisový certifikát a klepněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon9.png)

1. V části **Uživatelské účty Bamboo** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. V případě potřeby vyberte **vytvořit uživatele ve vnitřním adresáři aplikace Bamboo** a zadejte příslušný název skupiny pro uživatele (může být vícenásobné ne. skupin oddělených čárkou).

    b. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon11.png)

1. V části **Známé domény pro Azure AD** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon12.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k kantega jednotného přihlašování pro Bamboo.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Kantega Jednotné přihlašování pro Bamboo**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Kantega Jednotné přihlašování pro bamboo**.

    ![Odkaz Kantega SSO pro bamboo v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Vytvoření jednotného přiznaného uživatele kantega pro testování bambusu

Chcete-li povolit uživatelům Azure AD k přihlášení k Bamboo, musí být zřízena do Bamboo. V případě Kantega Jednotné přihlašování pro Bamboo zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru Bamboo jako správce.

1. Najeďte přes kolečko a klikněte na **správu uživatelů**.

    ![Přidat zaměstnance](./media/kantegassoforbamboo-tutorial/user1.png)

1. Klepněte na **položku Uživatelé**. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforbamboo-tutorial/user2.png)

    a. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.come-mail uživatele jako .

    b. Do textového pole **Heslo** zadejte heslo uživatele.

    c. Do textového pole **Potvrdit heslo** zadejte znovu heslo uživatele.

    d. Do textového pole **Celé jméno** zadejte celé jméno uživatele, jako je Britta Simon.

    e. Do textového pole **E-mail** zadejte Brittasimon@contoso.come-mailovou adresu uživatele, který se líbí .

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega jednotného připínání pro bambus na přístupovém panelu, můžete být automaticky přihlášeni k Kantega jednotného připojování pro bambus, u kterého nastavíte jednotné přistupující místo. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
