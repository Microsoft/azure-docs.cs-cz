---
title: 'Kurz: Integrace Azure Active Directory s ThirdLight | Dokumentace Microsoftu'
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089093"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Kurz: Integrace Azure Active Directory s ThirdLight

V tomto kurzu se dozvíte, jak integrovat ThirdLight s Azure Active Directory (Azure AD). Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k ThirdLight Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k ThirdLight (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Pokud chcete získat další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ThirdLight, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ThirdLight předplatné, které obsahuje single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* ThirdLight podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-thirdlight-from-the-gallery"></a>Přidání ThirdLight z Galerie

Nastavení integrace ThirdLight do služby Azure AD, budete muset přidat ThirdLight z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ThirdLight**. Vyberte **ThirdLight** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s ThirdLight test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v ThirdLight.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ThirdLight, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace ThirdLight jednotného přihlašování](#configure-thirdlight-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvoření zkušebního uživatele ThirdLight](#create-a-thirdlight-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ThirdLight, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/), vyberte na stránce ThirdLight integrace aplikací, **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotné přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/sp-identifier.png)

    1. V **přihlašovací adresa URL** pole, zadejte adresu URL v tomto vzoru:
    
          `https://<subdomain>.thirdlight.com/`

    1. V **identifikátor (Entity ID)** pole, zadejte adresu URL v tomto vzoru:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Tyto hodnoty jsou zástupné symboly. Budete muset použít skutečné přihlašovací adresu URL a identifikátor. Obraťte se [tým podpory ThirdLight](https://www.thirdlight.com/support) k získání hodnoty. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **kód XML metadat federace** , podle požadavků vaší a uložte soubor ve vašem počítači:

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V **nastavení ThirdLight** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurace ThirdLight jednotného přihlašování

1. V novém okně webového prohlížeče Přihlaste se k webu společnosti ThirdLight jako správce.

1. Přejděte na **konfigurace** > **Správa systému** > **SAML2**:

    ![Správa systému](./media/thirdlight-tutorial/ic805843.png "Správa systému")

1. V konfiguračním oddílu typu SAML2 proveďte následující kroky.
  
    ![Oddíl konfigurace typu SAML2](./media/thirdlight-tutorial/ic805844.png "SAML2 konfiguračního oddílu")

    1. Vyberte **povolit SAML2 Single Sign-On**.

    1. V části **zdroj metadat zprostředkovatele identity**vyberte **metadat zprostředkovatele identity načíst ze souboru XML**.

    1. Otevřete soubor metadat, který jste si stáhli z webu Azure portal v předchozí části. Obsah souboru zkopírujte a vložte ho do **kód XML metadat zprostředkovatele identity** pole.

    1. Vyberte **SAML2 uložit nastavení**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části okna:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >** . (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k ThirdLight Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ThirdLight**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ThirdLight**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-a-thirdlight-test-user"></a>Vytvoření zkušebního uživatele ThirdLight

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k ThirdLight, budete muset přidat je do ThirdLight. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte tyto kroky:

1. Přihlaste se k webu společnosti ThirdLight jako správce.

1. Přejděte **uživatelé** kartu.

1. Vyberte **uživatelů a skupin**.

1. Vyberte **přidání nového uživatele**.

1. Zadejte uživatelské jméno, název nebo popis a e-mailovou adresu platnou Azure AD účtů, které chcete zřídit. Vyberte předvolbu nebo skupiny nové členy.

1. Vyberte **Vytvořit**.

> [!NOTE]
> Můžete použít libovolný nástroj pro vytváření účtu uživatele nebo rozhraní API poskytovaných ThirdLight zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice ThirdLight na přístupovém panelu, vám by měl být automaticky přihlášeni ThirdLight instanci, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
