---
title: 'Kurz: Integrace Azure Active Directory s ScreenSteps | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 570f789d0f399c5ffa7535101136ab65ba58ccd5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277081"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Kurz: Integrace Azure Active Directory s ScreenSteps

V tomto kurzu se dozvíte, jak integrovat ScreenSteps s Azure Active Directory (Azure AD).
ScreenSteps integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ScreenSteps.
* Můžete povolit uživatelům být automaticky přihlášeni k ScreenSteps (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ScreenSteps, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje ScreenSteps **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-screensteps-from-the-gallery"></a>Přidání ScreenSteps z Galerie

Konfigurace integrace ScreenSteps do služby Azure AD, budete muset přidat ScreenSteps z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ScreenSteps z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ScreenSteps**vyberte **ScreenSteps** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ScreenSteps v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí ScreenSteps podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ScreenSteps.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ScreenSteps, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ScreenSteps Single Sign-On](#configure-screensteps-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ScreenSteps](#create-screensteps-test-user)**  – Pokud chcete mít protějšek Britta Simon ScreenSteps, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ScreenSteps, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **ScreenSteps** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ScreenSteps domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení ScreenSteps** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-screensteps-single-sign-on"></a>Konfigurace ScreenSteps jednotné přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti ScreenSteps.

1. Klikněte na tlačítko **nastavení účtu**.

    ![Správa účtů](./media/screensteps-tutorial/ic778523.png "Správa účtů")

1. Klikněte na tlačítko **jednotného přihlašování**.

    ![Ověřování vzdálené](./media/screensteps-tutorial/ic778524.png "vzdáleného ověřování")

1. Klikněte na tlačítko **vytvořit jeden koncový bod přihlašování –**.

    ![Ověřování vzdálené](./media/screensteps-tutorial/ic778525.png "vzdáleného ověřování")

1. V **vytvořit jeden koncový bod přihlašování –** části, proveďte následující kroky:

    ![Vytvořit koncový bod ověřování](./media/screensteps-tutorial/ic778526.png "vytvořit koncový bod ověřování")

    a. V **název** textového pole zadejte název.

    b. Z **režimu** seznamu vyberte **SAML**.

    c. Klikněte na možnost **Vytvořit**.

1. **Upravit** nový koncový bod.

    ![Upravit koncový bod](./media/screensteps-tutorial/ic778528.png "upravit koncový bod")

1. V **upravit jeden koncový bod přihlašování –** části, proveďte následující kroky:

    ![Koncový bod vzdáleného ověřování](./media/screensteps-tutorial/ic778527.png "koncový bod vzdáleného ověřování")

    a. Klikněte na tlačítko **nahrát nový certifikát SAML soubor**a pak nahrajte certifikát, který jste si stáhli z webu Azure portal.

    b. Vložit **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal do **Vzdálená adresa URL pro přihlášení** textového pole.

    c. Vložit **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal do **URL odhlašovací stránky** textového pole.

    d. Vyberte **skupiny** přiřazovat uživatele k kdy byly povolené.

    e. Klikněte na tlačítko **aktualizace**.

    f. Kopírovat **adresu URL příjemce SAML** do schránky a vložte do **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** části webu Azure Portal.

    g. Vraťte se **upravit jeden koncový bod přihlašování –**.

    h. Klikněte na tlačítko **nastavit jako výchozí pro účet** tlačítko použít tento koncový bod pro všechny uživatele, kteří se přihlásí do ScreenSteps. Případně můžete kliknout **přidat do lokality** tlačítko použít tento koncový bod pro určité weby v **ScreenSteps**.

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

V této části je povolit Britta Simon k udělení přístupu k ScreenSteps použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ScreenSteps**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ScreenSteps**.

    ![Odkaz ScreenSteps v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-screensteps-test-user"></a>Vytvoření ScreenSteps testovacího uživatele

V této části vytvoříte uživatele v ScreenSteps jako Britta Simon. Práce s [tým podpory ScreenSteps klienta](https://www.screensteps.com/contact) přidat uživatele na platformě ScreenSteps. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ScreenSteps na přístupovém panelu, můžete by měl být automaticky přihlášeni k ScreenSteps, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)