---
title: 'Kurz: Azure Active Directory integrace s cloudem Lifesize | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lifesize cloudem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: b462dff45263ba3f5e533cd6bd7c4ce089933f66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855391"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Kurz: Azure Active Directory integrace s cloudem Lifesize

V tomto kurzu se dozvíte, jak integrovat Lifesize Cloud s Azure Active Directory (Azure AD).
Integrace Lifesize cloudu s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup ke cloudu Lifesize.
* Můžete uživatelům povolit, aby se automaticky přihlásili k Lifesize cloudu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Lifesize cloudem potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Lifesize cloudu s podporou jednotného přihlašování

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Lifesize Cloud podporuje jednotné přihlašování (SSO) iniciované **SP**

* Lifesize Cloud podporuje **automatizované** zřizování uživatelů

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Přidání cloudu Lifesize z Galerie

Pokud chcete nakonfigurovat integraci Lifesize cloudu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Lifesize Cloud z galerie.

**Pokud chcete přidat Lifesize Cloud z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Lifesize Cloud**, vyberte **Lifesize Cloud** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Lifesize Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Lifesize cloudu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu Lifesize.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu Lifesize, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Lifesize Cloud](#configure-lifesize-cloud-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte uživatele cloudového testu Lifesize](#create-lifesize-cloud-test-user)** , abyste měli protějšek Britta Simon v cloudu Lifesize, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí cloudu Lifesize, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **cloudových aplikací Lifesize** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Lifesize informace o jednotném přihlašování v cloudové doméně a adresách URL](common/sp-identifier-relay.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://login.lifesizecloud.com/ls/?acs`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://login.lifesizecloud.com/<companyname>`

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a stavu přenosu. Obraťte se na [tým podpory Lifesize cloudového klienta](https://www.lifesize.com/en/support) , který získá adresu URL Sign-On a hodnoty identifikátoru a můžete získat hodnotu stavu přenosu z konfigurace jednotného přihlašování, která je vysvětlena dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení cloudu Lifesize** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-lifesize-cloud-single-sign-on"></a>Konfigurace Lifesize cloudu s jedním Sign-On

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování (SSO), přihlaste se do cloudové aplikace Lifesize s oprávněními správce.

2. V pravém horním rohu klikněte na své jméno a pak klikněte na **nastavení zálohy**.

    ![Snímek obrazovky se zobrazí položka nabídky Upřesnit nastavení.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. V nastavení zálohy teď klikněte na odkaz **Konfigurace jednotného přihlašování** . Otevře se stránka konfigurace jednotného přihlašování pro vaši instanci.

    ![Snímek obrazovky ukazuje upřesňující nastavení, kde můžete vybrat konfiguraci S.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Teď v uživatelském rozhraní konfigurace jednotného přihlašování nakonfigurujte následující hodnoty.

    ![Snímek obrazovky se zobrazí na stránce konfigurace S S/O, kde můžete zadat hodnoty, které jsou popsány.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Do textového pole **vystavitele zprostředkovatele identity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    b.  Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64 stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát X. 509** .
  
    d. V poli mapování atributů SAML pro textové pole jméno zadejte hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. V poli mapování atributů SAML pro textové pole **příjmení** zadejte hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. V poli mapování atributů SAML pro textové pole **e-mail** zadejte hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Chcete-li zkontrolovat konfiguraci, můžete kliknout na tlačítko **test** .

    >[!NOTE]
    >Pro úspěšné testování je potřeba dokončit Průvodce konfigurací služby Azure AD a poskytnout přístup uživatelům nebo skupinám, kteří můžou test provádět.

6. Jednotné přihlašování povolte tak, že zkontrolujete tlačítko **Povolit jednotné přihlašování** .

7. Nyní klikněte na tlačítko **aktualizovat** , aby se všechna nastavení uložila. Tím se vygeneruje hodnota RelayState. Zkopírujte hodnotu RelayState, která se vygeneruje v textovém poli, a vložte ji do textového pole **stav přenosu** v části **Lifesize cloudové domény a adresy URL** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Lifesize cloudu.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Cloud Lifesize**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cloud Lifesize**.

    ![Odkaz Lifesize Cloud v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-lifesize-cloud-test-user"></a>Vytvořit uživatele cloudového testu Lifesize

V této části vytvoříte uživatele s názvem Britta Simon v cloudu Lifesize. Lifesize Cloud podporuje automatické zřizování uživatelů. Po úspěšném ověření ve službě Azure AD se uživatel v aplikaci automaticky zřídí.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Cloud Lifesize, měli byste získat přihlašovací stránku cloudové aplikace Lifesize. Zde je třeba zadat své uživatelské jméno a poté, co budete přesměrováni na domovskou stránku aplikace.

Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
