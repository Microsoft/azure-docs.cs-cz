---
title: 'Kurz: Integrace Azure Active Directory s Promapp | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 2ddb8777a6470c0e739545e71867a694022d1723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093604"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Kurz: Integrace Azure Active Directory s Promapp

V tomto kurzu se dozvíte, jak integrovat Promapp s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k Promapp Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Promapp (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Promapp, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Promapp předplatné, které obsahuje single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Promapp podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování a zahájené pomocí IdP.

* Promapp podporuje zřizování uživatelů just-in-time.

## <a name="add-promapp-from-the-gallery"></a>Přidání Promapp z Galerie

Nastavení integrace Promapp do služby Azure AD, budete muset přidat Promapp z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Promapp**. Vyberte **Promapp** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s Promapp test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v Promapp.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Promapp, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace Promapp jednotného přihlašování](#configure-promapp-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Promapp, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/), vyberte na stránce Promapp integrace aplikací, **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotné přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno, pokud chcete nakonfigurovat aplikace v režimu zahájené pomocí IdP, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/idp-intiated.png)

    1. V **identifikátor** pole, zadejte adresu URL v tomto vzoru:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Integrace Azure AD s Promapp je nakonfigurován pouze pro ověřování spouštěných službou. (To znamená, že přejdete na adresu URL Promapp zahájí proces ověřování.) Ale **adresy URL odpovědi** pole je povinné pole.

    1. V **adresy URL odpovědi** pole, zadejte adresu URL v tomto vzoru:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **nastavit další adresy URL**. V **přihlašovací adresa URL** pole, zadejte adresu URL v tomto vzoru:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Promapp domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Budete muset použít skutečné identifikátor, adresa URL pro odpověď a adresu URL přihlašování. Obraťte se [tým podpory Promapp](https://www.promapp.com/about-us/contact-us/) k získání hodnoty. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **certifikát (Base64)** , podle požadavků vaší a uložte certifikát v počítači:

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V **nastavení Promapp** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-promapp-single-sign-on"></a>Konfigurace Promapp jednotného přihlašování

1. Přihlaste se k webu společnosti Promapp jako správce.

2. V nabídce v horní části okna vyberte **správce**:
   
    ![Vyberte správce][12]

3. Vyberte **konfigurace**:
   
    ![Vyberte konfiguraci][13]

4. V **zabezpečení** dialogové okno pole, proveďte následující kroky.
   
    ![Dialogové okno zabezpečení][14]
    
    1. Vložit **přihlašovací adresa URL** , který jste zkopírovali z portálu Azure portal do **adresu URL pro jednotné přihlášení** pole.
    
    1. V **jednotným Přihlašováním – Single Sign-on režimu** seznamu vyberte **volitelné**. Vyberte **Uložit**.

       > [!NOTE]
       > Volitelné režim je pouze pro testování. Jakmile budete spokojeni s konfigurací, vyberte **vyžaduje** v **jednotným Přihlašováním – Single Sign-on režimu** seznamu pro všechny uživatele k ověření ve službě Azure AD.

    1. V poznámkovém bloku otevřete certifikát, který jste si stáhli v předchozí části. Zkopírujte obsah certifikát bez první řádek ( **---BEGIN CERTIFICATE---** ) nebo poslední řádek ( **---END CERTIFICATE---** ). Vložení obsahu do certifikátu **certifikát x.509 jednotného přihlašování** a potom vyberte **Uložit**.

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

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k Promapp Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Promapp**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Promapp**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="just-in-time-user-provisioning"></a>Zřizování uživatelů just-in-time

Promapp podporuje zřizování uživatelů just-in-time. Tato funkce je ve výchozím nastavení povolena. Pokud uživatel již neexistuje mezi Promapp, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice Promapp na přístupovém panelu, vám by měl být automaticky přihlášeni Promapp instanci, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
