---
title: 'Kurz: Integrace Azure Active Directory se systémy Pega | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pega systémy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 195e7bf21fe1f6017705883f2ec692c182f15375
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560598"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Integrace Azure Active Directory s Pega systémy

V tomto kurzu se dozvíte, jak integrovat systémy Pega s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k systémům Pega Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k systémům Pega (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před zahájením.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Pega systémy, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Pega systémy, které má single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Systémy Pega podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování a zahájené pomocí IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Přidat Pega systémů z Galerie

Postup nastavení integrace systémů Pega do služby Azure AD, budete muset přidat Pega systémů z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Pega systémy**. Vyberte **Pega systémy** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky vyhledávání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a otestovat Azure AD jednotné přihlašování s Pega systémy pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v systémech Pega.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pega systémy, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace Pega systémy jednotného přihlašování](#configure-pega-systems-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvoření zkušebního uživatele Pega systémy](#create-a-pega-systems-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Pega systémy, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Pega systémy** integrace stránce aplikace vyberte **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno, pokud chcete nakonfigurovat aplikace v režimu zahájené pomocí IdP, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/idp-intiated.png)

    1. V **identifikátor** pole, zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. V **adresy URL odpovědi** pole, zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **nastavit další adresy URL** a proveďte následující kroky.

    ![Pega systémy domény a adresy URL jednotného přihlašování – informace](common/both-advanced-urls.png)

    1. V **přihlašovací adresa URL** zadejte znaménko na hodnotu adresy URL.

    1. V **stav přenosu** pole, zadejte adresu URL v tomto vzoru: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Hodnoty za předpokladu, že tady jsou zástupné symboly. Budete muset použít skutečné identifikátor, adresa URL pro odpověď, přihlaste se na adresu URL a adresy URL relay stavu. Můžete mít identifikátor a odpovědět hodnoty adresy URL z Pega aplikace, jak je popsáno dále v tomto kurzu. K získání hodnoty stavu relay, obraťte se [tým podpory Pega systémy](https://www.pega.com/contact-us). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Systémy Pega aplikace potřebuje kontrolní výrazy SAML v určitém formátu. Aby je ve správném formátu, budete muset přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje výchozí atributy. Vyberte **upravit** ikony otevřete **atributy uživatele** dialogové okno:

    ![Atributy uživatele](common/edit-attribute.png)

7. Kromě atributů, které je znázorněno na předchozím snímku obrazovky systémy Pega aplikace vyžaduje několik další atributy se mají předat zpět jako odpověď SAML. V **deklarace identity uživatelů** část **atributy uživatele** dialogové okno pak dokončete následující kroky a přidejte tyto atributy tokenu SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Tyto hodnoty jsou specifické pro vaši organizaci. Zadejte odpovídající hodnoty.

    1. Vyberte **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogové okno:

    ![Vyberte Přidat novou deklaraci](common/new-save-attribute.png)

    ![Správa dialogové okno deklarace identity uživatele](common/new-attribute-details.png)

    1. V **název** zadejte název atributu, který je zobrazený pro tento řádek.

    1. Nechte **Namespace** prázdné.

    1. Pro **zdroj**vyberte **atribut**.

    1. V **zdrojový atribut** vyberte hodnotu atributu zobrazený pro tento řádek.

    1. Vyberte **OK**.

    1. Vyberte **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **kód XML metadat federace** , podle požadavků vaší a uložte certifikát v počítači:

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. V **nastavení Pega systémy** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurace Pega systémy jednotného přihlašování

1. Ke konfiguraci jednotného přihlašování na **Pega systémy** straně, přihlaste se k portálu Pega pomocí účtu správce v jiném okně prohlížeče.

2. Vyberte **vytvořit** > **SysAdmin** > **ověřovací službu**:

    ![Vyberte službu ověřování](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Proveďte následující kroky na **vytvořit ověřovací službu** obrazovky.

    ![Vytvoření obrazovky pro ověřovací službu](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. V **typ** seznamu vyberte **SAML 2.0**.

    1. V **název** zadejte libovolný název (například **jednotného přihlašování k Azure AD**).

    1. V **krátký popis** zadejte popis.  

    1. Vyberte **vytvořit a otevřít**.
    
4. V **zprostředkovatele Identity (IdP) informace** vyberte **metadat zprostředkovatele identity Import** a přejděte k souboru metadat, který jste si stáhli z webu Azure portal. Klikněte na tlačítko **odeslat** načtení metadat:

    ![Oddíl informací identity zprostředkovatele (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Import se načítají data zprostředkovatele identity, jak je znázorněno zde:

    ![Importovaná data zprostředkovatele identity](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Dokončete následující kroků v **nastavení poskytovatele služeb (SP)** oddílu.

    ![Nastavení poskytovatele služeb](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopírovat **identifikace entit** hodnotu a vložte ho do **identifikátor** pole **základní konfiguraci SAML** části webu Azure Portal.

    1. Kopírovat **Assertion Consumer Service (ACS) umístění** hodnotu a vložte ho do **adresy URL odpovědi** pole **základní konfiguraci SAML** části webu Azure Portal.

    1. Vyberte **zakázat žádost o podepsání**.

7. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon @\<doména_společnosti >.\< Rozšíření >**. (Například BrittaSimon@contoso.com.)

    c. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k systémům Pega Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Pega systémy**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Pega systémy**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-a-pega-systems-test-user"></a>Vytvoření zkušebního uživatele Pega systémy

Dále je třeba vytvořit uživatele s názvem Britta Simon v systémech Pega. Práce s [tým podpory Pega systémy](https://www.pega.com/contact-us) pro vytváření uživatelů.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice Pega systémy na přístupovém panelu, můžete by měl být automaticky přihlášeni k instanci Pega systémy, u kterého nastavíte jednotné přihlašování. Další informace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)