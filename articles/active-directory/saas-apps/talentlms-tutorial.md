---
title: 'Kurz: Integrace služby Azure Active Directory s TalentLMS | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233369"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Kurz: Integrace Azure Active Directory s TalentLMS

V tomto kurzu se dozvíte, jak integrovat TalentLMS s Azure Active Directory (Azure AD).
Integrace TalentLMS s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TalentLMS.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k TalentLMS (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s TalentLMS, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením TalentLMS

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TalentLMS podporuje **sp** inicioval sso

## <a name="adding-talentlms-from-the-gallery"></a>Přidání TalentLMS z galerie

Chcete-li nakonfigurovat integraci TalentLMS do Azure AD, musíte přidat TalentLMS z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat talentLMS z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TalentLMS**, z panelu výsledků vyberte **TalentLMS** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![TalentLMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TalentLMS na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v TalentLMS.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TalentLMS, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování TalentLMS](#configure-talentlms-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu TalentLMS](#create-talentlms-test-user)** – chcete-li mít protějšek Britta Simon v TalentLMS, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí talentlms, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **TalentLMS** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotlivých přihlašovacích údajích o doméně a adresách URL talentlms](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.TalentLMSapp.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta TalentLMS,](https://www.talentlms.com/contact) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **thumbprint** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit TalentLMS** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-talentlms-single-sign-on"></a>Konfigurace jednotného přihlášení talentlms

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti TalentLMS jako správce.

1. V části **Nastavení & účtu** klikněte na kartu **Uživatelé.**

    ![Nastavení & účtu](./media/talentlms-tutorial/IC777296.png "Nastavení & účtu")

1. Klikněte **na jednotné přihlašování (SSO)**,

1. V části Jednotné přihlašování proveďte následující kroky:

    ![Jednotné přihlašování](./media/talentlms-tutorial/IC777297.png "Jednotné přihlašování")

    a. Ze seznamu **typů integrace s přisychávacím sítě** vyberte možnost **SAML 2.0**.

    b. V textovém poli **zprostředkovatele identity (IDP)** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z webu Azure Portal.

    c. Vložte hodnotu **Kryptografický otisk** z portálu Azure do textového pole **otisků prstů certifikátu.**

    d.  V textovém poli Adresa **URL vzdáleného přihlášení** vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z webu Azure Portal.

    e. V textovém poli Adresa **URL vzdáleného odhlášení** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    f. Zadejte tyto informace:

    * Do textového pole **Cílené ID** zadejte`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Do textového pole **Křestní jméno** zadejte`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Do textového pole **Příjmení** zadejte`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Do textového pole **E-mail** zadejte`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k TalentLMS.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **TalentLMS**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TalentLMS**.

    ![Odkaz TalentLMS v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-talentlms-test-user"></a>Vytvořit testovacího uživatele TalentLMS

Aby se uživatelé Azure AD mohli přihlásit k TalentLMS, musí být zřízeni do TalentLMS. V případě TalentLMS zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **TalentLMS.**

1. Klepněte na tlačítko **Uživatelé**a potom klepněte na tlačítko **Přidat uživatele**.

1. Na stránce Dialogové okno **Přidat uživatele** proveďte následující kroky:

    ![Přidání uživatele](./media/talentlms-tutorial/IC777299.png "Přidání uživatele")  

    a. Do textového pole **Jméno** zadejte křestní jméno uživatele, jako **je Britta**.

    b. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.
 
    c. Do textového pole **E-mailová** adresa `brittasimon\@contoso.com`zadejte e-mail uživatele jako .

    d. Klikněte na **Add User** (Přidat uživatele).

> [!NOTE]
> Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů TalentLMS nebo rozhraní API poskytované TalentLMS zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici TalentLMS na přístupovém panelu, můžete by měl být automaticky přihlášeni k TalentLMS, pro které nastavíte přivytváření připojovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

