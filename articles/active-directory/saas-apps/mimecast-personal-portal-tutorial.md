---
title: 'Kurz: Integrace Azure Active Directory pomocí portálu osobní Mimecast | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a osobní Mimecast portálu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 24bd6fe29e05f84d78316d0bb3e8ffc0d25157f3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828031"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Kurz: Integrace Azure Active Directory s Mimecast osobní portálu

V tomto kurzu se dozvíte, jak integrovat Mimecast osobní portálu Azure Active Directory (Azure AD).
Integrace s Azure AD Portal osobní Mimecast poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k portálu Mimecast osobní.
* Uživatelům se automaticky přihlášeni k Mimecast osobní portálu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí portálu osobní Mimecast, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Osobní portálu Mimecast jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Mimecast osobní portál podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Přidání portálu osobní Mimecast z Galerie

Konfigurovat integraci portálu osobní Mimecast do služby Azure AD, budete muset přidat portál osobní Mimecast z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat portál osobní Mimecast z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **osobní portál Mimecast**vyberte **osobní portál Mimecast** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Osobní portálu Mimecast v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Mimecast osobní portál na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Mimecast osobní portálu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí osobního portálu Mimecast, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Mimecast osobní portál Single Sign-On](#configure-mimecast-personal-portal-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Mimecast osobní portál](#create-mimecast-personal-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon Mimecast osobní portálu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí portálu osobní Mimecast, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **osobní portál Mimecast** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Mimecast osobní portál domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Poskytuje        | `https://jer-api.mimecast.com/login/saml`|

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | Oblast  |  Hodnota | 
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Spojené státy   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Jižní Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrálie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Poskytuje        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Poskytuje        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Identifikátor skutečné zaktualizujte příslušnou hodnotu. Kontakt [tým podpory osobní klienta portálu Mimecast](https://www.mimecast.com/customer-success/technical-support/) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení portálu osobní Mimecast** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Konfigurace Mimecast osobní portálu jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se ke Mimecast osobní portálu jako správce.

2. Přejděte na **služby \> aplikací**.
   
    ![Aplikace](./media/mimecast-personal-portal-tutorial/ic794998.png "aplikací")

3. Klikněte na tlačítko **ověřování profilů**.
   
    ![Ověřování profilů](./media/mimecast-personal-portal-tutorial/ic794999.png "ověřování profilů")

4. Klikněte na tlačítko **nový profil ověřování**.
   
    ![Nový profil ověřování](./media/mimecast-personal-portal-tutorial/ic795000.png "nový profil ověřování")

5. V **ověřování profilu** části, proveďte následující kroky:
   
    ![Profil ověření](./media/mimecast-personal-portal-tutorial/ic795001.png "profilu ověření systému")
   
    a. V **popis** textového pole zadejte název pro vaši konfiguraci.
   
    b. Vyberte **vynutit ověřování SAML pro osobní Mimecast portál**.
   
    c. Jako **poskytovatele**vyberte **Azure Active Directory**.
   
    d. V **URL vystavitele** textového pole vložte hodnotu **Azure Ad identifikátor**, který jste zkopírovali z portálu Azure portal.
   
    e. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.
   
    f. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    g. Otevřete váš **base-64** kódovaného certifikátu v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **certifikát zprostředkovatele Identity (Metadata)** textového pole.

    h. Vyberte **povolit jednotné přihlašování na**.
   
    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na portál Mimecast osobní.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **osobní portál Mimecast**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **osobní portál Mimecast**.

    ![Osobní portálu Mimecast odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-mimecast-personal-portal-test-user"></a>Vytvořit osobní portál Mimecast testovacího uživatele

Chcete-li povolit Azure AD uživatelům přihlásit do portálu osobní Mimecast, musí být zřízená Mimecast osobní portál. V případě Mimecast osobní portál je zřizování úlohu.

Budete muset zaregistrovat k doméně, než budete moct vytvořit uživatele.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **osobní portál Mimecast** jako správce.

2. Přejděte na **adresáře \> interní**.
   
    ![Adresáře](./media/mimecast-personal-portal-tutorial/ic795003.png "adresáře")

3. Klikněte na tlačítko **zaregistrovat novou doménu**.
   
    ![Register New Domain](./media/mimecast-personal-portal-tutorial/ic795004.png "Register New Domain")

4. Po vytvoření nové domény, klikněte na tlačítko **novou adresu**.
   
    ![Nová adresa](./media/mimecast-personal-portal-tutorial/ic795005.png "novou adresu")

5. V dialogovém okně Nový adresy, proveďte následující kroky platný Azure ke zřízení účtu AD:
   
    ![Uložit](./media/mimecast-personal-portal-tutorial/ic795006.png "uložit")
   
    a. V **e-mailovou adresu** textové pole, typ **e-mailovou adresu** uživatele jako datový typ **BrittaSimon@contoso.com**.
    
    b. V **globální název** textové pole, typ **uživatelské jméno** jako **BrittaSimon**.

    c. V **heslo**, a **potvrzení hesla** textová pole, typ **heslo** uživatele.
   
    b. Klikněte na **Uložit**.

>[!NOTE]
>Další nástroje pro tvorbu účtu uživatele Mimecast osobní portálu nebo rozhraní API poskytovaných Mimecast osobní portálu můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Mimecast osobní portál na přístupovém panelu, by měly být automaticky přihlásíte k portálu osobní Mimecast, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

