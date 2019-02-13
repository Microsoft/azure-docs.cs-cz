---
title: 'Kurz: Integrace Azure Active Directory s Autotask pracoviště | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Autotask pracoviště.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72dc8db48736d586a6db171d27f308339674d73c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189153"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Kurz: Integrace Azure Active Directory s Autotask pracoviště

V tomto kurzu se dozvíte, jak integrovat Autotask pracoviště s Azure Active Directory (Azure AD).
Integrace Autotask pracoviště s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti Autotask.
* Uživatelům se automaticky přihlášeni k síti na pracovišti Autotask (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Autotask síti na pracovišti, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Autotask pracoviště jednotného přihlašování povolená předplatného
* Síti na pracovišti Autotask jednotného přihlašování povolená předplatné
* Musíte být správce nebo správce super v síti na pracovišti.
* Musíte mít účet správce ve službě Azure AD.
* Uživatele, kteří budou tuto funkci využít, musí mít účty v síti na pracovišti a Azure AD a jejich e-mailové adresy pro obě se musí shodovat.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Autotask pracoviště **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-autotask-workplace-from-the-gallery"></a>Přidání Autotask síti na pracovišti z Galerie

Pokud chcete nakonfigurovat integraci pracovní plocha Autotask do služby Azure AD, budete muset přidat Autotask síti na pracovišti z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Autotask síti na pracovišti z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Autotask pracoviště**vyberte **Autotask pracoviště** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Autotask síti na pracovišti v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Autotask pracoviště podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v síti na pracovišti Autotask.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Autotask síti na pracovišti, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Autotask pracoviště Single Sign-On](#configure-autotask-workplace-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Autotask pracoviště](#create-autotask-workplace-test-user)**  – Pokud chcete mít protějšek Britta Simon Autotask pracovišti, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Autotask pracoviště, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Autotask pracoviště** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Autotask pracoviště domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Autotask pracoviště domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory Autotask pracoviště klienta](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavení pracoviště Autotask** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-autotask-workplace-single-sign-on"></a>Konfigurace Autotask pracoviště jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k síti na pracovišti Online pomocí přihlašovacích údajů správce.

    > [!Note]
    > Při konfiguraci zprostředkovatele identity, bude potřeba zadat subdomény. Potvrďte správné subdomény, přihlaste se k síti na pracovišti Online. Po přihlášení, poznamenejte si poddomény v adrese URL. Poddomény je ta část mezi "https://" a ".awp.autotask.net/" a musí být v USA, Evropa, certifikační autority nebo Austrálie.

2. Přejděte na **konfigurace** > **Single Sign-On** a proveďte následující kroky:

    ![Autotask Single Sign-on konfigurace](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Vyberte **soubor metadat XML** možnost a potom ho nahrajete na stažený **kód XML metadat federace** z webu Azure portal.

    b. Klikněte na tlačítko **povolit jednotné přihlašování**.

    ![Autotask Single Sign-on schválit konfigurace](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Vyberte **potvrzuji, že tyto informace jsou správné a Věřím tohoto zprostředkovatele identity** zaškrtávací políčko.

    d. Klikněte na tlačítko **SCHVÁLIT**.

> [!Note]
> Pokud potřebujete pomoc s konfigurací Autotask síti na pracovišti, najdete [na této stránce](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) a vyžádejte si pomoc s vaším účtem v síti na pracovišti.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k síti na pracovišti Autotask.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Autotask pracoviště**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Autotask pracoviště**.

    ![Odkaz Autotask síti na pracovišti v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-autotask-workplace-test-user"></a>Vytvořit testovacího uživatele Autotask pracoviště

V této části vytvoříte uživatele v síti na pracovišti Autotask jako Britta Simon. Prosím práci s [tým podpory Autotask pracoviště](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) k přidání uživatelů v síti na pracovišti Autotask platformy.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Autotask pracoviště na přístupovém panelu, vám by měl být automaticky přihlášeni k pracovní ploše Autotask, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
