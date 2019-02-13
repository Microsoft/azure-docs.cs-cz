---
title: 'Kurz: Integrace Azure Active Directory s plátna | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a plátna.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecc3ad9fcf1bb1aee9392f0dfcf40807b0edf508
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183765"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Kurz: Integrace Azure Active Directory s plátna

V tomto kurzu se dozvíte, jak integrovat plátna pomocí služby Azure Active Directory (Azure AD).
Integrace plátno s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup na plátno.
* Můžete povolit uživatelům, aby se automaticky přihlášeni na plátno (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s plátno, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Plátno jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Plátno podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-canvas-from-the-gallery"></a>Přidání plátna z Galerie

Konfigurace integrace plátna do služby Azure AD, budete muset přidat plátna z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat plátna z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **plátna**vyberte **plátna** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Plátno v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí plátno, na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské plátně.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s plátno, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace plátna Single Sign-On](#configure-canvas-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele plátna](#create-canvas-test-user)**  – Pokud chcete mít protějšek Britta Simon plátno, na kterém je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s plátno, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **plátna** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Domény pro plátno a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.instructure.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory plátna klienta](https://community.canvaslms.com/community/help) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. V **podpisový certifikát SAML** klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogového okna.

    ![Upravit podpisového certifikátu SAML](common/edit-certificate.png)

6. V **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. Na **nastavení plátna** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-canvas-single-sign-on"></a>Konfigurace plátna jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti plátna jako správce.

2. Přejděte na **kurzy \> spravované účty \> Microsoft**.

    ![Plátno](./media/canvas-lms-tutorial/ic775990.png "plátna")

3. V navigačním podokně na levé straně vyberte **ověřování**a potom klikněte na tlačítko **přidat novou konfiguraci SAML**.

    ![Ověřování](./media/canvas-lms-tutorial/ic775991.png "ověřování")

4. Na stránce aktuální integrace postupujte následovně:

    ![Aktuální integrace](./media/canvas-lms-tutorial/ic775992.png "aktuální integrace")

    a. V **IdP Entity ID** textového pole vložte hodnotu **Azure Ad identifikátor** zkopírovanou z webu Azure portal.

    b. V **protokolu na adresu URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. V **adresy URL odhlašovací** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. V **odkaz změnit heslo** textového pole vložte hodnotu **heslo změnit adresu URL** zkopírovanou z webu Azure portal.

    e. V **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnotu certifikát, který jste zkopírovali z portálu Azure portal.

    f. Z **přihlášení atribut** seznamu vyberte **NameID**.

    g. Z **identifikátor formátu** seznamu vyberte **emailAddress**.

    h. Klikněte na tlačítko **uložit nastavení ověřování**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na plátno.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **plátna**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **plátna**.

    ![Na plátně odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-canvas-test-user"></a>Vytvořit testovacího uživatele plátna

Pokud chcete povolit Azure AD uživatelům přihlášení na plátno, musí být zřízená plátno. V případě plátno zřizování uživatelů je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **plátna** tenanta.

2. Přejděte na **kurzy \> spravované účty \> Microsoft**.

   ![Plátno](./media/canvas-lms-tutorial/ic775990.png "plátna")

3. Klikněte na tlačítko **uživatelé**.

   ![Uživatelé](./media/canvas-lms-tutorial/ic775995.png "uživatelů")

4. Klikněte na tlačítko **přidání nového uživatele**.

   ![Uživatelé](./media/canvas-lms-tutorial/ic775996.png "uživatelů")

5. Na stránce dialogového okna nového uživatele přidat proveďte následující kroky:

   ![Přidání uživatele](./media/canvas-lms-tutorial/ic775997.png "přidat uživatele")

   a. V **jméno a příjmení** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.

   b. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

   c. V **přihlášení** textového pole zadejte uživatele Azure AD e-mailovou adresu jako **brittasimon@contoso.com**.

   d. Vyberte **e-mailu uživatele o vytvoření tohoto účtu**.

   e. Klikněte na tlačítko **přidat uživatele**.

> [!NOTE]
> Můžete použít jakékoli jiné plátna uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných plátna uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici plátno, na přístupovém panelu, vám by měl být automaticky přihlášeni na plátno, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

