---
title: 'Kurz: Integrace služby Azure Active Directory pomocí funkce Proxyclick | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093496"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Kurz: Integrace služby Azure Active Directory pomocí funkce Proxyclick

V tomto kurzu se dozvíte, jak integrovat proxyclick s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k Proxyclick.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k proxyclick (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí proxyclick, musíte mít:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete se zaregistrovat na [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Proxyclick předplatné, které má jednotné přihlášení povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* Proxyclick podporuje SP-inicioval a IdP-inicioval sso.

## <a name="add-proxyclick-from-the-gallery"></a>Přidat Proxyclick z galerie

Chcete-li nastavit integraci Proxyklikněte do Azure AD, musíte přidat Proxyclick z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít na **podnikové aplikace** > **Všechny aplikace**:

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Příkaz Proxyclick**. Ve výsledcích hledání **vyberte Příkaz Proxyclick** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí funkce Proxyclick pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, musíte vytvořit vztah mezi uživatelem Azure AD a odpovídajícím uživatelem v proxyclick.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí proxyclick, je třeba provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Konfigurace proxyclick jednotné přihlášení na](#configure-proxyclick-single-sign-on)** straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele testu Proxyclick,](#create-a-proxyclick-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí proxyclicku, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací Proxyclick vyberte **Jednotné přihlašování**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. Chcete-li aplikaci konfigurovat v režimu iniciovaném protokolem IdP, proveďte následující kroky v dialogovém okně **Základní konfigurace SAML.**

    ![Dialogové okno Základní konfigurace SAML](common/idp-intiated.png)

    1. Do pole **Identifikátor** zadejte adresu URL v tomto vzoru:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Do pole **Adresa URL pro odpověď** zadejte adresu URL v tomto vzoru:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Pokud chcete aplikaci nakonfigurovat v režimu iniciovaném službou SP, vyberte **nastavit další adresy URL**. Do pole **Přihlásit se na adresu URL** zadejte adresu URL v tomto vzoru:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick Doména a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi a přihlašovací adresu URL. Kroky pro získání těchto hodnot jsou popsány dále v tomto kurzu.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** odkaz **Ke stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit proxyclick** zkopírujte příslušné adresy URL na základě vašich požadavků:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurace jednotného přihlášení pomocí příkazu Proxyclick

1. V novém okně webového prohlížeče se přihlaste na web společnosti Proxyclick jako správce.

2. Vyberte **nastavení & účtu**:

    ![Vybrat nastavení & účtu](./media/proxyclick-tutorial/configure1.png)

3. Přejděte dolů do části **Integrace** a vyberte **možnost SAML**:

    ![Vybrat SAML](./media/proxyclick-tutorial/configure2.png)

4. V části **SAML** postupujte takto.

    ![Sekce SAML](./media/proxyclick-tutorial/configure3.png)

    1. Zkopírujte hodnotu **adresy URL příjemce SAML** a vložte ji do pole **Adresa URL odpovědi** v dialogovém okně Základní **konfigurace SAML** na webu Azure Portal.

    1. Zkopírujte hodnotu adresy URL přesměrování při **správě SAML** a vložte ji do polí **Přihlásit se na adresu URL** a **Identifikátor** v dialogovém okně Základní **konfigurace SAML** na webu Azure Portal.

    1. V seznamu **Metod požadavků SAML** vyberte **možnost Přesměrování HTTP**.

    1. Do pole **Vystavitel** vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.

    1. Do pole **ADRESA URL koncového bodu SAML 2.0** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    1. V poznámkovém bloku otevřete soubor certifikátu, který jste stáhli z webu Azure Portal. Vložte obsah tohoto souboru do pole **Certifikát.**

    1. Vyberte **Uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **BrittaSimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení tím, že jí přístup k Proxyclick.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Proxyclick**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Příkaz Proxyclick**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a v dolní části okna klikněte na tlačítko **Vybrat.**

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-proxyclick-test-user"></a>Vytvoření testovacího uživatele proxyclick

Chcete-li povolit uživatelům Azure AD k přihlášení k Proxyclick, je nutné je přidat do Proxyclick. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, postupujte takto:

1. Přihlaste se na web společnosti Proxyclick jako správce.

1. V horní části okna vyberte **Kolegové:**

    ![Vybrat kolegy](./media/proxyclick-tutorial/user1.png)

1. Vyberte **Přidat kolegu**:

    ![Vybrat Přidat kolegu](./media/proxyclick-tutorial/user2.png)

1. V části **Přidat kolegu** postupujte podle následujících kroků.

    ![Přidání oddílu kolega](./media/proxyclick-tutorial/user3.png)

    1. Do pole **E-mail** zadejte e-mailovou adresu uživatele. V tomto případě **brittasimon\@contoso.com**.

    1. Do pole **Jméno** zadejte křestní jméno uživatele. V tomto **případě, Britta**.

    1. Do pole **Příjmení** zadejte příjmení uživatele. V tomto případě, **Simon**.

    1. Vyberte **Přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici Proxyclick na přístupovém panelu, měli byste být automaticky přihlášeni k instanci Proxyclick, pro kterou nastavíte přispojené přispojené k zámese. Další informace o přístupovém panelu najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

