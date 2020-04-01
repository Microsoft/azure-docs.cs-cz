---
title: 'Kurz: Integrace služby Azure Active Directory s Peakonem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Peakonem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094724"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Kurz: Integrace Azure Active Directory s Peakonem

V tomto kurzu se dozvíte, jak integrovat Peakon s Azure Active Directory (Azure AD).
Integrace Peakonu s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Peakon.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Peakon (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Peakon, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Peakon

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Peakon podporuje **sp** a **IDP** inicioval sso

## <a name="adding-peakon-from-the-gallery"></a>Přidání Peakonz galerie

Chcete-li nakonfigurovat integraci Peakonu do Azure AD, musíte přidat Peakon z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Peakon z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Peakon**, z panelu výsledků vyberte **Peakon** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Peakon v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Peakonu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve Službě Peakon.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Peakon, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace funkce Peakon Single Sign-On](#configure-peakon-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte test Peakon uživatele](#create-peakon-test-user)** – mít protějšek Britta Simon v Peakon, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Peakon, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Peakon** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Informace o jednotném přihlášení domény Peakon a adres URL](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://app.peakon.com/saml/<companyid>/metadata`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://app.peakon.com/saml/<companyid>/assert`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Informace o jednotném přihlášení domény Peakon a adres URL](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://app.peakon.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi, která je vysvětlena dále v kurzu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Nezpracovaný)** z daných možností a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

7. V části **Nastavit Peakon** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-peakon-single-sign-on"></a>Konfigurace jednotného přihlašování Peakon

1. V jiném okně webového prohlížeče se přihlaste ke službě Peakon jako správce.

2. Na řádku nabídek na levé straně stránky klepněte na **položku Konfigurace**a potom přejděte na **položku Integrace**.

    ![Konfigurace](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na stránce **Integrace** klikněte na **Jednotné přihlašování**.

    ![Jednotný](./media/peakon-tutorial/tutorial_peakon_single.png)

4. V části **Jednotné přihlašování** klikněte na **Povolit**.

    ![Povolení](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. V části **Jednotné přihlašování pro zaměstnance používající saml** proveďte následující kroky:

    ![Saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. V textovém poli **adresa URL přihlášení k přihlašovacímu zabezpečení** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z portálu Azure.

    b. V textovém poli **url odhlášení od s vždyznatého zabezpečení** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z portálu Azure.

    c. Kliknutím **na Vybrat soubor** nahrajete certifikát, který jste stáhli z webu Azure Portal, do pole Certifikát.

    d. Kliknutím na **ikonu** **zkopírujete ID entity** a vložte do textového pole **Identifikátor** v části **Základní konfigurace SAML** na webu Azure Portal.

    e. Kliknutím na **ikonu** zkopírujete **adresu URL odpovědi (ACS)** a vložte do textového pole **Adresa URL odpovědi** v části Základní **konfigurace SAML** na webu Azure Portal.

    f. Klikněte na **Uložit.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu peakon.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Peakon**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Peakon**.

    ![Odkaz Peakon v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-peakon-test-user"></a>Vytvořit uživatele testu Peakon

Pro povolení uživatelů Azure AD k přihlášení ke Peakonu, musí být zřízena do Peakon.  
V případě Peakon zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se na svůj web společnosti Peakon jako správce.

2. Na řádku nabídek na levé straně stránky klepněte na **položku Konfigurace**a potom přejděte na **položku Zaměstnanci**.

    ![Zaměstnanec](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. V pravé horní části stránky klikněte na **Přidat zaměstnance**.

      ![Přidejte zaměstnance](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na stránce Dialogové okno **Nový zaměstnanec** proveďte následující kroky:

     ![Nový zaměstnanec](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Do textového pole **Název** zadejte křestní jméno jako **Britta** a příjmení jako **simon**.

    b. Do textového pole **E-mail** zadejte e-mailovou adresu, jako je **\@Brittasimon contoso.com**.

    c. Klepněte na **tlačítko Vytvořit zaměstnance**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Peakon na přístupovém panelu, můžete by měl být automaticky přihlášeni k Peakon, u kterého nastavíte přisuzušující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

