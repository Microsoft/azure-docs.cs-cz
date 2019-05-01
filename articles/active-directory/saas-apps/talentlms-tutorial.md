---
title: 'Kurz: Integrace Azure Active Directory s TalentLMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c17c055b79b8b90bd56a1dcd6472cf74d2512137
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922320"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Kurz: Integrace Azure Active Directory s TalentLMS

V tomto kurzu se dozvíte, jak integrovat TalentLMS s Azure Active Directory (Azure AD).
TalentLMS integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TalentLMS.
* Můžete povolit uživatelům být automaticky přihlášeni k TalentLMS (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TalentLMS, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* TalentLMS jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje TalentLMS **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-talentlms-from-the-gallery"></a>Přidání TalentLMS z Galerie

Konfigurace integrace TalentLMS do služby Azure AD, budete muset přidat TalentLMS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TalentLMS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TalentLMS**vyberte **TalentLMS** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![TalentLMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí TalentLMS podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TalentLMS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TalentLMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace TalentLMS Single Sign-On](#configure-talentlms-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele TalentLMS](#create-talentlms-test-user)**  – Pokud chcete mít protějšek Britta Simon TalentLMS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TalentLMS, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TalentLMS** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![TalentLMS domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.TalentLMSapp.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory TalentLMS klienta](https://www.talentlms.com/contact) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. V **podpisový certifikát SAML** klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogového okna.

    ![Upravit podpisového certifikátu SAML](common/edit-certificate.png)

6. V **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. Na **nastavení TalentLMS** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-talentlms-single-sign-on"></a>Konfigurace TalentLMS jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti TalentLMS jako správce.

1. V **účet a nastavení** klikněte na tlačítko **uživatelé** kartu.

    ![Účet a nastavení](./media/talentlms-tutorial/IC777296.png "účet a nastavení")

1. Klikněte na tlačítko **jednotné přihlašování (SSO)**,

1. V části jednotného přihlašování proveďte následující kroky:

    ![Jednotné přihlašování](./media/talentlms-tutorial/IC777297.png "jednotného přihlašování")

    a. Z **typ integrace jednotného přihlašování** seznamu vyberte **SAML 2.0**.

    b. V **zprostředkovatele Identity (IDP)** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    c. Vložit **kryptografický otisk** hodnotu z webu Azure portal do **otisku certifikátu** textového pole.

    d.  V **přihlašovací adresa URL vzdáleného úložiště** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. V **Vzdálená adresa URL pro odhlášení** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    f. Vyplňte následující údaje:

    * V **TargetedID** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * V **křestní jméno** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * V **příjmení** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k TalentLMS použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TalentLMS**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TalentLMS**.

    ![Odkaz TalentLMS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-talentlms-test-user"></a>Vytvoření TalentLMS testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k TalentLMS, musí být poskytnuty do TalentLMS. V případě TalentLMS zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **TalentLMS** tenanta.

1. Klikněte na tlačítko **uživatelé**a potom klikněte na tlačítko **přidat uživatele**.

1. Na **přidat uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Přidání uživatele](./media/talentlms-tutorial/IC777299.png "přidat uživatele")  

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.
 
    c. V **e-mailová adresa** textového pole zadejte e-mailu uživatele, jako je `brittasimon\@contoso.com`.

    d. Klikněte na tlačítko **přidat uživatele**.

> [!NOTE]
> Můžete použít jakékoli jiné TalentLMS uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných TalentLMS uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TalentLMS na přístupovém panelu, můžete by měl být automaticky přihlášeni k TalentLMS, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

