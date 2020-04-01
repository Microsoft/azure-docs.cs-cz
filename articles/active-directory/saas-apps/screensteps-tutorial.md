---
title: 'Kurz: Integrace služby Azure Active Directory s ScreenSteps | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091667"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Kurz: Integrace služby Azure Active Directory s ScreenSteps

V tomto kurzu se dozvíte, jak integrovat ScreenSteps s Azure Active Directory (Azure AD).
Integrace ScreenSteps s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ScreenSteps.
* Můžete povolit uživatelům, aby se automaticky přihlásili k ScreenSteps (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s ScreenSteps, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením ScreenSteps

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ScreenSteps podporuje **sp** inicioval sso

## <a name="adding-screensteps-from-the-gallery"></a>Přidání ScreenSteps z galerie

Chcete-li nakonfigurovat integraci ScreenSteps do Azure AD, je potřeba přidat ScreenSteps z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ScreenSteps z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ScreenSteps**, z panelu výsledků vyberte **ScreenSteps** a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![ScreenSteps v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ScreenSteps na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v ScreenSteps.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ScreenSteps, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace ScreenSteps Jednotné přihlašování](#configure-screensteps-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit ScreenSteps test uživatele](#create-screensteps-test-user)** – mít protějšek Britta Simon v ScreenSteps, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí screensteps, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikace **ScreenSteps** **možnost Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![ScreenSteps Informace o jedné přihlašovací službě a adresách URL](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Tato hodnota není skutečná. Aktualizujte tuto hodnotu skutečnou přihlašovací adresou URL, která je vysvětlena dále v tomto kurzu.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit ScreenSteps** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-screensteps-single-sign-on"></a>Konfigurace jednotného přihlašování screensteps

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti ScreenSteps jako správce.

1. Klepněte na **položku Nastavení účtu**.

    ![Správa účtů](./media/screensteps-tutorial/ic778523.png "Správa účtů")

1. Klepněte **na položku Jednotné přihlášení**.

    ![Vzdálené ověřování](./media/screensteps-tutorial/ic778524.png "Vzdálené ověřování")

1. Klepněte **na tlačítko Vytvořit koncový bod jednotného přihlášení**.

    ![Vzdálené ověřování](./media/screensteps-tutorial/ic778525.png "Vzdálené ověřování")

1. V části **Vytvořit koncový bod jednotného přihlášení** proveďte následující kroky:

    ![Vytvoření koncového bodu ověřování](./media/screensteps-tutorial/ic778526.png "Vytvoření koncového bodu ověřování")

    a. Do textového pole **Nadpis** zadejte název.

    b. Ze seznamu **Režim** vyberte **možnost SAML**.

    c. Klikněte na **Vytvořit**.

1. **Upravte** nový koncový bod.

    ![Upravit koncový bod](./media/screensteps-tutorial/ic778528.png "Upravit koncový bod")

1. V části **Upravit koncový bod jednotného přihlášení** proveďte následující kroky:

    ![Koncový bod vzdáleného ověřování](./media/screensteps-tutorial/ic778527.png "Koncový bod vzdáleného ověřování")

    a. Klikněte na **Nahrát nový soubor certifikátu SAML**a potom nahrajte certifikát, který jste stáhli z webu Azure Portal.

    b. Vložte hodnotu **adresy URL přihlášení,** kterou jste zkopírovali z portálu Azure do textového pole **Adresa URL vzdáleného přihlášení.**

    c. Vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z portálu Azure do textového pole **Odhlásit adresu URL.**

    d. Vyberte **skupinu,** ke které chcete přiřadit uživatele při jejich zřízení.

    e. Klikněte na **Aktualizovat**.

    f. Zkopírujte **adresu URL příjemce SAML** do schránky a vložte do textového pole přihlašovací adresy **URL** v části Základní **konfigurace SAML** na webu Azure Portal.

    g. Vraťte se do **koncového bodu pro úpravy jednotného přihlášení**.

    h. Kliknutím na tlačítko **Vytvořit výchozí pro účet** použijte tento koncový bod pro všechny uživatele, kteří se přihlásí do ScreenSteps. Případně můžete klepnutím na tlačítko **Přidat na web** použít tento koncový bod pro konkrétní weby v **ScreenSteps**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu screensteps.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ScreenSteps**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ScreenSteps**.

    ![Odkaz ScreenSteps v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-screensteps-test-user"></a>Vytvořit testovacího uživatele ScreenSteps

V této části vytvoříte uživatele s názvem Britta Simon v ScreenSteps. Spolupracujte s  [týmem podpory klienta ScreenSteps](https://www.screensteps.com/contact)a přidejte uživatele na platformě ScreenSteps. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ScreenSteps na přístupovém panelu, můžete by měl být automaticky přihlášeni k ScreenSteps, pro které nastavíte přistupující stav. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)