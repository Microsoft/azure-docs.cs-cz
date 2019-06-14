---
title: 'Kurz: Integrace Azure Active Directory s UserEcho | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 59d61eda7002fe46cf99fac63822b2333b2d64b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087769"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Kurz: Integrace Azure Active Directory s UserEcho

V tomto kurzu se dozvíte, jak integrovat UserEcho s Azure Active Directory (Azure AD).
UserEcho integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k UserEcho.
* Můžete povolit uživatelům být automaticky přihlášeni k UserEcho (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s UserEcho, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* UserEcho jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje UserEcho **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-userecho-from-the-gallery"></a>Přidání UserEcho z Galerie

Konfigurace integrace UserEcho do služby Azure AD, budete muset přidat UserEcho z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat UserEcho z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **UserEcho**vyberte **UserEcho** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![UserEcho v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí UserEcho podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v UserEcho.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserEcho, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace UserEcho Single Sign-On](#configure-userecho-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele UserEcho](#create-userecho-test-user)**  – Pokud chcete mít protějšek Britta Simon UserEcho, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s UserEcho, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **UserEcho** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![UserEcho domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.userecho.com/`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory UserEcho klienta](https://feedback.userecho.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení UserEcho** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-userecho-single-sign-on"></a>Konfigurace UserEcho jednotné přihlašování

1. V jiném okně prohlížeče Přihlaste se k webu společnosti UserEcho jako správce.

2. Na panelu nástrojů v horní části klikněte na své uživatelské jméno, rozbalte nabídku a pak klikněte na tlačítko **nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klikněte na tlačítko **integrace**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klikněte na tlačítko **webu**a potom klikněte na tlačítko **jednotné přihlašování (SAML2)** .
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na **jednotné přihlašování (SAML)** stránce, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Jako **povoleno SAML**vyberte **Ano**.
    
    b. Vložit **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **adresu URL jednotného přihlašování SAML** textového pole.
    
    c. Vložit **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **vzdálené adresy URL odhlašovací** textového pole.
    
    d. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát X.509** textového pole.
    
    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k UserEcho použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **UserEcho**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **UserEcho**.

    ![Odkaz UserEcho v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-userecho-test-user"></a>Vytvoření UserEcho testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v UserEcho.

**Vytvořte uživatele v UserEcho jako Britta Simon, proveďte následující kroky:**

1. Přihlašování k webu společnosti UserEcho jako správce.

2. Na panelu nástrojů v horní části klikněte na své uživatelské jméno, rozbalte nabídku a pak klikněte na tlačítko **nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klikněte na tlačítko **uživatelé**, rozbalte **uživatelé** oddílu.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klikněte na tlačítko **uživatelé**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klikněte na tlačítko **pozvat nového uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Na **pozvat nového uživatele** dialogového okna, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. V **název** textového pole zadejte jméno uživatele, jako Britta Simon.
    
    b.  V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.
    
    c. Klikněte na tlačítko **pozvat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici UserEcho na přístupovém panelu, můžete by měl být automaticky přihlášeni k UserEcho, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

