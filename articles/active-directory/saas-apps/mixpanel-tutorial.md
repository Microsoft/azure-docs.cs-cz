---
title: 'Kurz: Integrace Azure Active Directory s Mixpanelu | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mixpanelu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: 6aec2e41e4819d7c7241482146d92318ccf8e076
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774360"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Kurz: Integrace Azure Active Directory s Mixpanelu

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Mixpanelu.
Mixpanelu integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup do Mixpanelu.
* Můžete povolit uživatelům, aby se automaticky přihlášeni do Mixpanelu (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Mixpanelu, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Mixpanelu jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Mixpanelu **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-mixpanel-from-the-gallery"></a>Přidání Mixpanelu z Galerie

Konfigurace integrace Mixpanelu do služby Azure AD, budete muset přidat Mixpanelu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Mixpanelu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mixpanelu**vyberte **Mixpanelu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Mixpanelu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s Mixpanelu podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Mixpanelu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mixpanelu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Mixpanelu Single Sign-On](#configure-mixpanel-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Mixpanelu](#create-mixpanel-test-user)**  – Pokud chcete mít protějšek Britta Simon v Mixpanelu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Mixpanelu, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Mixpanelu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Mixpanelu domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://mixpanel.com/login/`

    > [!NOTE]
    > Zaregistrujte se prosím na [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) nastavit přihlašovací údaje a obraťte se [tým podpory Mixpanelu](mailto:support@mixpanel.com) povolit nastavení jednotného přihlašování pro vašeho tenanta. Vaše přihlašovací adresa URL hodnotu lze také získat v případě potřeby váš tým podpory Mixpanelu. 

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Mixpanelu** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-mixpanel-single-sign-on"></a>Konfigurace Mixpanelu jednotné přihlašování

1. V jiném okně prohlížeče přihlašování k aplikaci Mixpanelu jako správce.

2. V dolní části stránky klikněte na daném malém **ozubené kolečko** ikony v levém rohu. 
   
    ![Mixpanelu jednotné přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Klikněte na tlačítko **zabezpečení přístupu k** kartu a potom klikněte na tlačítko **změnit nastavení**.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Na **změnit certifikát** dialogového okna stránky, klikněte na tlačítko **zvolte soubor** nahrát svůj certifikát stažený, a potom klikněte na **Další**.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Ověřování adresy URL do textového pole na **změnit adresu URL svého ověřování** dialogového okna stránky, vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal a potom klikněte na **Další**.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Klikněte na **Done** (Hotovo).

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do Mixpanelu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Mixpanelu**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Mixpanelu**.

    ![Odkaz Mixpanelu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-mixpanel-test-user"></a>Vytvoření Mixpanelu testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Mixpanelu. 

1. Přihlaste se k webu společnosti Mixpanelu jako správce.

2. V dolní části stránky klikněte na malé tlačítko ozubeného kola na levém rohu otevřete **nastavení** okna.

3. Klikněte na tlačítko **týmu** kartu.

4. V **člen týmu** textového pole zadejte Haniny e-mailovou adresu v Azure.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Klikněte na tlačítko **pozvat**. 

> [!Note]
> Uživatel dostane e-mailu k profilu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Mixpanelu na přístupovém panelu, vám by měl být automaticky přihlášeni do Mixpanelu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

