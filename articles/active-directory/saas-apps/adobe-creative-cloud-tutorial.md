---
title: 'Kurz: Integrace Azure Active Directory s Adobe Creative Cloud | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a160c604050f567f3fc0f77ca20a22349f07fd9
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341160"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Kurz: Integrace Azure Active Directory s Adobe Creative Cloud

V tomto kurzu se dozvíte, jak integrovat Adobe Creative Cloud s Azure Active Directory (Azure AD).
Integrace Adobe Creative Cloud s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Adobe Creative Cloud.
* Uživatelům se automaticky přihlášeni k Adobe Creative Cloud (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Adobe Creative Cloud, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Creative Cloud jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Adobe Creative Cloud podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Přidání Adobe Creative Cloud z Galerie

Pokud chcete nakonfigurovat integraci Adobe Creative Cloud do služby Azure AD, budete muset přidat Adobe Creative Cloud na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Adobe Creative Cloud z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Creative Cloud**vyberte **Adobe Creative Cloud** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Adobe Creative Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí Adobe Creative Cloud na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v aplikaci Adobe Creative Cloud.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe Creative Cloud, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Adobe Creative Cloud Single Sign-On](#configure-adobe-creative-cloud-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon v Adobe Creative Cloud, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Adobe Creative Cloud, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Adobe Creative Cloud** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Adobe Creative Cloud domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte hodnotu jako: `https://adobe.com`.

    b. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Aktualizujte tuto hodnotu skutečné identifikátor. Kontakt [tým podpory Adobe Creative Cloud klienta](https://www.adobe.com/au/creativecloud/business/teams/plans.html) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Adobe Creative Cloud aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Uživatelé musí mít platnou licenci Office 365 ExO e-mailu pro hodnoty, který se má naplnit odpověď SAML deklarace identity.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení Adobe Creative Cloud** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Konfigurace Adobe Creative Cloud jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se do [konzoly pro správu Adobe](https://adminconsole.adobe.com) jako správce.

2. Přejděte na **nastavení** na horním navigačním panelu a klikněte na tlačítko **Identity**. Otevře se seznam domén. Klikněte na tlačítko **konfigurovat** odkaz proti vaší doméně. Pak proveďte následující kroky na **jednotné přihlašování na konfigurace požadované** oddílu. Další informace najdete v tématu [nastavení domény](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Nastavení](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "nastavení")

    a. Klikněte na tlačítko **Procházet** na kterou odešlete certifikát stažený z Azure AD, aby **IDP certifikát**.

    b. V **Vystavitel zprostředkovatele identity** textového pole vložte hodnotu **SAML Entity Id** zkopírovanou z **nakonfigurovat přihlašování** části webu Azure Portal.

    c. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **adresa URL služby jednotného přihlašování SAML** zkopírovanou z **nakonfigurovat přihlašování** části webu Azure Portal.

    d. Vyberte **HTTP - Redirect** jako **IDP vazby**.

    e. Vyberte **e-mailová adresa** jako **nastavení hlavního názvu uživatele přihlášení**.

    f. Klikněte na tlačítko **Uložit** tlačítko.

3. Řídicí panel se teď k dispozici XML **"Stáhnout Metadata"** souboru. Obsahuje EntityDescriptor adresy URL a AssertionConsumerService Adobe. Otevřete soubor a nakonfigurovat v aplikaci Azure AD.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Použijte hodnotu EntityDescriptor Adobe předpokladu, že pro **identifikátor** na **nakonfigurovat nastavení aplikace** dialogového okna.

    b. Použijte hodnotu AssertionConsumerService Adobe předpokladu, že pro **adresy URL odpovědi** na **nakonfigurovat nastavení aplikace** dialogového okna.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Adobe Creative Cloud.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Adobe Creative Cloud**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Adobe Creative Cloud**.

    ![V seznamu aplikací na odkaz Adobe Creative Cloud](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-adobe-creative-cloud-test-user"></a>Vytvořit testovacího uživatele Adobe Creative Cloud

Chcete-li povolit uživatele Azure AD k přihlášení do Adobe Creative Cloud, musí být poskytnuty do Adobe Creative Cloud. V případě Adobe Creative Cloud je zřizování úlohu.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ke zřízení uživatelských účtů, proveďte následující kroky:

1. Přihlaste se k [konzoly pro správu Adobe](https://adminconsole.adobe.com) lokality jako správce.

2. Přidat jako federované ID uživatele v rámci konzoly Adobe a přiřadit profil produktu. Podrobné informace o přidávání uživatelů najdete v tématu [přidat uživatele konzoly pro správu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. V tuto chvíli zadejte e-mailovou adresu nebo hlavní název uživatele do formuláře signin Adobe, stisknutím klávesy tab a by měl Federovat zpět do služby Azure AD:
    * Webový přístup: www.adobe.com > přihlášení
    * V rámci aplikace klasické pracovní plochy nástroje > přihlášení
    * V rámci aplikace > Nápověda > přihlášení

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Adobe Creative Cloud na přístupovém panelu, vám by měl být automaticky přihlášeni na Adobe Creative Cloud, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Nastavení domény (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Konfigurace Azure pro použití s jednotným Přihlašováním Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
