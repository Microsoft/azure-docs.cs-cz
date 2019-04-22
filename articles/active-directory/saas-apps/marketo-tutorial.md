---
title: 'Kurz: Integrace Azure Active Directory s Marketo | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 09f452a0971e2a0e74e51edd2db44eecda39c204
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265759"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Integrace Azure Active Directory s platformy Marketo

V tomto kurzu se dozvíte, jak integrovat služby Marketo se službou Azure Active Directory (Azure AD).
Integrace platformy Marketo s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k službě Marketo.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k službě Marketo (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí služby Marketo, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Marketo jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje platformy Marketo **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-marketo-from-the-gallery"></a>Přidání služby Marketo z Galerie

Pokud chcete nakonfigurovat integraci platformy Marketo do služby Azure AD, budete muset přidat Marketo z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání služby Marketo z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Marketo**vyberte **Marketo** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Marketo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotného přihlašování pomocí služby Marketo podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské služby marketo.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby Marketo, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace služby Marketo Single Sign-On](#configure-marketo-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Marketo](#create-marketo-test-user)**  – Pokud chcete mít protějšek Britta Simon služby marketo, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí služby Marketo, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Marketo** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Marketo domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://saml.marketo.com/sp`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory klientu Marketo](http://investors.marketo.com/contactus.cfm) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Marketo** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-marketo-single-sign-on"></a>Konfigurace služby Marketo jednotné přihlašování

1. K získání Id Munchkin vaší aplikace, přihlaste se k Marketo pomocí přihlašovacích údajů správce a proveďte následující akce:
   
    a. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na tlačítko **Munchkin odkaz**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Zkopírujte Id Munchkin uvedené na obrazovce a dokončení vaší adresy URL odpovědi v Průvodci konfigurací služby Azure AD.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Pokud chcete nakonfigurovat jednotné přihlašování v aplikaci, postupujte následujících kroků:
   
    a. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na tlačítko **Single Sign On**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Chcete-li povolit nastavení SAML, klikněte na tlačítko **upravit** tlačítko.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Povolené** nastavení jednotného přihlašování.
   
    f. Vložit **Azure AD identifikátor**v **ID vystavitele** textového pole.
   
    g. V **Entity ID** textového pole zadejte adresu URL jako `http://saml.marketo.com/sp`.
   
    h. Vyberte umístění, ID uživatele jako **identifikátor názvu elementu**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Pokud není uživatelský identifikátor hodnotu hlavního názvu uživatele, pak změňte hodnotu atributu kartě.
   
    i. Nahrajte certifikát, který jste si stáhli z Průvodce konfigurací služby Azure AD. **Uložit** nastavení.
   
    j. Upravte nastavení přesměrování stránky.
   
    k. Vložit **přihlašovací adresa URL** v **přihlašovací adresa URL** textového pole.
   
    l. Vložit **odhlašovací adresa URL** v **odhlašovací adresa URL** textového pole.
   
    m. V **chybová adresa URL**, kopie vašeho **adresu URL instance služby Marketo** a klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Pokud chcete povolit jednotné přihlašování pro uživatele, proveďte následující akce:
   
    a. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte **zabezpečení** nabídky a klikněte na tlačítko **nastavení přihlášení**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Zkontrolujte **vyžadovat jednotné přihlašování** možnost a **Uložit** nastavení.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon\@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k službě Marketo.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Marketo**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Marketo**.

    ![Propojení platformy Marketo v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-marketo-test-user"></a>Vytvoření služby Marketo testovacího uživatele

V této části vytvořte uživatele Britta Simon služby marketo. postupujte podle těchto kroků a vytvořte uživatele v platformy Marketo.

1. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.

2. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Přejděte **zabezpečení** nabídky a klikněte na tlačítko **uživatelů a rolí**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klikněte na tlačítko **pozvat nového uživatele** odkaz na kartě Uživatelé
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. V Průvodci pozvat nového uživatele zadejte následující informace
   
    a. Zadejte uživatele **e-mailu** adresu do textového pole
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Zadejte **křestní jméno** do textového pole
   
    c. Zadejte **příjmení** do textového pole
   
    d. Klikněte na **Další**

6. V **oprávnění** kartu, vyberte **userRoles** a klikněte na tlačítko **další**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klikněte na tlačítko **odeslat** tlačítko si pošlete e-mailové pozvánce uživatele
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Uživatel obdrží e-mailové oznámení a klikněte na odkaz a změnit heslo k aktivaci účtu. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby Marketo na přístupovém panelu, vám by měl být automaticky přihlášeni k službě Marketo, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

