---
title: 'Kurz: Integrace s Azure Active Directory pomocí modulu snap-in Správce přístupu k Internetu Zscalerem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem Internetu přístup správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.openlocfilehash: 088fc2b7aeb318481a81387770f1dcef2883241d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064096"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Kurz: Integrace s Azure Active Directory pomocí modulu snap-in Správce přístupu k Internetu Zscaler

V tomto kurzu se dozvíte, jak integrovat Zscalerem Internetu přístup správce Azure Active Directory (Azure AD).
Správce přístupu k Internetu Zscalerem integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscalerem Internetu přístup správce.
* Uživatelům se automaticky přihlášeni k Zscalerem Internetu přístup správce (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem Internetu přístup správce, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné správce přístup k Internetu Zscaler

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje správce přístupu k Internetu Zscalerem **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Přidání správce přístupu k Internetu Zscalerem z Galerie

Pokud chcete nakonfigurovat integrace správce přístup Internet Zscalerem do služby Azure AD, potřebujete přidat Zscalerem Internetu přístup správce z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat správce přístupu k Internetu Zscalerem z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem Internetu přístup správce**vyberte **Zscalerem Internetu přístup správce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Správce přístupu k Internetu Zscalerem v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s Zscaler Internet přístup správce na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem Internetu přístup správce.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem Internetu přístup správce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zscalerem Internetu přístup správce jednotného přihlašování](#configure-zscaler-internet-access-administrator-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele Zscalerem Internetu přístup správce](#create-zscaler-internet-access-administrator-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zscalerem Internetu přístup správce, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem Internetu přístup správce, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Zscalerem Internetu přístup správce** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![Zscalerem Internetu přístup správce domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL podle vašich požadavků:
    
    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` | 
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL podle vašich požadavků:
    
    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` | 
    | `https://admin.zscalertwo.net/adminsso.do` | 
    | `https://admin.zscalerthree.net/adminsso.do` | 
    | `https://admin.zscloud.net/adminsso.do` | 
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Správce přístupu k Internetu Zscalerem aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Na **sadě si jednotné přihlašování pomocí SAML, stránce**, klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![Odkaz atributu](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název  | Zdrojový atribut  |
    | ---------| ------------ |
    | Role     | user.assignedroles |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. Z **zdrojový atribut** seznamu selelct hodnotu atributu.

    c. Klikněte na tlačítko **OK**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Po klepnutí na [tady](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) vědět, jak nakonfigurovat Role ve službě Azure AD

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastaven se Zscalerem Internetu přístup správce** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Konfigurovat Internet Zscalerem přístup správce jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k vaší Zscalerem Internetu přístup k uživatelskému rozhraní správce.

2. Přejděte na **Správa > Správa správců** a proveďte následující kroky a klikněte na Uložit:
   
    ![Správa](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "správy")

    a. Zkontrolujte **povolit ověřování SAML**.

    b. Klikněte na tlačítko **nahrát**, k nahrání podpisového certifikátu Azure SAML, který jste si stáhli z webu Azure portal v **veřejný certifikát SSL**.
    
    c. Volitelně můžete přidat za účelem zvýšení zabezpečení, **vystavitele** podrobnosti ověření vystavitele odpověď SAML.

3. V Uživatelském rozhraní správce proveďte následující kroky:

    ![Správa](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Najeďte myší **aktivace** nabídky vlevo dole.

    b. Klikněte na tlačítko **aktivovat**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem Internetu přístup správce.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zscalerem Internetu přístup správce**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zscalerem Internetu přístup správce**.

    ![Správce přístupu k Internetu Zscalerem odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Vytvořit testovacího uživatele Zscalerem Internetu přístup správce

Cílem této části je vytvořte uživatele Britta Simon v Zscalerem Internetu přístup správce. Přístup k Internetu Zscalerem nepodporuje Just-In-Time zřizování pro jednotné přihlašování pro správce. Musíte ručně vytvořit účet správce.
Pokyny o tom, jak vytvořit účet správce najdete v dokumentaci Zscalerem:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem Internetu přístup správce na přístupovém panelu, vám by měl být automaticky přihlášeni k Zscalerem Internetu přístup k uživatelskému rozhraní správce u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

