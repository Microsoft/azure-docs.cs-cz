---
title: 'Kurz: Integrace s Azure Active Directory s vnímání Spojené státy (Non-UltiPro) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a dojem Spojené státy (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4b7a9409052d8255bbad00b38217bcff030e8620
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277013"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Kurz: Integrace s Azure Active Directory s vnímání Spojené státy (Non-UltiPro)

V tomto kurzu se dozvíte, jak integrovat vnímání Spojené státy (Non-UltiPro) se službou Azure Active Directory (Azure AD).
Integrace vnímání Spojené státy (Non-UltiPro) s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k vnímání Spojené státy (Non-UltiPro).
* Můžete povolit uživatelům, aby se automaticky přihlášeni k vnímání Spojené státy (Non-UltiPro) (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vnímání Spojené státy (Non-UltiPro), potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Vnímání Spojené státy (Non-UltiPro) jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Vnímání Spojené státy (Non-UltiPro) podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Přidání vnímání Spojené státy (Non-UltiPro) z Galerie

Konfigurace integrace nástroje vnímání Spojené státy (Non-UltiPro) do služby Azure AD, musíte přidat vnímání Spojené státy (Non-UltiPro) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat vnímání Spojené státy (Non-UltiPro) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **vnímání Spojené státy (Non-UltiPro)** vyberte **vnímání Spojené státy (Non-UltiPro)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Vnímání Spojené státy (Non-UltiPro) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s vnímání Spojené státy (Non-UltiPro) na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v vnímání Spojené státy (Non-UltiPro).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vnímání Spojené státy (Non-UltiPro), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace vnímání Spojené státy (Non-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytváření vnímání Spojené státy (Non-UltiPro) testovacího uživatele](#create-perception-united-states-non-ultipro-test-user)**  – Pokud chcete mít protějšek Britta Simon v vnímání Spojené státy (Non-UltiPro), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s vnímání Spojené státy (Non-UltiPro), proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **vnímání Spojené státy (Non-UltiPro)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Vnímání Spojené státy (Non-UltiPro) domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `https://perception.kanjoya.com/sp`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. **Vnímání Spojené státy (Non-UltiPro)** aplikace vyžaduje **Azure AD identifikátor** hodnotu jako < entity_id >, která budou optimálnímu **nastavení vnímání Spojené státy ( Non-UltiPro)** části zakódovat do identifikátoru uri. K získání hodnoty zakódovat do identifikátoru uri, pomocí následujícího odkazu: **http://www.url-encode-decode.com/**.

    d. Po získání identifikátoru uri kódovanou hodnotu kombinaci se službou **adresy URL odpovědi** jak je uvedeno níže –

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Vložte hodnotu uvedenou výše v **adresy URL odpovědi** textového pole.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit až vnímání Spojené státy (Non-UltiPro)** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurace vnímání Spojené státy (bez UltiPro) jednotného přihlašování

1. V jiném okně prohlížeče Přihlaste se k webu společnosti vnímání Spojené státy (Non-UltiPro) jako správce.

2. Na hlavním panelu nástrojů klikněte na tlačítko **nastavení účtu**.

    ![Uživatel vnímání Spojené státy (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na **nastavení účtu** stránce, proveďte následující kroky:

    ![Uživatel vnímání Spojené státy (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. V **název společnosti** textového pole zadejte název **společnosti**.
    
    b. V **název účtu** textového pole zadejte název **účet**.

    c. V **výchozí odpověď na E-mail** text zadejte platnými **e-mailu**.

    d. Vyberte **zprostředkovatele Identity jednotného přihlašování** jako **SAML 2.0**.

4. Na **Konfigurace jednotného přihlašování** stránce, proveďte následující kroky:

    ![Spojené státy (bez UltiPro) SSOConfig vnímání](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Vyberte **SAML NameID typ** jako **e-MAILU**.

    b. V **název konfigurace jednotného přihlašování** textového pole zadejte název vaší **konfigurace**.
    
    c. V **název zprostředkovatele identit** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal. 

    d. V **pole pro doménu SAML**, zadejte doménu, jako je @contoso.com.

    e. Klikněte na **nahrát znovu** k nahrání **soubor XML s metadaty** souboru.

    f. Klikněte na tlačítko **aktualizace**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k vnímání Spojené státy (Non-UltiPro).

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **vnímání Spojené státy (Non-UltiPro)**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **vnímání Spojené státy (Non-UltiPro)**.

    ![Odkaz vnímání Spojené státy (Non-UltiPro) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Vytváření vnímání Spojené státy (Non-UltiPro) testovacího uživatele

V této části vytvoříte uživateli Britta Simon v vnímání Spojené státy (Non-UltiPro). Práce s [tým podpory vnímání Spojené státy (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) přidat uživatele na platformě vnímání Spojené státy (Non-UltiPro).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici vnímání Spojené státy (Non-UltiPro) na přístupovém panelu, které by měl být automaticky přihlášeni na vnímání Spojených států amerických (Non-UltiPro) u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

