---
title: 'Kurz: Integrace Azure Active Directory s Proxyclick | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d37e3cc56b4a80fce9dcae6f87ff626867496007
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578319"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Kurz: Integrace Azure Active Directory s Proxyclick

V tomto kurzu se dozvíte, jak integrovat Proxyclick s Azure Active Directory (Azure AD).
Proxyclick integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Proxyclick.
* Můžete povolit uživatelům být automaticky přihlášeni k Proxyclick (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Proxyclick, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Proxyclick jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Proxyclick **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie

Konfigurace integrace Proxyclick do služby Azure AD, budete muset přidat Proxyclick z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Proxyclick z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Proxyclick**vyberte **Proxyclick** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Proxyclick v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Proxyclick podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Proxyclick.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Proxyclick, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Proxyclick Single Sign-On](#configure-proxyclick-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Proxyclick](#create-proxyclick-test-user)**  – Pokud chcete mít protějšek Britta Simon Proxyclick, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Proxyclick, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Proxyclick** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Proxyclick domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://saml.proxyclick.com/init/<companyId>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://saml.proxyclick.com/consume/<companyId>`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Proxyclick domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení Proxyclick** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurace Proxyclick jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Proxyclick jako správce.

2. Vyberte **účet a nastavení**.

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure1.png)

3. Přejděte dolů k položce **integrace** a vyberte **SAML**.

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure2.png)

4. V **SAML** části, proveďte následující kroky:

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Kopírování **adresu URL příjemce SAML** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    b. Kopírování **přesměrovat adresu URL jednotného přihlašování SAML** hodnotu a vložte ho do **přihlašovací adresa URL** a **identifikátor** textová pole v **základní konfiguraci SAML** oddílu na portálu Azure portal.

    c. Vyberte **metoda požadavku SAML** jako **přesměrování protokolu HTTP**.

    d. V **vystavitele** textového pole vložte hodnotu **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **URL koncového bodu služby SAML 2.0** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanými z webu Azure portal.

    f. Otevřete soubor stažený certifikát z webu Azure portal v programu Poznámkový blok a vložte jej do **certifikát** textového pole.

    g. Klikněte na tlačítko **uložit změny**.

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

V této části je povolit Britta Simon k udělení přístupu k Proxyclick použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Proxyclick**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Proxyclick**.

    ![Odkaz Proxyclick v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-proxyclick-test-user"></a>Vytvoření Proxyclick testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Proxyclick, musí být poskytnuty do Proxyclick. V případě Proxyclick zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k webu společnosti Proxyclick jako správce.

1. Klikněte na tlačítko **kolegy** z horního navigačního panelu.

    ![Přidat zaměstnance](./media/proxyclick-tutorial/user1.png)

1. Klikněte na tlačítko **Přidat kolegy**

    ![Přidat zaměstnance](./media/proxyclick-tutorial/user2.png)

1. V **přidat kolegu** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/proxyclick-tutorial/user3.png)

    a. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele brittasimon@contoso.com.

    b. V **křestní jméno** textového pole Název typu prvního uživatele, jako je Britta.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    d. Klikněte na tlačítko **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Proxyclick na přístupovém panelu, můžete by měl být automaticky přihlášeni k Proxyclick, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

