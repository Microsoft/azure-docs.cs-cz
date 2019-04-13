---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Procore | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Procore jednotného přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bba33792bd35227fa64ef556e811cb0bddbab74d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544717"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Kurz: Integrace Azure Active Directory s Procore jednotného přihlašování

V tomto kurzu se dozvíte, jak integrovat Procore jednotného přihlašování s Azure Active Directory (Azure AD).
Integrace Procore jednotného přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Procore jednotného přihlašování.
* Uživatelům se automaticky přihlášeni k Procore jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Procore, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Procore jednotného přihlašování jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Procore jednotného přihlašování podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-procore-sso-from-the-gallery"></a>Přidání jednotného přihlašování k Procore z Galerie

Konfigurace integrace Procore jednotné přihlašování do služby Azure AD, budete muset přidat Procore jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Procore jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Procore jednotného přihlašování**vyberte **Procore jednotného přihlašování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Procore jednotné přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Procore jednotného přihlašování na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Procore jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Procore jednotného přihlašování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Procore jednotného přihlašování Single Sign-On](#configure-procore-sso-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Procore jednotného přihlašování](#create-procore-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Procore jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Procore, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Procore jednotného přihlašování** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![Procore jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](common/preintegrated.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit jednotné přihlašování Procore** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurace jednotného přihlašování k Procore jednotného přihlašování

1. Ke konfiguraci jednotného přihlašování na **Procore jednotného přihlašování** straně, přihlaste se k webu procore společnosti jako správce.

2. Ze seznamu nástrojů dolů, klikněte na **správce** otevřete stránku nastavení jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_tool_admin.png)

3. Vložení hodnoty do polí, jak je popsáno níže –

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. V **jednotné přihlašování vystavitele adresa URL** text vložte hodnotu **Azure AD identifikátor** který jste zkopírovali z portálu Azure portal.

    b. V **SAML znak v cílové adrese URL** vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    c. Nyní otevřete **kód XML metadat federace** nad stáhnout z webu Azure portal a zkopírujte certifikát ve značce s názvem **certifikátu x 509**. Vložte zkopírovaný hodnotu do **Single Sign On x509 certifikát** pole.

4. Klikněte na **Save Changes** (Uložit změny).

5. Po nastavení, musí odeslat **název domény** (např. **contoso.com**) prostřednictvím které se přihlašujete do Procore k [tým Procore podpory](https://support.procore.com/) ty budou Aktivujte federovaného jednotného přihlašování pro tuto doménu.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Procore jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Procore jednotného přihlašování**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Procore jednotného přihlašování**.

    ![Odkaz Procore jednotného přihlašování v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-procore-sso-test-user"></a>Vytvořit testovacího uživatele Procore jednotného přihlašování

Postupujte prosím podle níže uvedený postup k vytvoření Procore testovacího uživatele na straně Procore jednotného přihlašování.

1. Přihlaste se k webu procore společnosti jako správce.    

2. Ze seznamu nástrojů dolů, klikněte na **Directory** otevřete stránku directory společnosti.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klikněte na **přidat uživatele** provádět možnost otevřete formulář a zadejte následující možnosti -

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_add.png)

    a. V **křestní jméno** textového pole jméno typ uživatele jako **Britta**.

    b. V **příjmení** textové pole, příjmení typ uživatele jako **Simon**.

    c. V **e-mailovou adresu** , jako je textové pole, typ uživatele e-mailovou adresu BrittaSimon@contoso.com.

    d. Vyberte **šablona oprávnění** jako **později použít šablonu oprávnění**.

    e. Klikněte na možnost **Vytvořit**.

4. Zkontrolujte a aktualizujte podrobnosti o nově přidaných kontaktu.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_check.png)

5. Klikněte na **uložit a poslat pozvánku** (Pokud pozvání prostřednictvím e-mailu se vyžaduje) nebo **Uložit** (uložit přímo) k dokončení registrace uživatele.
    
    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Procore jednotného přihlašování na přístupovém panelu, vám by měl být automaticky přihlášeni ke Procore jednotného přihlašování, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

