---
title: 'Kurz: Integrace Azure Active Directory s Work.com | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f9bfe022b4a00c01c3ee1bc243f87ef56817b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384294"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Kurz: Integrace Azure Active Directory s Work.com

V tomto kurzu se dozvíte, jak integrovat Work.com s Azure Active Directory (Azure AD).
Work.com integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Work.com.
* Můžete povolit uživatelům být automaticky přihlášeni k Work.com (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Work.com, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Work.com jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Work.com **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-workcom-from-the-gallery"></a>Přidání Work.com z Galerie

Konfigurace integrace Work.com do služby Azure AD, budete muset přidat Work.com z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Work.com z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Work.com**vyberte **Work.com** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Work.com v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Work.com podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Work.com.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Work.com, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Work.com Single Sign-On](#configure-workcom-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Work.com](#create-workcom-test-user)**  – Pokud chcete mít protějšek Britta Simon Work.com, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

>[!NOTE]
>Pokud chcete nakonfigurovat jednotné přihlašování, budete muset nastavit vlastní název domény Work.com ještě. Musíte definovat alespoň názvu domény, test název domény a nasazení pro celou organizaci.

Ke konfiguraci Azure AD jednotné přihlašování s Work.com, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Work.com** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Work.com domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Work.com klienta](https://help.salesforce.com/articleView?id=000159855&type=3) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Work.com** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-workcom-single-sign-on"></a>Konfigurace Work.com jednotné přihlašování

1. Přihlaste se k tenantovi Work.com jako správce.

2. Přejděte na **nastavení**.
   
    ![Instalační program](./media/work-com-tutorial/ic794108.png "instalační program")

3. V levém navigačním podokně v **spravovat** klikněte na tlačítko **Správa domén** související rozbalíte, a pak klikněte na **Moje doména** otevřete **Moje doména** stránky. 
   
    ![Moje doména](./media/work-com-tutorial/ic767825.png "Moje doména")

4. Pokud chcete ověřit, správně nastavené vaší domény, ujistěte se, že je v "**kroku 4 nasazena u uživatelů**" a zkontrolovat vaše "**má nastavení domény**".
   
    ![Doména uživatele nasazena](./media/work-com-tutorial/ic784377.png "nasadit na uživatele domény")

5. Přihlaste se k vašemu tenantovi Work.com.

6. Přejděte na **nastavení**.
    
    ![Instalační program](./media/work-com-tutorial/ic794108.png "instalační program")

7. Rozbalte **kontrolních mechanismů pro zabezpečení** nabídky a pak klikněte na tlačítko **nastavení jednotného přihlašování**.
    
    ![Jednotné přihlašování – nastavení](./media/work-com-tutorial/ic794113.png "jednotné přihlašování – nastavení")

8. Na **nastavení jednotného přihlašování** dialogového okna stránky, proveďte následující kroky:
    
    ![Povoleno SAML](./media/work-com-tutorial/ic781026.png "povoleno SAML")
    
    a. Vyberte **povoleno SAML**.
    
    b. Klikněte na možnost **Nové**.

9. V **SAML jednotné přihlašování – nastavení** části, proveďte následující kroky:
    
    ![SAML jednotné přihlašování – nastavení](./media/work-com-tutorial/ic794114.png "SAML jednotné přihlašování – nastavení")
    
    a. V **název** textového pole zadejte název pro vaši konfiguraci.  
       
    > [!NOTE]
    > Zadání hodnoty pro **název** se automaticky vyplní **název rozhraní API** textového pole.
    
    b. V **vystavitele** textového pole vložte hodnotu **Azure AD identifikátor** zkopírovanou z webu Azure portal.
    
    c. Chcete-li nahrát na server certifikát stažený z webu Azure portal, klikněte na tlačítko **Procházet**.
    
    d. V **Entity Id** textové pole, typ `https://salesforce-work.com`.
    
    e. Jako **typ Identity SAML**vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**.
    
    f. Jako **umístění Identity SAML**vyberte **identita je v elementu NameIdentfier příkazu subjektu**.
    
    g. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    h. V **odhlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.
    
    i. Jako **poskytovatele inicioval žádost o vazbu služby**vyberte **HTTP Post**.
    
    j. Klikněte na **Uložit**.

10. Na portálu classic Work.com, v levém navigačním podokně klikněte na tlačítko **Správa domén** související rozbalíte, a pak klikněte na **Moje doména** otevřít **Moje doména** stránka. 
    
    ![Moje doména](./media/work-com-tutorial/ic794115.png "Moje doména")

11. Na **Moje doména** stránku, **Branding přihlašovací stránky** klikněte na tlačítko **upravit**.
    
    ![Branding přihlašovací stránky](./media/work-com-tutorial/ic767826.png "Branding přihlašovací stránky")

12. Na **Branding přihlašovací stránky** stránku, **ověřovací službu** části název vaší **nastavení jednotného přihlašování SAML** se zobrazí. Vyberte ho a pak klikněte na tlačítko **Uložit**.
    
    ![Branding přihlašovací stránky](./media/work-com-tutorial/ic784366.png "Branding přihlašovací stránky")

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Work.com použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Work.com**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Work.com**.

    ![Odkaz Work.com v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-workcom-test-user"></a>Vytvoření Work.com testovacího uživatele

Pro uživatele Azure Active Directory bude moct přihlásit musí být poskytnuty k Work.com. V případě Work.com zřizování se ruční úlohy.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k webu společnosti Work.com jako správce.

2. Přejděte na **nastavení**.
   
    ![Instalační program](./media/work-com-tutorial/IC794108.png "instalační program")

3. Přejděte na **spravovat uživatele \> uživatelé**.
   
    ![Správa uživatelů](./media/work-com-tutorial/IC784369.png "Správa uživatelů")

4. Klikněte na tlačítko **nového uživatele**.
   
    ![Všichni uživatelé](./media/work-com-tutorial/IC794117.png "všichni uživatelé")

5. V části uživatel upravovat provedete následující kroky v atributech platný Azure AD účet ke zřízení do související textová pole:
   
    ![Úprava uživatele](./media/work-com-tutorial/ic794118.png "Úprava uživatele")
   
    a. V **křestní jméno** textové pole, typ **křestní jméno** uživatele **Britta**.
    
    b. V **příjmení** textové pole, typ **příjmení** uživatele **Simon**.
    
    c. V **Alias** textové pole, typ **název** uživatele **BrittaS**.
    
    d. V **e-mailu** textové pole, typ **e-mailová adresa** uživatele Brittasimon@contoso.com.
    
    e. V **uživatelské jméno** textové pole, typ uživatelskému jménu uživatele jako Brittasimon@contoso.com.
    
    f. V **Přezdívka** textového pole zadejte **Přezdívka** uživatele **Simon**.
    
    g. Vyberte **Role**, **uživatelskou licenci**, a **profilu**.
    
    h. Klikněte na **Uložit**.  
      
    > [!NOTE]
    > Držitel účtu Azure AD se zobrazí e-mailu včetně odkaz pro potvrzení účtu, pak se změní na aktivní.
    > 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Work.com na přístupovém panelu, můžete by měl být automaticky přihlášeni k Work.com, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

