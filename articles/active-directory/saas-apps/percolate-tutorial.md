---
title: 'Kurz: Integrace Azure Active Directory s provede se perkolace | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a provede se perkolace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094600"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Kurz: Integrace Azure Active Directory s provede se perkolace

V tomto kurzu se dozvíte, jak integrovat provede se perkolace s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k provede se perkolace Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k provede se perkolace (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před zahájením.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s provede se perkolace, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Provede se perkolace předplatné, které má single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Provede se perkolace podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování a zahájené pomocí IdP.

## <a name="add-percolate-from-the-gallery"></a>Přidání provede se perkolace z Galerie

Pokud chcete nakonfigurovat integraci provede se perkolace do služby Azure AD, musíte doplnit provede se perkolace z Galerie váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **provede se perkolace**. Vyberte **provede se perkolace** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s provede se perkolace test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v provede se perkolace.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s provede se perkolace, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Provede se perkolace jednotné přihlašování konfigurovat](#configure-percolate-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvoření zkušebního uživatele provede se perkolace](#create-a-percolate-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s provede se perkolace, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **provede se perkolace** integrace stránce aplikace vyberte **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotné přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno, nemusíte provádět žádnou akci ke konfiguraci aplikace v režimu zahájené pomocí IdP. Aplikace je již integrovaná s Azure.

    ![Provede se perkolace domény a adresy URL jednotné přihlašování – informace](common/preintegrated.png)

5. Pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **nastavit další adresy URL** a v **přihlašovací adresa URL** zadejte **https://percolate.com/app/login** :

   ![Provede se perkolace domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)
6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **kopírování** ikonu zkopírujte **adresa Url federačních metadat aplikace** . Tuto adresu URL si uložte.

    ![Zkopírujte adresu URL federačních metadat aplikace](common/copy-metadataurl.png)

7. V **nastavení provede se perkolace** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurace provede se perkolace jednotného přihlašování

1. V novém okně webového prohlížeče Přihlaste se k provede se perkolace jako správce.

2. Na levé straně domovské stránky vyberte **nastavení**:
    
    ![Vyberte nastavení](./media/percolate-tutorial/configure01.png)

3. V levém podokně vyberte **jednotného přihlašování** pod **organizace**:

    ![Vyberte jednotné přihlašování v rámci organizace](./media/percolate-tutorial/configure02.png)

    1. V **přihlašovací adresa URL** pole, vložte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V **Entity ID** pole, vložte **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal. Zkopírujte jeho obsah a vložte ho do **x509 certifikáty** pole.

    1. V **atribut e-mailové** zadejte **emailaddress**.

    1. **Adresa URL metadat zprostředkovatele Identity** pole je volitelné pole. Pokud jste si zkopírovali **adresa Url federačních metadat aplikace** z portálu Azure portal, můžete ho vložit do tohoto pole.

    1. V **AuthNRequests by měl být podepsané?** seznamu vyberte **ne**.

    1. V **jednotného přihlašování k povolení automatického zřizování** seznamu vyberte **ne**.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >** . (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat Azure AD jednotného přihlašování tak, že udělíte přístup k provede se perkolace Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **provede se perkolace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **provede se perkolace**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Vyberte uživatele a skupiny](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-a-percolate-test-user"></a>Vytvoření zkušebního uživatele provede se perkolace

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k provede se perkolace, budete muset přidat je do provede se perkolace. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte tyto kroky:

1. Přihlaste se k provede se perkolace jako správce.

2. V levém podokně vyberte **uživatelé** pod **organizace**. Vyberte **noví uživatelé**:

    ![Vyberte nového uživatele](./media/percolate-tutorial/configure03.png)

3. Na **vytvořit uživatele** stránce, proveďte následující kroky.

    ![Vytvoření stránky uživatelé](./media/percolate-tutorial/configure04.png)

    1. V **e-mailu** zadejte e-mailovou adresu uživatele. Například, brittasimon@contoso.com.

    1. V **jméno a příjmení** zadejte jméno uživatele. Například **Brittasimon**.

    1. Vyberte **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice provede se perkolace na přístupovém panelu, vám by měl být automaticky přihlášeni provede se perkolace instanci, u kterého nastavíte jednotné přihlašování. Další informace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)