---
title: 'Kurz: Integrace Azure Active Directory s Salesforce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ad510b4ca40c4c39fe1d7004b9e8f8fc167f9f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178375"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Kurz: Integrace Azure Active Directory se Salesforce

V tomto kurzu se dozvíte, jak integrovat služby Salesforce se službou Azure Active Directory (Azure AD).
Salesforce integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Salesforce.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Salesforce (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí služby Salesforce, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Salesforce – jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Salesforce **SP** jednotné přihlašování zahájené pomocí

* Podporuje Salesforce **JIT** zřizování uživatelů

* Podporuje Salesforce [ **automatizovaná** zřizování uživatelů](salesforce-provisioning-tutorial.md)

## <a name="adding-salesforce-from-the-gallery"></a>Přidání Salesforce z Galerie

Pokud chcete nakonfigurovat integraci Salesforce do služby Azure AD, budete muset přidat Salesforce z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat z Galerie služby Salesforce, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Salesforce**vyberte **Salesforce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Salesforce v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s Salesforce podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Salesforce.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby Salesforce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace služby Salesforce Single Sign-On](#configure-salesforce-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Salesforce](#create-salesforce-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí služby Salesforce, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Salesforce** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Salesforce domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce:

    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce:

    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory klient Salesforce](https://help.salesforce.com/support) k získání těchto hodnot.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Salesforce** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-salesforce-single-sign-on"></a>Konfigurace služby Salesforce jednotné přihlašování

1. Otevře nová karta prohlížeče a přihlaste se k vašemu účtu správce služby Salesforce.

2. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/configure1.png)

3. Přejděte dolů k položce **nastavení** v navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso.png)

4. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Pokud nemůžete povolit nastavení jednotného přihlašování pro váš účet Salesforce, budete muset kontaktovat [tým podpory klient Salesforce](https://help.salesforce.com/support).

5. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

      ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-enable-saml.png)

6. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový ze souboru metadat**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Klikněte na tlačítko **zvolit soubor** nahrát soubor metadat XML, který jste si stáhli z webu Azure portal a klikněte na tlačítko **vytvořit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/xmlchoose.png)

8. Na **SAML jednotné přihlašování – nastavení** stránky, automaticky vyplnit pole a klikněte na Uložit.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/salesforcexml.png)

9. Na levém navigačním podokně v Salesforce, klikněte na tlačítko **nastavení společnosti** související rozbalíte, a pak klikněte na **Moje doména**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-my-domain.png)

10. Přejděte dolů k položce **konfigurace ověřování** a klikněte **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. V **konfigurace ověřování** části, zkontrolujte **AzureSSO** jako **ověřovací službu** Konfigurace jednotného přihlašování SAML, a pak klikněte na tlačítko  **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Pokud je vybrán více než jedna služba ověřování, uživatelé vyzváni k výběru které ověřovací službu, jako jsou se přihlásit pomocí při inicializaci jednotné přihlašování do prostředí Salesforce. Pokud nechcete, aby bylo možné, měli byste **nechte nezaškrtnuté všechny ostatní služby ověřování**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Salesforce.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Salesforce**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Salesforce**.

    ![Propojení služby Salesforce v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-salesforce-test-user"></a>Vytvořit testovacího uživatele Salesforce

V této části se v Salesforce vytvoří uživatelské volá Britta Simon. Salesforce podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Salesforce, vytvoří se nový při pokusu o přístup k Salesforce. Salesforce také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](salesforce-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby Salesforce na přístupovém panelu, vám by měl být automaticky přihlášeni k Salesforce, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurace zřizování uživatelů](salesforce-provisioning-tutorial.md)
