---
title: 'Kurz: Integrace Azure Active Directory s Splunk Enterprise a Splunk Cloud | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Splunk Enterprise a Splunk cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 15596c79859b4af08f3b1992a6aa072f5f86b48f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974926"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Kurz: Integrace Azure Active Directory s Splunk Enterprise a Splunk Cloud

V tomto kurzu se dozvíte, jak integrovat řešení Splunk Enterprise a Splunk cloudu s Azure Active Directory (Azure AD).
Integrace řešení Splunk Enterprise a Splunk cloudu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k řešení Splunk Enterprise a Splunk cloudu.
* Uživatelům se automaticky přihlášeni k řešení Splunk Enterprise a Splunk Cloud (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí řešení Splunk Enterprise a Splunk Cloud, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Splunk Enterprise a Splunk Cloud jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje řešení Splunk Enterprise a Splunk Cloud **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Přidání řešení Splunk Enterprise a Splunk Cloud z Galerie

Konfigurovat integraci řešení Splunk Enterprise a Splunk Cloud do služby Azure AD, budete muset přidat Splunk Enterprise a Splunk Cloud z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidat z Galerie řešení Splunk Enterprise a Splunk Cloud, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Splunk Enterprise a Splunk Cloud**vyberte **Splunk Enterprise a Splunk Cloud** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace .

     ![Splunk Enterprise a Splunk Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotného přihlašování se Splunk Enterprise a Splunk cloudu podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Splunk Enterprise a Splunk cloudu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Splunk Enterprise a Splunk Cloud, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace řešení Splunk Enterprise a Splunk cloudu Single Sign-On](#configure-splunk-enterprise-and-splunk-cloud-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření řešení Splunk Enterprise a Splunk Cloud testovacího uživatele](#create-splunk-enterprise-and-splunk-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon v řešení Splunk Enterprise a Splunk Cloud, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Konfigurace služby Azure AD jednotné přihlašování pomocí řešení Splunk Enterprise a Splunk Cloud, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Splunk Enterprise a Splunk Cloud** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Splunk Enterprise a Splunk Cloudová doména a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<splunkserverUrl>/en-US/app/launcher/home`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `<splunkserverUrl>`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [Splunk Enterprise a Cloud klienta Splunk tým podpory](https://www.splunk.com/about-us/contact.html#tabs/customer-support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-splunk-enterprise-and-splunk-cloud-single-sign-on"></a>Konfigurace řešení Splunk Enterprise a Splunk Cloud jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Splunk Enterprise a Splunk Cloud** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [Splunk Tým podpory Enterprise a Splunk Cloud](https://www.splunk.com/about-us/contact.html#tabs/customer-support). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k řešení Splunk Enterprise a Splunk Cloud.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Splunk Enterprise a Splunk Cloud**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Splunk Enterprise a Splunk Cloud**.

    ![Splunk Enterprise a Splunk Cloud odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Vytvoření řešení Splunk Enterprise a Splunk Cloud testovacího uživatele

V této části vytvořte uživatele Britta Simon v řešení Splunk Enterprise a Splunk cloudu. Práce s [Splunk Enterprise a Splunk Cloud tým podpory](https://www.splunk.com/about-us/contact.html#tabs/customer-support) přidat uživatele na Splunk Enterprise a Splunk cloudové platformě. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Splunk Enterprise a Splunk Cloud na přístupovém panelu, vám by měl být automaticky přihlášeni Splunk Enterprise a Splunk Cloud, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

