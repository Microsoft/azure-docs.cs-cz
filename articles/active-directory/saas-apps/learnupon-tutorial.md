---
title: 'Kurz: Integrace Azure Active Directory s LearnUpon | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a8b894a5b790321c0af8527bf77e07b9923d577
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686764"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Kurz: Integrace Azure Active Directory s LearnUpon

V tomto kurzu se dozvíte, jak integrovat LearnUpon s Azure Active Directory (Azure AD).
LearnUpon integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LearnUpon.
* Můžete povolit uživatelům být automaticky přihlášeni k LearnUpon (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LearnUpon, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* LearnUpon jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.


* Podporuje LearnUpon **IDP** jednotné přihlašování zahájené pomocí

* Podporuje LearnUpon **JIT** zřizování uživatelů


## <a name="adding-learnupon-from-the-gallery"></a>Přidání LearnUpon z Galerie

Konfigurace integrace LearnUpon do služby Azure AD, budete muset přidat LearnUpon z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LearnUpon z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LearnUpon**vyberte **LearnUpon** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![LearnUpon v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí LearnUpon podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LearnUpon.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LearnUpon, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace LearnUpon Single Sign-On](#configure-learnupon-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele LearnUpon](#create-learnupon-test-user)**  – Pokud chcete mít protějšek Britta Simon LearnUpon, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s LearnUpon, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **LearnUpon** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![LearnUpon domény a adresy URL jednotného přihlašování – informace](common/idp-reply.png)

    V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Hodnota není skutečný. Skutečná adresa URL odpovědi zaktualizujte příslušnou hodnotu. Kontakt [tým podpory LearnUpon klienta](https://www.learnupon.com/features/support/) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyhledejte **kryptografický OTISK** – tím se přidají do nastavení LearnUpon SAML.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavení LearnUpon** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-learnupon-single-sign-on"></a>Konfigurace LearnUpon jednotné přihlašování

1. Otevřete jiná instance prohlížeče a přihlaste se do LearnUpon s účtem správce.

1. Klikněte na tlačítko **nastavení** kartu.

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klikněte na tlačítko **jednotného přihlašování – SAML**a potom klikněte na tlačítko **obecné nastavení** ke konfiguraci nastavení SAML.
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. V **obecné nastavení** části, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Vyberte **povolené**.

    b. Vyberte **verze** jako **2.0**.

    c. Vyberte **přeskočit podmínky** jako **ne**.

    d. V **odeslat Token SAML název param** textové pole, typ výše uvedené název parametru požadavku post odeslané na adresu příjemce SAML, který obsahuje kontrolní výraz SAML ověřit a – třeba authenticated **SAMLResponse** .

    e. V **formát názvu identifikátor** textového pole zadejte hodnotu, která označuje, kde ve vaší kontrolní výraz SAML identifikátor uživatele (e-mailovou adresu) se nachází – například `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. V **určit konkrétní umístění poskytovatele** textového pole zadejte hodnotu, která určuje, kde jsou uživatelé nasměrovaní pokud kliknou na vaše nahrané ikonu z obrazovky přihlášení k portálu Azure.
  
    g. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    h. Klikněte na tlačítko **spravovat prstem vytiskne**a pak nahrajte hlas staženého certifikátu.

1. Klikněte na tlačítko **uživatelská nastavení**a pak proveďte následující kroky:

     ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. V **křestní jméno identifikátor formátu** textového pole zadejte hodnotu, která nám sdělí v vaše kontrolní výraz SAML jméno uživatele se nachází – například: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. V **poslední formát názvu identifikátor** textového pole zadejte hodnotu, která nám sdělí v vaše kontrolní výraz SAML příjmení uživatele nachází – například: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

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

V této části je povolit Britta Simon k udělení přístupu k LearnUpon použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **LearnUpon**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LearnUpon**.

    ![Odkaz LearnUpon v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-learnupon-test-user"></a>Vytvoření LearnUpon testovacího uživatele

V této části se vytvoří uživateli Britta Simon v LearnUpon. LearnUpon podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi LearnUpon, vytvoří se nový po ověření. Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici LearnUpon na přístupovém panelu, můžete by měl být automaticky přihlášeni k LearnUpon, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)