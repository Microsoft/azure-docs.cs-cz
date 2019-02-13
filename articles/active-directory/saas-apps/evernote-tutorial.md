---
title: 'Kurz: Integrace Azure Active Directory s Evernote | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1875c198115c2d7c37eb48c3fde6c65d4ffe4d2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171923"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Kurz: Integrace Azure Active Directory s Evernote

V tomto kurzu se dozvíte, jak integrovat Evernote s Azure Active Directory (Azure AD).
Evernote integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Evernote.
* Můžete povolit uživatelům být automaticky přihlášeni k Evernote (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Evernote, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Evernote jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Evernote **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-evernote-from-the-gallery"></a>Přidání Evernote z Galerie

Konfigurace integrace Evernote do služby Azure AD, budete muset přidat Evernote z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Evernote z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Evernote**vyberte **Evernote** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Evernote v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Evernote podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Evernote.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Evernote, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Evernote Single Sign-On](#configure-evernote-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Evernote](#create-evernote-test-user)**  – Pokud chcete mít protějšek Britta Simon Evernote, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Evernote, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Evernote** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    ![Evernote domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor** textové pole, zadejte adresu URL:  `https://www.evernote.com/saml2`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/both-preintegrated-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://www.evernote.com/Login.action`

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení Evernote** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-evernote-single-sign-on"></a>Konfigurace Evernote jednotné přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Evernote.

2. Přejděte na **"konzoly pro správu.**

    ![Konzoly pro správu](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z **'Konzoly pro správu'**, přejděte na stránku **"Zabezpečení"** a vyberte **"Single Sign-On"**

    ![Nastavení jednotného přihlašování](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. Nakonfigurujte následující hodnoty:

    ![Nastavení certifikátu.](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Povolení jednotného přihlašování:** Ve výchozím nastavení je povoleno jednotné přihlašování (klikněte na tlačítko **zakázat Single Sign-on** odstraníte požadavek jednotné přihlašování)

    b. Vložit **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal do **URL žádosti HTTP SAML** textového pole.

    c. Otevřete stažený certifikát ze služby Azure AD v Poznámkový blok a zkopírujte obsah, včetně "BEGIN" a "END osvědčení" a vložte ho do **certifikát X.509** textového pole. 

    d.Click **uložit změny**

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

V této části je povolit Britta Simon k udělení přístupu k Evernote použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Evernote**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Evernote**.

    ![Odkaz Evernote v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-evernote-test-user"></a>Vytvoření Evernote testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do Evernote, musí být poskytnuty do Evernote.  
V případě Evernote zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se na web společnosti Evernote jako správce.

2. Klikněte na tlačítko **'Konzoly pro správu'**.

    ![Konzoly pro správu](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z **'Konzoly pro správu'**, přejděte na stránku **'Přidat uživatele'**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Přidat členy týmu** v **e-mailu** textového pole zadejte e-mailovou adresu uživatelského účtu a klikněte na tlačítko **pozvat.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Po odeslání pozvánky držitel účtu Azure Active Directory obdrží e-mailu k přijetí pozvánky.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Evernote na přístupovém panelu, můžete by měl být automaticky přihlášeni k Evernote, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

