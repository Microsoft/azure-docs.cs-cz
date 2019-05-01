---
title: 'Kurz: Integrace Azure Active Directory s EverBridge | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 886cfc59ed41e25c8c3953888690f58e4cc4c252
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695333"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Kurz: Integrace Azure Active Directory s EverBridge

V tomto kurzu se dozvíte, jak integrovat EverBridge s Azure Active Directory (Azure AD).
EverBridge integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k EverBridge.
* Můžete povolit uživatelům být automaticky přihlášeni k EverBridge (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s EverBridge, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* EverBridge jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje EverBridge **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-everbridge-from-the-gallery"></a>Přidání EverBridge z Galerie

Konfigurace integrace EverBridge do služby Azure AD, budete muset přidat EverBridge z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat EverBridge z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **EverBridge**vyberte **EverBridge** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![EverBridge v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí EverBridge podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v EverBridge.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s EverBridge, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace EverBridge jako EverBridge Manager Portal Single Sign-On](#configure-everbridge-as-everbridge-manager-portal-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Konfigurace EverBridge jako EverBridge Manager Portal Single Sign-On](#configure-everbridge-as-everbridge-member-portal-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
4. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Vytvořit testovacího uživatele EverBridge](#create-everbridge-test-user)**  – Pokud chcete mít protějšek Britta Simon EverBridge, který je propojený s Azure AD reprezentace uživatele.
7. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s EverBridge, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **EverBridge** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

    >[!NOTE]
    >Je potřeba konfigurace aplikace jako portál správce nebo jako člen portál na obou koncích tedy na webu Azure Portal a Everbridge portálu.

4. Ke konfiguraci **EverBridge** aplikace jako **EverBridge Manager Portal**na **základní konfiguraci SAML** části proveďte následující kroky:

    ![EverBridge domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://sso.everbridge.net/<API_Name>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory EverBridge](mailto:support@everbridge.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Ke konfiguraci **EverBridge** aplikace jako **EverBridge člen portál**na **základní konfiguraci SAML** části, proveďte následující kroky:

   * Pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![EverBridge domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    * V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    * V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

* Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![EverBridge domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

    * V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlášení na adrese URL. Kontakt [tým podpory EverBridge](mailto:support@everbridge.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavení EverBridge** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurace EverBridge jako EverBridge Manager Portal jednotné přihlašování

1. Chcete-li získat jednotné přihlašování nakonfigurované pro **EverBridge** jako **EverBridge Manager Portal** aplikace, proveďte následující kroky: 
 
2. V okně jiné webové prohlížeče Přihlaste se k EverBridge jako správce.

3. V nabídce v horní části klikněte **nastavení** kartě a vyberte **Single Sign-On** pod **zabezpečení**.
   
     ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. V **název** textového pole zadejte název zprostředkovatele identifikátor (například: název vaší společnosti).
   
     b. V **název rozhraní API** textového pole zadejte název rozhraní API.
   
     c. Klikněte na tlačítko **zvolit soubor** tlačítko Nahrát soubor metadat, který jste si stáhli z webu Azure portal.
   
     d. V umístění Identity SAML, vyberte **identita je v elementu NameIdentifier příkazu subjektu**.
   
     e. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
   
     f. Poskytovatele inicioval žádost o vazbu služby, vyberte **přesměrování protokolu HTTP**.

     g. Klikněte na **Uložit**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurace EverBridge jako člen EverBridge portálu jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **EverBridge** jako **EverBridge člen portál**, je nutné odeslat na stažený **kód XML metadat federace** k [Everbridge tým podpory](mailto:support@everbridge.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon k udělení přístupu k EverBridge použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **EverBridge**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **EverBridge**.

    ![Odkaz EverBridge v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-everbridge-test-user"></a>Vytvoření EverBridge testovacího uživatele

V této části vytvoříte uživatele v Everbridge jako Britta Simon. Práce s [tým podpory EverBridge](mailto:support@everbridge.com) přidat uživatele na platformě Everbridge. Uživatelé musí vytvořit a aktivovat v EverBridge, než použití jednotného přihlašování. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici EverBridge na přístupovém panelu, můžete by měl být automaticky přihlášeni k EverBridge, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

