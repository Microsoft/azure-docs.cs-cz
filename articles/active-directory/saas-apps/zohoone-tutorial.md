---
title: 'Kurz: Integrace Azure Active Directory s Zoho jeden | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086229"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Kurz: Integrace Azure Active Directory s Zoho jeden

V tomto kurzu se dozvíte, jak integrovat Zoho jeden s Azure Active Directory (Azure AD).
Zoho jeden integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k jednomu Zoho.
* Uživatelům se automaticky přihlášeni k jedné Zoho (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jeden Zoho, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Zoho jeden jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Zoho jeden podporuje **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-zoho-one-from-the-gallery"></a>Přidání Zoho jeden z Galerie

Konfigurace integrace Zoho jeden do služby Azure AD, budete muset přidat Zoho jeden z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zoho jeden z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zoho jeden**vyberte **Zoho jeden** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Zoho jeden v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Zoho jeden podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zoho jeden.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednoho Zoho, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zoho jeden Single Sign-On](#configure-zoho-one-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte jeden Zoho testovacího uživatele](#create-zoho-one-test-user)**  – Pokud chcete mít protějšek Britta Simon Zoho jeden, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí jednoho Zoho, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Zoho jeden** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Zoho jedné domény a adresy URL jednotného přihlašování – informace](common/idp-relay.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `one.zoho.com`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Předchozí **adresy URL odpovědi** není skutečnou hodnotu. Zobrazí se `<saml-identifier>` hodnotu z step4 # z **konfigurace Zoho jeden Single Sign-On** oddílu, který je vysvětlen později v tomto kurzu.

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **stav přenosu** textové pole, zadejte adresu URL: `https://one.zoho.com`

5. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, postupujte následovně:


    ![Zoho jedné domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Předchozí **přihlašovací adresa URL** není skutečnou hodnotu. Hodnota bude aktualizován skutečné přihlašovací adresu URL z **konfigurace Zoho jeden Single Sign-On** oddílu, který je vysvětlen později v tomto kurzu. 

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavit jeden Zoho** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurace Zoho jedné jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu Zoho jeden společnosti jako správce.

2. Na **organizace** kartu, klikněte na tlačítko **nastavení** pod **ověřování SAML**.

    ![Zoho jedné organizaci](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na stránce rozbalovací proveďte následující kroky:

    ![Zoho jeden podpis](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. V **odhlašování URL** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. Klikněte na tlačítko **Procházet** k nahrání **certifikát (Base64)** který jste si stáhli z webu Azure portal.

    d. Klikněte na **Uložit**.

4. Po uložení nastavení ověřování SAML, zkopírujte **identifikátor SAML** hodnotu a s příponou **adresy URL odpovědi** místo `<saml-identifier>`, třeba `https://accounts.zoho.com/samlresponse/one.zoho.com` a vložte vygenerovanou hodnotu v **Adresy URL odpovědi** textového pole pod **základní konfiguraci SAML** oddílu.

    ![Zoho jeden saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Přejděte **domén** kartu a potom klikněte na tlačítko **přidat doménu**.

    ![Zoho jednu doménu](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na **přidat doménu** stránce, proveďte následující kroky:

    ![Zoho jeden přidat doménu](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. V **název domény** textové pole, typ domény třeba contoso.com.

    b. Klikněte na tlačítko **Add** (Přidat).

    >[!Note]
    >Po přidání domény postupujte [tyto](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroky k ověření vaší domény. Po ověření domény použít název domény v **přihlašovací adresa URL** v **základní konfiguraci SAML** části webu Azure Portal.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k jednomu Zoho.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zoho jeden**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zoho jeden**.

    ![Zoho jedno propojení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zoho-one-test-user"></a>Vytvořte jeden Zoho testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k jednomu Zoho, musí být poskytnuty do Zoho jeden. V jedné Zoho zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Zoho jeden jako správce zabezpečení.

2. Na **uživatelé** kartu, klepněte na **uživatele logo**.

    ![Zoho jeden uživatel](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na **přidat uživatele** stránce, proveďte následující kroky:

    ![Zoho jeden přidat uživatele](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. V **název** textové pole, zadejte jméno uživatele, jako je **Britta simon**.
    
    b. V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je brittasimon@contoso.com.

    >[!Note]
    >Vyberte ověřenou doménu v seznamu domény.

    c. Klikněte na tlačítko **Add** (Přidat).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Zoho jednu dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni Zoho k jednomu u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

