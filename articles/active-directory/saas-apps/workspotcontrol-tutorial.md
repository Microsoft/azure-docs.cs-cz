---
title: 'Kurz: Integrace Azure Active Directory s řízením Workspot | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workspot ovládacího prvku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 676a0da5f677bacc7451bf25584180d6e9e10a72
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920309"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Kurz: Integrace Azure Active Directory s řízením Workspot

V tomto kurzu se dozvíte, jak integrovat ovládací prvek Workspot se službou Azure Active Directory (Azure AD).
Ovládací prvek Workspot integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Workspot ovládacího prvku.
* Uživatelům se automaticky přihlášeni k řízení Workspot (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ovládacím prvkem Workspot, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Ovládací prvek Workspot jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje ovládací prvek Workspot **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-workspot-control-from-the-gallery"></a>Přidání ovládacího prvku Workspot z Galerie

Pokud chcete nakonfigurovat integraci Workspot ovládacího prvku do služby Azure AD, budete muset přidat ovládací prvek Workspot z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání ovládacího prvku Workspot z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Workspot řízení**vyberte **Workspot řízení** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Workspot ovládací prvek v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s ovládacím prvkem Workspot podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel v ovládacím prvku Workspot.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ovládacím prvkem Workspot, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Workspot ovládací prvek Single Sign-On](#configure-workspot-control-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření ovládacího prvku Workspot testovacího uživatele](#create-workspot-control-test-user)**  – Pokud chcete mít protějšek Britta Simon Workspot ovládací prvek, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ovládacím prvkem Workspot, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Workspot řízení** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Workspot ovládací prvek domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Workspot ovládací prvek domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory klienta řízení Workspot](mailto:support@workspot.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavit řízení Workspot** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurovat ovládací prvek Workspot jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k řízení Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavení**, přejděte na **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Na **konfiguraci zabezpečení kontrolního výrazu značky jazyka** stránce, proveďte následující kroky:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. V **Entity ID** textového pole vložte hodnotu **Azure Ad identifikátor** který jste zkopírovali z portálu Azure portal.   

    b.In **adresa URL služby Sign-on** textového pole vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    c. V **odhlašovací adresa URL služby** textového pole vložte hodnotu **odhlašovací adresa URL** který jste zkopírovali z portálu Azure portal. 

    d. Klikněte na **soubor aktualizace** tlačítko Nahrát base-64 kódování certifikátu, který jste si stáhli z portálu Azure portal do certifikátu X.509.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k ovládacímu prvku Workspot.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ovládací prvek Workspot**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ovládací prvek Workspot**.

    ![Odkaz Workspot ovládací prvek v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-workspot-control-test-user"></a>Vytvoření ovládacího prvku Workspot testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Workspot ovládací prvek, musí být zřízená do Workspot ovládacího prvku. V ovládacím prvku Workspot zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k řízení Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky klikněte na tlačítko **uživatelé**, přejděte na **přidat uživatele**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na **přidat nového uživatele** stránce, proveďte následující kroky:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je Brittasimon@contoso.com.

    d. Vyberte příslušné role uživatele z **Role** rozevíracího seznamu.

    e. Vyberte příslušné skupiny uživatelů z **skupiny** rozevíracího seznamu.

    f. Klikněte na tlačítko **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Workspot ovládacího prvku na přístupovém panelu, můžete by měl být automaticky přihlášeni do ovládacího prvku Workspot, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

