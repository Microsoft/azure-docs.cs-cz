---
title: 'Kurz: Integrace Azure Active Directory s CloudPassage | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: 0e6ffb7f797746fed30520c1c99130ff585909a9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697855"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Kurz: Integrace Azure Active Directory s CloudPassage

V tomto kurzu se dozvíte, jak integrovat CloudPassage s Azure Active Directory (Azure AD).
CloudPassage integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k CloudPassage.
* Můžete povolit uživatelům být automaticky přihlášeni k CloudPassage (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s CloudPassage, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* CloudPassage jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje CloudPassage **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-cloudpassage-from-the-gallery"></a>Přidání CloudPassage z Galerie

Konfigurace integrace CloudPassage do služby Azure AD, budete muset přidat CloudPassage z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat CloudPassage z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **CloudPassage**vyberte **CloudPassage** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![CloudPassage v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí CloudPassage podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v CloudPassage.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s CloudPassage, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace CloudPassage Single Sign-On](#configure-cloudpassage-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele CloudPassage](#create-cloudpassage-test-user)**  – Pokud chcete mít protějšek Britta Simon CloudPassage, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s CloudPassage, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **CloudPassage** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![CloudPassage domény a adresy URL jednotného přihlašování – informace](common/sp-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://portal.cloudpassage.com/saml/init/accountid`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://portal.cloudpassage.com/saml/consume/accountid`. Kliknutím můžete získat hodnotu pro tento atribut **nastavení jednotného přihlašování k dokumentaci** v **nastavení jednotného přihlašování** části CloudPassage portálu.

    ![Konfigurace jednotného přihlašování](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a adresy URL odpovědi. Kontakt [tým podpory CloudPassage klienta](https://www.cloudpassage.com/company/contact/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. CloudPassage aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | jméno |user.givenname |
    | Příjmení |user.surname |
    | e-mail |user.mail |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení CloudPassage** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-cloudpassage-single-sign-on"></a>Konfigurace CloudPassage jednotné přihlašování

1. V jiném okně prohlížeče přihlašování k webu společnosti CloudPassage jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **Správa webu**. 
   
    ![Konfigurace jednotného přihlašování][12]

1. Klikněte na tlačítko **nastavení ověřování** kartu. 
   
    ![Konfigurace jednotného přihlašování][13]

1. V **nastavení jednotného přihlašování** části, proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování][14]

    a. Vyberte **povolit jednotné sign-on(SSO) (dokumentace instalace jednotné přihlašování)** zaškrtávací políčko.
    
    b. Vložit **Azure Ad identifikátor** do **URL vystavitele SAML** textového pole.
  
    c. Vložit **přihlašovací adresa URL** do **adresu URL koncového bodu SAML** textového pole.
  
    d. Vložit **odhlašovací adresa URL** do **cílová stránka odhlášení** textového pole.
  
    e. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah staženého certifikátu do schránky a vložte jej do **x 509 certifikát** textového pole.
  
    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k CloudPassage použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **CloudPassage**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **CloudPassage**.

    ![Odkaz CloudPassage v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-cloudpassage-test-user"></a>Vytvoření CloudPassage testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v CloudPassage.

**Vytvořte uživatele v CloudPassage jako Britta Simon, proveďte následující kroky:**

1. Přihlášení k vaší **CloudPassage** společnosti serveru jako správce. 

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **Správa webu**. 
   
    ![Vytvoření zkušebního uživatele CloudPassage][22] 

1. Klikněte na tlačítko **uživatelé** kartu a potom klikněte na tlačítko **Add New User**. 
   
    ![Vytvoření zkušebního uživatele CloudPassage][23]

1. V **Add New User** části, proveďte následující kroky: 
   
    ![Vytvoření zkušebního uživatele CloudPassage][24]
    
    a. V **křestní jméno** textového pole zadejte Britta. 
  
    b. V **příjmení** textového pole zadejte Simon.
  
    c. V **uživatelské jméno** textového pole **e-mailu** textového pole a **znovu zadejte e-mailu** textového pole zadejte Haniny uživatelské jméno ve službě Azure AD.
  
    d. Jako **typ přístupu**vyberte **povolit přístup z portálu Haló**.
  
    e. Klikněte na tlačítko **Add** (Přidat).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici CloudPassage na přístupovém panelu, můžete by měl být automaticky přihlášeni k CloudPassage, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

