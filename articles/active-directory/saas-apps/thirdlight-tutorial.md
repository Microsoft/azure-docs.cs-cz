---
title: 'Kurz: Integrace služby Azure Active Directory se službou ThirdLight | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 448d46cd21a63488c4f567d5555fe6406fc0fa73
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089093"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Kurz: Integrace služby Azure Active Directory se službou ThirdLight

V tomto kurzu se dozvíte, jak integrovat ThirdLight s Azure Active Directory (Azure AD). Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k ThirdLight.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k ThirdLight (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Pokud se chcete dozvědět víc o integraci aplikací SaaS s Azure AD, přečtěte [si témat jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s ThirdLight, musíte mít:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ThirdLight, které má povoleno jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* ThirdLight podporuje sp-iniciované spřiby.

## <a name="add-thirdlight-from-the-gallery"></a>Přidat ThirdLight z galerie

Chcete-li nastavit integraci ThirdLight do Azure AD, musíte přidat ThirdLight z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít na **podnikové aplikace** > **Všechny aplikace**:

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ThirdLight**. Ve výsledcích hledání vyberte **Třetí světlo** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí thirdlight pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, musíte vytvořit vztah mezi uživatelem Azure AD a odpovídajícím uživatelem v ThirdLight.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí thirdlight, je třeba provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Konfigurace ThirdLight jednotné přihlášení na](#configure-thirdlight-single-sign-on)** straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele testu ThirdLight,](#create-a-thirdlight-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ThirdLight, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací ThirdLight vyberte **Jednotné přihlašování**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **Základní konfigurace SAML** proveďte následující kroky.

    ![Dialogové okno Základní konfigurace SAML](common/sp-identifier.png)

    1. Do pole **Přihlásit se na adresu URL** zadejte adresu URL v tomto vzoru:
    
          `https://<subdomain>.thirdlight.com/`

    1. Do pole **Identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečnou přihlašovací adresu URL a identifikátor. Obraťte se na [tým podpory ThirdLight](https://www.thirdlight.com/support) získat hodnoty. Můžete také odkazovat na vzory zobrazené v dialogovém okně **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** odkaz **Ke stažení** vedle xml metadat **federace**a uložte soubor do počítače:

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit ThirdLight** zkopírujte příslušné adresy URL na základě vašich požadavků:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurace jednotného přihlašování ThirdLight

1. V novém okně webového prohlížeče se přihlaste na web společnosti ThirdLight jako správce.

1. Přejít na **správu konfiguračního** > **systému** > **SAML2**:

    ![Správa systému](./media/thirdlight-tutorial/ic805843.png "Správa systému")

1. V části konfigurace SAML2 postupujte podle následujících kroků.
  
    ![Oddíl konfigurace SAML2](./media/thirdlight-tutorial/ic805844.png "Oddíl konfigurace SAML2")

    1. Vyberte **povolit jednotné přihlašování SAML2**.

    1. V části **Zdroj metadat idp**vyberte **načíst metadata idp z XML**.

    1. Otevřete soubor metadat, který jste stáhli z webu Azure Portal v předchozí části. Zkopírujte obsah souboru a vložte jej do pole **XML metadat idp.**

    1. Vyberte **Uložit nastavení SAML2**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části okna vyberte **Nového uživatele:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **BrittaSimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení tím, že jí přístup k ThirdLight.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ThirdLight**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ThirdLight**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a v dolní části okna klikněte na tlačítko **Vybrat.**

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-thirdlight-test-user"></a>Vytvoření testovacího uživatele ThirdLight

Chcete-li povolit uživatelům Azure AD k přihlášení k ThirdLight, je nutné je přidat do ThirdLight. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, postupujte takto:

1. Přihlaste se na své stránky společnosti ThirdLight jako správce.

1. Přejděte na kartu **Uživatelé.**

1. Vyberte **možnost Uživatelé a skupiny**.

1. Vyberte **Přidat nového uživatele**.

1. Zadejte uživatelské jméno, jméno nebo popis a e-mailovou adresu platného účtu Azure AD, který chcete zřídit. Zvolte přednastavení nebo skupinu nových členů.

1. Vyberte **Vytvořit**.

> [!NOTE]
> Můžete použít libovolný nástroj pro vytváření uživatelských účtů nebo rozhraní API poskytované ThirdLight zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici ThirdLight na přístupovém panelu, měli byste být automaticky přihlášeni k instanci ThirdLight, pro kterou nastavíte přispojené přispojené k zemi. Další informace o přístupovém panelu najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
