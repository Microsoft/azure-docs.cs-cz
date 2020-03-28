---
title: 'Kurz: Integrace služby Azure Active Directory s teamseerem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a TeamSeerem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3e505e4823db8c9f42bbff216f30326081110dcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088966"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Kurz: Integrace Azure Active Directory s TeamSeerem

V tomto kurzu se dozvíte, jak integrovat TeamSeer s Azure Active Directory (Azure AD).
Integrace TeamSeer s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TeamSeer.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k TeamSeer (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s TeamSeer, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* TeamSeer povolení jednotného přihlášení předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TeamSeer podporuje **SP** inicioval sso

## <a name="adding-teamseer-from-the-gallery"></a>Přidání TeamSeer z galerie

Chcete-li nakonfigurovat integraci TeamSeer do Azure AD, musíte přidat TeamSeer z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat TeamSeer z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TeamSeer**, vyberte **TeamSeer** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![TeamSeer v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TeamSeer na základě testovacího uživatele s názvem **Britta Simon**.
Pro jednotné přihlašování do práce, propojení vztah mezi uživatelem Azure AD a související uživatel v TeamSeer musí být vytvořena.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TeamSeer, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace TeamSeer jednotné přihlášení](#configure-teamseer-single-sign-on)** - ke konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit TeamSeer testovací ho uživatele](#create-teamseer-test-user)** – mít protějšek Britta Simon v TeamSeer, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí TeamSeer, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **TeamSeer** **možnost Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![TeamSeer Domény a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory klienta TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit TeamSeer** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-teamseer-single-sign-on"></a>Konfigurace jednotného přihlašování teamseeru

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti TeamSeer jako správce.

1. Přejděte na **správce lidských zdrojů**.

    ![Správce lidských zdrojů](./media/teamseer-tutorial/ic789634.png "Správce lidských zdrojů")

1. Klepněte na **položku Nastavení**.

    ![Nastavení](./media/teamseer-tutorial/ic789635.png "Nastavení")

1. Klepněte na tlačítko **Nastavit podrobnosti o zprostředkovateli SAML**.

    ![Nastavení SAML](./media/teamseer-tutorial/ic789636.png "Nastavení SAML")

1. V části podrobnosti o zprostředkovateli SAML proveďte následující kroky:

    ![Nastavení SAML](./media/teamseer-tutorial/ic789637.png "Nastavení SAML")

    a. Do textového pole **URL** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    b. Otevřete certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **Veřejného certifikátu IdP.**

1. Chcete-li dokončit konfiguraci zprostředkovatele SAML, proveďte následující kroky:

    ![Nastavení SAML](./media/teamseer-tutorial/ic789638.png "Nastavení SAML")

    a. Do **pole Testovat e-mailové adresy**zadejte e-mailovou adresu testovacího uživatele.
  
    b. Do textového pole **Vystavitele** zadejte adresu URL vystavitele poskytovatele služeb.
  
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
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu TeamSeer.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **TeamSeer**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TeamSeer**.

    ![Odkaz TeamSeer v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-teamseer-test-user"></a>Vytvořit uživatele testu TeamSeer

Chcete-li povolit uživatelům Azure AD k přihlášení k TeamSeer, musí být zřízena do ShiftPlanning. V případě TeamSeer zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti **TeamSeer** jako správce.

1. Přejděte na **položku Uživatelé správce \> lidských zdrojů** a klepněte na tlačítko Spustit průvodce Novým **uživatelem**.

    ![Správce lidských zdrojů](./media/teamseer-tutorial/ic789640.png "Správce lidských zdrojů")

1. V části **Podrobnosti o uživateli** proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/teamseer-tutorial/ic789641.png "Podrobnosti o uživateli")

    a. Zadejte **jméno**, **příjmení**, uživatelské **jméno (e-mailovou adresu)** platného účtu Azure AD, který chcete zřídit do souvisejících textových polí.
  
    b. Klikněte na **Další**.

1. Postupujte podle pokynů na obrazovce pro přidání nového uživatele a klepněte na tlačítko **Dokončit**.

> [!NOTE]
> Můžete použít všechny ostatní Nástroje pro vytváření uživatelských účtů TeamSeer nebo rozhraní API poskytované TeamSeer zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici TeamSeer na přístupovém panelu, můžete by měl být automaticky přihlášeni k TeamSeer, u kterého nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
