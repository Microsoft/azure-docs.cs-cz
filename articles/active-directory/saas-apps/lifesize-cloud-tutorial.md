---
title: 'Kurz: Integrace služby Azure Active Directory s lifesize cloudem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a lifesize cloudem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e886a1fa3f590ac94dbf088520e6770690ee21ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159575"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Kurz: Integrace Azure Active Directory s Lifesize Cloudem

V tomto kurzu se dozvíte, jak integrovat Lifesize Cloud s Azure Active Directory (Azure AD).
Integrace Lifesize Cloudu s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Lifesize Cloud.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Lifesize Cloud (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí lifesize cloudu, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením lifesize cloudu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Lifesize Cloud podporuje **sp** inicioval SSO

* Lifesize Cloud podporuje **automatické** zřizování uživatelů

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Přidání cloudu Lifesize z galerie

Chcete-li nakonfigurovat integraci Lifesize Cloudu do Azure AD, musíte přidat Lifesize Cloud z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat lifesize cloud z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Lifesize Cloud**, z panelu výsledků vyberte **Lifesize Cloud** a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Lifesize Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí lifesize cloudu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Lifesize Cloudu.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí lifesize cloudu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace lifesize cloudu single sign-on](#configure-lifesize-cloud-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Lifesize Cloud](#create-lifesize-cloud-test-user)** – chcete-li mít protějšek Britta Simon v Lifesize Cloud, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí lifesize cloudu, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Lifesize Cloud** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Lifesize Cloud domain a adresy URL jednotné přihlašovací informace](common/sp-identifier-relay.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://login.lifesizecloud.com/ls/?acs`

    b. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://login.lifesizecloud.com/<companyname>`

    c. Klepněte na **tlačítko Nastavit další adresy URL**.

    d. Do textového pole **Stav přenosu** zadejte adresu URL pomocí následujícího vzoru:`https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a stavem přenosu. Obraťte se na [tým podpory cloudového klienta Lifesize,](https://www.lifesize.com/en/support) abyste získali přihlašovací adresu URL a hodnoty identifikátorů, a můžete získat hodnotu stavu přenosu z konfigurace spřimit, která je vysvětlena dále v kurzu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Lifesize Cloud** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-lifesize-cloud-single-sign-on"></a>Konfigurace jednotného přihlašování v cloudu lifesize

1. Chcete-li získat nakonfigurované přihlašování pro vaši aplikaci, přihlaste se do aplikace Lifesize Cloud s oprávněními správce.

2. V pravém horním rohu klikněte na své jméno a pak klikněte na **Advance Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. V nastavení předem nyní klikněte na odkaz **Konfigurace přizpůsobování služby ASO.** Otevře stránku konfigurace přizpůsobování přizpůsobí pro vaši instanci.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Teď nakonfigurujte následující hodnoty v konfiguračním unovém rozhraní s přisyp.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. V textovém poli **Vydavatel zprostředkovatele identity** vložte hodnotu **identifikátoru Azure Ad,** kterou jste zkopírovali z webu Azure Portal.

    b.  V textovém poli **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    c. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku staženém z webu Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát X.509.**
  
    d. Do mapování atributů SAML pro textové pole Jméno zadejte hodnotu jako`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Do mapování atributů SAML pro textové pole **Příjmení** zadejte hodnotu jako`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Do mapování atributů SAML pro textové pole **E-mail** zadejte hodnotu jako`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Chcete-li zkontrolovat konfiguraci, můžete kliknout na tlačítko **Test.**

    >[!NOTE]
    >Pro úspěšné testování je třeba dokončit průvodce konfigurací ve službě Azure AD a také poskytnout přístup uživatelům nebo skupinám, kteří mohou provést test.

6. Povolte sso kontrolou na **tlačítko Povolit spřimit.**

7. Nyní klikněte na tlačítko **Aktualizovat** tak, aby všechna nastavení jsou uloženy. Tím se vygeneruje hodnota RelayState. Zkopírujte hodnotu RelayState, která je generována v textovém poli, vložte ji do textového pole **Stav přenosu** v části **Lifesize Cloud Domain and URLs.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Lifesize Cloud.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Lifesize Cloud**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Lifesize Cloud**.

    ![Odkaz Lifesize Cloud v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-lifesize-cloud-test-user"></a>Vytvoření testovacího uživatele lifesize cloudu

V této části vytvoříte uživatele s názvem Britta Simon v Lifesize Cloud. Lifesize cloud podporuje automatické zřizování uživatelů. Po úspěšném ověření ve službě Azure AD se uživatel automaticky zřídí v aplikaci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Lifesize Cloud na přístupovém panelu, měli byste získat přihlašovací stránku aplikace Lifesize Cloud. Zde musíte zadat své uživatelské jméno a poté budete přesměrováni na domovskou stránku aplikace.

Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
