---
title: 'Kurz: Azure Active Directory integrace s riskware | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a riskware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b147037a58e4af59c0c3ccd56b6f6b241d775308
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553316"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Kurz: Azure Active Directory integrace s riskware

V tomto kurzu se dozvíte, jak integrovat riskware s Azure Active Directory (Azure AD).
Integrace riskware s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k riskware.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k riskware (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s riskware potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Riskwarem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Riskware podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-riskware-from-the-gallery"></a>Přidání riskware z Galerie

Pokud chcete nakonfigurovat integraci riskware do služby Azure AD, musíte přidat riskware z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat riskware z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **riskware**, vyberte **riskware** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Riskware v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí riskware na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v riskware.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí riskware, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování riskware](#configure-riskware-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření riskware Test User](#create-riskware-test-user)** – pro Britta Simon v riskware, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí riskware, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **riskware** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně riskware a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | SK| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | SPUSTIT| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL:
    
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | SK| `https://riskcloud.net/prod` |
    | SPUSTIT| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta riskware](mailto:support@pansoftware.com.au) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení riskware** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-riskware-single-sign-on"></a>Nakonfigurovat riskware jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu riskware společnosti jako správce.

1. V pravém horním rohu kliknutím na **Údržba** otevřete stránku Údržba.

    ![Údržba konfigurací riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stránce Údržba klikněte na **ověřování**.

    ![AUTHEN konfigurace riskware](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na stránce **Konfigurace ověřování** proveďte následující kroky:

    ![Authenconfig konfigurace riskware](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Pro ověřování vyberte **typ** jako **SAML** .

    b. Do textového pole **Code (kód** ) zadejte kód, například AZURE_UAT.

    c. Do textového pole **Popis** zadejte popis jako konfigurace Azure pro jednotné přihlašování.

    d. Do textového pole na **stránce jednotného přihlašování** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **stránky** pro odhlášení vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    f. Do textového **pole po pole formuláře** zadejte název pole obsažený v odpovědi post, který obsahuje SAML jako SAMLResponse

    například Do textového pole **název značky identity XML** zadejte atribut Type, který obsahuje jedinečný identifikátor v odpovědi SAML, jako je NameId.

    h. Otevřete stažený soubor **XML metadat** z Azure Portal v programu Poznámkový blok, zkopírujte certifikát ze souboru metadat a vložte ho do textového pole **certifikátu** .

    i. Do textového pole **Adresa URL příjemce** vložte hodnotu **adresy URL odpovědi**, kterou získáte od týmu podpory.

    j. Do textového pole **vystavitele** vložte hodnotu **identifikátoru**, kterou získáte od týmu podpory.

    > [!Note]
    > Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta riskware](mailto:support@pansoftware.com.au) .

    k. Zaškrtněte políčko **použít příspěvek** .

    l. Zaškrtněte políčko **použít žádost SAML** .

    m. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k riskware.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **riskware**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **riskware**.

    ![Odkaz riskware v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-riskware-test-user"></a>Vytvořit testovacího uživatele riskware

Aby se uživatelé Azure AD mohli přihlašovat k riskware, musí se zřídit v riskware. V riskware je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k riskware jako správce zabezpečení.

1. V pravém horním rohu kliknutím na **Údržba** otevřete stránku Údržba. 

    ![Konfigurace riskware zachovává](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stránce Údržba klikněte na tlačítko **lidé**.

    ![Riskware osoby konfigurace](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Vyberte kartu **Podrobnosti** a proveďte následující kroky:

    ![Podrobnosti konfigurace riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Vyberte **typ osoby** jako zaměstnanec.

    b. Do textového pole **jméno** a příjmení zadejte jméno uživatele jako **Britta**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

1. Na kartě **zabezpečení** proveďte následující kroky:

    ![Zabezpečení konfigurace riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. V části **ověřování** vyberte režim **ověřování** , který jste nastavili jako konfigurace Azure pro jednotné přihlašování.

    b. V části **Podrobnosti přihlášení** zadejte do TEXTOVÉHO pole **ID uživatele** e-mail jako uživatel `brittasimon@contoso.com` .

    c. Do textového pole **heslo** zadejte heslo uživatele.

1. Na kartě **organizace** proveďte následující kroky:

    ![Organizace konfigurace riskware](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Vyberte možnost jako **Level1** organizace.

    b. V části **primární pracoviště osoby** zadejte do textového pole **umístění** své umístění.

    c. V části **Zaměstnanec** vyberte **stav zaměstnance** jako neformální.

    d. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici riskware, měli byste se automaticky přihlásit k riskware, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
