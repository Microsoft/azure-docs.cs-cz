---
title: 'Kurz: Integrace služby Azure Active Directory s rizikovým nádobím | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a riskwarem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027114"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Kurz: Integrace Azure Active Directory s rizikovým nádobím

V tomto kurzu se dozvíte, jak integrovat Riskware s Azure Active Directory (Azure AD).
Integrace Riskware s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Riskware.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Riskware (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Riskware, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením riskware

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Riskware podporuje **sp** inicioval SSO

## <a name="adding-riskware-from-the-gallery"></a>Přidání riskware z galerie

Chcete-li nakonfigurovat integraci Riskware do Azure AD, musíte přidat Riskware z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat riskware z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Riskware**, z panelu výsledků vyberte **Riskware** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Riskware v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí riskware na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Riskware.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí riskware, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace možnosti Jednotné přihlašování do riskwaru](#configure-riskware-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte test Riskware uživatele](#create-riskware-test-user)** – mít protějšek Britta Simon v Riskware, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí riskwaru, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Riskware** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení k doméně rizikového zboží a adresURL](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:
    
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | Prod| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | Demo| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL:
    
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | Prod| `https://riskcloud.net/prod` |
    | Demo| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Hodnota adresy URL přihlášení není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory klienta Riskware,](mailto:support@pansoftware.com.au) abyste získali hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit riskware** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-riskware-single-sign-on"></a>Konfigurace jednotného přihlašování pomocí služby Riskware

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Riskware jako správce.

1. Vpravo nahoře kliknutím na **Údržba** otevřete stránku údržby.

    ![Konfigurace Riskware udržují](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stránce údržby klepněte na **položku Ověřování**.

    ![Riskware Konfigurace authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na stránce **Konfigurace ověřování** proveďte následující kroky:

    ![Riskware Konfigurace authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Vyberte **Typ** jako **SAML** pro ověřování.

    b. Do textového pole **Kód** zadejte kód jako AZURE_UAT.

    c. Do textového pole **Popis** zadejte popis jako konfigurace AZURE pro přisuzovací služby.

    d. V textovém poli **Jednotné přihlášení na stránce** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    e. V textovém poli **Odhlásit stránku** vložte hodnotu **URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    f. Do textového pole **Pole zaúčtovat formulář** zadejte název pole, který je k dispozici v poli Post Response, které obsahuje saml jako SAMLResponse.

    g. Do textového pole **Název značky identity XML** zadejte atribut, který obsahuje jedinečný identifikátor v odpovědi SAML, jako je NameID.

    h. Otevření stažených **metadat Xml** z portálu Azure v poznámkovém bloku, zkopírování certifikátu ze souboru metadat a vložení do textového pole **Certifikát**

    i. Do textového pole **Url příjemce** vložte hodnotu adresy URL **pro odpověď**, kterou získáte od týmu podpory.

    j. V textovém poli **Vystavitr** vložte hodnotu **Identifikátor**, kterou získáte od týmu podpory.

    > [!Note]
    > Obraťte se na [tým podpory klienta Riskware,](mailto:support@pansoftware.com.au) abyste získali tyto hodnoty

    k. Zaškrtněte **políčko Použít post.**

    l. Zaškrtněte políčko **Použít požadavek SAML.**

    m. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Riskware.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Riskware**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **riskware**.

    ![Odkaz Riskware v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-riskware-test-user"></a>Vytvořit testovacího uživatele Riskware

Aby se uživatelé Azure AD mohli přihlásit k Riskware, musí být zřízeni do Riskware. V Riskware zřizování je ruční úloha.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Riskware jako správce zabezpečení.

1. Vpravo nahoře kliknutím na **Údržba** otevřete stránku údržby. 

    ![Konfigurace riskware udržuje](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stránce údržby klikněte na **Lidé**.

    ![Riskware Konfigurace lidí](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Vyberte kartu **Podrobnosti** a proveďte následující kroky:

    ![Podrobnosti o konfiguraci riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Vyberte **Typ osoby** jako Zaměstnanec.

    b. Do **pole Křestní jméno** zadejte křestní jméno uživatele, jako je **Britta**.

    c. Do pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.

1. Na kartě **Zabezpečení** proveďte následující kroky:

    ![Zabezpečení konfigurace riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. V části **Ověřování** vyberte režim **ověřování,** který máte nastavený jako Konfigurace Azure pro sso.

    b. V části **Podrobnosti o přihlášení** zadejte do textového pole `brittasimon@contoso.com` **ID uživatele** e-mail uživatele jako .

    c. Do textového pole **Heslo** zadejte heslo uživatele.

1. Na kartě **Organizace** proveďte následující kroky:

    ![Riskware Konfigurace org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Vyberte možnost jako organizace **Úrovně 1.**

    b. V části **Primární pracoviště osoby** zadejte do textového pole **Umístění** svou polohu.

    c. V části **Zaměstnanec** vyberte **Stav zaměstnance** jako Casual.

    d. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Riskware na přístupovém panelu, měli byste být automaticky přihlášeni k Riskware, u kterého nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
