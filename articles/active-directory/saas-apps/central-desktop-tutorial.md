---
title: 'Kurz: Integrace Azure Active Directory s centrální Desktop | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrální Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa1771366efba82fc00886581dac77295e68f9f7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342146"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Kurz: Integrace Azure Active Directory s centrální Desktop

V tomto kurzu se dozvíte, jak integrovat centrální plochy s Azure Active Directory (Azure AD).
Integrace s Azure AD střed Desktop poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ploše centrální.
* Uživatelům se automaticky přihlášeni k centrální Desktop (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s centrální Desktop, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Centrální klasické pracovní plochy jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Centrální Desktop podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-central-desktop-from-the-gallery"></a>Přidání centrální plochy z Galerie

Ke konfiguraci integrace centrální Desktopu do služby Azure AD, budete muset přidat centrální plochy z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat centrální plochy z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **centrální Desktop**vyberte **centrální Desktop** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Centrální Desktop v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s centrální Desktopu podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské centrální plochy.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí centrální plochy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte centrální Desktopu Single Sign-On](#configure-central-desktop-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele centrální Desktop](#create-central-desktop-test-user)**  – Pokud chcete mít protějšek Britta Simon v centrální Desktopu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s centrální Desktop, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **centrální Desktop** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Centrální Desktopu domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.centraldesktop.com`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory centrální klient pro stolní počítače](https://imeetcentral.com/contact-us) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavit centrální Desktopu** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-central-desktop-single-sign-on"></a>Konfigurace centrálního klasické pracovní plochy jednotného přihlašování

1. Přihlaste se k vaší **centrální Desktop** tenanta.

2. Přejděte na **nastavení**. Vyberte **Upřesnit**a pak vyberte **Single Sign On**.

    ![Instalační program – rozšířená](./media/central-desktop-tutorial/ic769563.png "instalační program – rozšířené")

3. Na **nastavení jednotného přihlašování** stránce, proveďte následující kroky:

    ![Jednotné přihlašování – nastavení](./media/central-desktop-tutorial/ic769564.png "nastavení jednotného přihlašování")

    a. Vyberte **povolit SAML v2 jednotného přihlašování**.

    b. V **adresu URL jednotného přihlašování** pole, vložte **Azure Ad identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **přihlašovací adresu URL pro jednotné přihlašování** pole, vložte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. V **jednotného přihlašování k odhlašovací adresa URL** pole, vložte **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

4. V **metodu ověření podpisu zpráva** části, proveďte následující kroky:

    ![Zpráva metodu ověření podpisu](./media/central-desktop-tutorial/ic769565.png "metodu ověření podpisu zprávy")
    
    a. Vyberte **Certifikát**.

    b. V **certifikát jednotného přihlašování** seznamu vyberte **RSH SHA256**.

    c. Otevřete stažený certifikát v poznámkovém bloku. Potom zkopírujte obsah certifikát a vložte ho do **certifikát jednotného přihlašování** pole.

    d. Vyberte **zobrazení odkazu na vaší přihlašovací stránce SAMLv2**.

    e. Vyberte **aktualizace**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k centrální Desktop.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **centrální Desktopu**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **centrální Desktopu**.

    ![Centrální Desktopu odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-central-desktop-test-user"></a>Vytvořit centrální Desktopu testovacího uživatele

Pro uživatele Azure AD bude moct přihlásit musí být poskytnuty v aplikace centrální klasické pracovní plochy. Tato část popisuje, jak vytvořit uživatelské účty služby Azure AD v centrální Desktopu.

> [!NOTE]
> Ke zřízení uživatelských účtů služby Azure AD, můžete použít jiné nástroje pro tvorbu centrální plochy uživatele účtu nebo rozhraní API, která jsou k dispozici v centrální Desktop.

**Ke zřízení uživatelských účtů do centrální Desktopu:**

1. Přihlaste se k vašemu tenantovi centrální Desktop.

2. Vyberte **lidé** a pak vyberte **přidat interní členy**.

    ![Lidé](./media/central-desktop-tutorial/ic781051.png "osoby")

3. V **e-mailovou adresu nové členy** zadejte účet služby Azure AD, které chcete zřídit a pak vyberte **Další**.

    ![E-mailové adresy nových členů](./media/central-desktop-tutorial/ic781052.png "e-mailové adresy nové členy")

4. Vyberte **přidejte interní členy**.

    ![Přidat vnitřní člena](./media/central-desktop-tutorial/ic781053.png "interní přidat člena")
  
   > [!NOTE]
   > Uživatelé, které přidáte dostanou e-mail, který obsahuje odkaz potvrzení pro aktivaci svých účtů.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici centrální plochy na přístupovém panelu, vám by měl být automaticky přihlášeni centrální desktopu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
