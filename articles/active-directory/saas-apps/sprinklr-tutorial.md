---
title: 'Kurz: Integrace služby Azure Active Directory se sprinklr | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089646"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Kurz: Integrace služby Azure Active Directory se sprinklrem

V tomto kurzu se dozvíte, jak integrovat Sprinklr s Azure Active Directory (Azure AD).
Integrace Sprinklr s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Sprinklr.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke sprinklr (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí sprinklr, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Sprinklr

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sprinklr podporuje **SP** inicioval SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Přidání Sprinklr z galerie

Chcete-li nakonfigurovat integraci Sprinklr do Azure AD, musíte přidat Sprinklr z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat sprinklr z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Sprinklr**, z panelu výsledků vyberte **Příkazka lr** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Sprinklr v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sprinklr na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Sprinklr.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sprinklr, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Sprinklr](#configure-sprinklr-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte sprinklr test uživatele](#create-sprinklr-test-user)** – mít protějšek Britta Simon v Sprinklr, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí sprinklr, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **Sprinklr** **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Sprinklr Doména a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.sprinklr.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta Sprinklr,](https://www.sprinklr.com/contact-us/) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Sprinklr** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurace jednotného přihlašování sprinklr

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Sprinklr jako správce.

1. Přejděte do **části Nastavení správy \> **.

    ![Správa](./media/sprinklr-tutorial/ic782907.png "Správa")

1. V levém podokně přejděte na **Spravovat \> jednotné přihlášení partnera.**

    ![Správa partnera](./media/sprinklr-tutorial/ic782908.png "Správa partnera")

1. Klikněte **na +Přidat jednotné přihlášení**.

    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782909.png "Jednotné přihlašování")

1. Na stránce **Jednotné přihlášení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782910.png "Jednotné přihlašování")

    a. Do textového pole **Název** zadejte název konfigurace (například *WAADSSOTest*).

    b. Vyberte **Povoleno**.

    c. Vyberte **Použít nový certifikát služby SSO**.

    d. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **Certifikát u poskytovatele identity.**

    e. Vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z portálu Azure Portal, do textového pole **Id entity.**

    f. Vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure Portal, do textového pole **přihlašovací adresy URL zprostředkovatele identity.**

    g. Vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z portálu Azure Portal, do textového pole **URL odhlášení zprostředkovatele identity.**

    h. Jako **typ id uživatele SAML**vyberte **položku Assertion obsahuje uživatelské jméno sprinklr.com uživatele**.

    i. Jako **umístění ID uživatele SAML**je vyberte **ID uživatele v prvku Identifikátor názvu příkazu Subject**.

    j. Klikněte na **Uložit**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu sprinklr.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Sprinklr**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **příkaz Sprinklr**.

    ![Odkaz Sprinklr v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-sprinklr-test-user"></a>Vytvořit testovacího uživatele Sprinklr

1. Přihlaste se k webu společnosti Sprinklr jako správce.

1. Přejděte do **části Nastavení správy \> **.

    ![Správa](./media/sprinklr-tutorial/ic782907.png "Správa")

1. V levém podokně přejděte na **Spravovat \> klientské uživatele.**

    ![Nastavení](./media/sprinklr-tutorial/ic782914.png "Nastavení")

1. Klikněte na **Add User** (Přidat uživatele).

    ![Nastavení](./media/sprinklr-tutorial/ic782915.png "Nastavení")

1. V dialogovém okně **Upravit uživatele** proveďte následující kroky:

    ![Upravit uživatele](./media/sprinklr-tutorial/ic782916.png "Upravit uživatele")

    a. Do textových polí **E-mail**, **Jméno** a **Příjmení** zadejte informace o uživatelském účtu Azure AD, který chcete zřídit.

    b. Vyberte **příkaz Zakázáno heslo**.

    c. Vyberte **jazyk**.

    d. Vyberte **typ uživatele**.

    e. Klikněte na **Aktualizovat**.

    > [!IMPORTANT]
    > **Heslo zakázáno** musí být vybráno, aby se uživatel mohl přihlásit prostřednictvím zprostředkovatele identity. 

1. Přejděte do **role**a proveďte následující kroky:

    ![Role partnerů](./media/sprinklr-tutorial/ic782917.png "Role partnerů")

    a. V **globálním** seznamu vyberte **ALL_Permissions**.  

    b. Klikněte na **Aktualizovat**.

> [!NOTE]
> Můžete použít jakékoli jiné sprinklr nástroje pro vytváření uživatelských účtů nebo rozhraní API poskytované Sprinklr zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Sprinklr na přístupovém panelu, můžete by měl být automaticky přihlášeni k Sprinklr, pro které nastavíte přistupující spo. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
