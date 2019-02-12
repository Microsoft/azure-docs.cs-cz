---
title: 'Kurz: Integrace Azure Active Directory s Druva | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a platformy Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: jeedes
ms.openlocfilehash: 0d0c8f41e1f7de9b2059e9be2c003c5ba44bcfe1
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005569"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Kurz: Integrace Azure Active Directory s Druva

V tomto kurzu se dozvíte, jak integrovat platformy Druva s Azure Active Directory (Azure AD).
Integrace platformy Druva s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Druva.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Druva (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Druva, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Platformy Druva jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje platformy Druva **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-druva-from-the-gallery"></a>Přidání Druva z Galerie

Konfigurace integrace platformy Druva do služby Azure AD, budete muset přidat Druva z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Druva z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **platformy Druva**vyberte **platformy Druva** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Platformy Druva v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí platformy Druva podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Druva.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí platformy Druva, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace platformy Druva Single Sign-On](#configure-druva-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Druva](#create-druva-test-user)**  – Pokud chcete mít protějšek Britta Simon Druva, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí platformy Druva, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **platformy Druva** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    ![Platformy Druva domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor** textové pole, zadejte hodnotu řetězce:  `druva-cloud`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/both-preintegrated-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://cloud.druva.com/home`

6. Aplikace platformy Druva očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název | Zdrojový atribut|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Zadejte token vygenerovanou hodnotu |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. Na **nastavení platformy Druva** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-druva-single-sign-on"></a>Konfigurace platformy Druva jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Druva jako správce.

2. Přejděte na **spravovat \> nastavení**.

    ![Nastavení](./media/druva-tutorial/ic795091.png "nastavení")

3. V dialogovém okně Nastavení jednotného přihlašování proveďte následující kroky:

    ![Jednotné přihlašování – nastavení](./media/druva-tutorial/ic795092.png "jednotné přihlašování – nastavení")
    
    a. V **přihlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.
        
    b. V **odhlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal
        
    c. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **ID zprostředkovatele certifikátu** textové pole
     
    d. Chcete-li otevřít **nastavení** klikněte na **Uložit**.

4. Na **nastavení** klikněte na **vygenerovat Token jednotného přihlašování**.

    ![Nastavení](./media/druva-tutorial/ic795093.png "nastavení")

5. Na **jednotné přihlašování ověřovací Token** dialogového okna, proveďte následující kroky:

    ![Token jednotného přihlašování](./media/druva-tutorial/ic795094.png "Token jednotného přihlašování")
    
    a. Klikněte na tlačítko **kopírování**, vložit zkopírovat hodnotu v **hodnotu** textového pole v **přidat atribut** části webu Azure Portal.
    
    b. Klikněte na **Zavřít**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu platformy Druva.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **platformy Druva**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **platformy Druva**.

    ![Propojení platformy Druva v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-druva-test-user"></a>Vytvoření platformy Druva testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do platformy Druva, musí být poskytnuty do platformy Druva. V případě platformy Druva zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **platformy Druva** společnosti serveru jako správce.

2. Přejděte na **spravovat \> uživatelé**.
   
    ![Správa uživatelů](./media/druva-tutorial/ic795097.png "Správa uživatelů")

3. Klikněte na tlačítko **vytvořit nový**.
   
    ![Správa uživatelů](./media/druva-tutorial/ic795098.png "Správa uživatelů")

4. V dialogovém okně Vytvořit nové uživatele proveďte následující kroky:
   
    ![Create NewUser](./media/druva-tutorial/ic795099.png "Create NewUser")
   
    a. V **e-mailová adresa** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.
   
    b. V **název** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.
   
    c. Klikněte na tlačítko **vytvořit uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné platformy Druva uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Druva zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Druva na přístupovém panelu, můžete by měl být automaticky přihlášeni do platformy Druva, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

