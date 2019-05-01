---
title: 'Kurz: Integrace Azure Active Directory s xMatters OnDemand | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 4217f85394d66c3da116cf92767ad1da8ad7e799
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920195"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Kurz: Integrace Azure Active Directory s xMatters OnDemand.

V tomto kurzu se dozvíte, jak integrovat xMatters OnDemand s Azure Active Directory (Azure AD).
XMatters OnDemand integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k xMatters OnDemand.
* Můžete povolit uživatelům být automaticky přihlášeni k xMatters OnDemand (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s xMatters OnDemand, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* xMatters OnDemand jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* xMatters OnDemand podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z Galerie

Konfigurace integrace xMatters OnDemand do služby Azure AD, budete muset přidat xMatters OnDemand z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat xMatters OnDemand z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **xMatters OnDemand**vyberte **xMatters OnDemand** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![xMatters OnDemand v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s xMatters OnDemand podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a související uživatelské v xMatters OnDemand musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s xMatters OnDemand, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace xMatters OnDemand Single Sign-On](#configure-xmatters-ondemand-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření xMatters OnDemand testovacího uživatele](#create-xmatters-ondemand-test-user)**  – Pokud chcete mít protějšek Britta Simon v xMatters OnDemand, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s xMatters OnDemand, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **xMatters OnDemand** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![xMatters OnDemand domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory xMatters OnDemand klienta](https://www.xmatters.com/company/contact-us/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Je potřeba směrovat certifikát, který [tým podpory xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Certifikát se musí být odeslán tým podpory xMatters předtím, než může dokončení konfigurace jednotného přihlašování.

6. Na **nastavení xMatters OnDemand** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurace xMatters OnDemand jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti XMatters OnDemand jako správce.

2. Na panelu nástrojů v horní části klikněte na tlačítko **správce**a potom klikněte na tlačítko **údaje o společnosti** na navigačním panelu na levé straně.

    ![Správce](./media/xmatters-ondemand-tutorial/IC776795.png "správce")

3. Na **konfigurace SAML** stránce, proveďte následující kroky:

    ![Konfigurace SAML](./media/xmatters-ondemand-tutorial/IC776796.png "konfigurace SAML")

    a. Vyberte **povolit SAML**.

    b. V **ID zprostředkovatele Identity** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **adresy jednotného přihlašování** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. V **adresu URL jednotného odhlašování** vložit do textového pole **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Na stránce s podrobnostmi společnosti, v horní části stránky, klikněte na tlačítko **uložit změny**.

    ![Podrobnosti o společnosti](./media/xmatters-ondemand-tutorial/IC776797.png "společnosti podrobnosti")

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k xMatters OnDemand.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **xMatters OnDemand**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **xMatters OnDemand**.

    ![XMatters OnDemand odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-xmatters-ondemand-test-user"></a>Vytvoření xMatters OnDemand testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v xMatters OnDemand.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **XMatters OnDemand** tenanta.

2. Klikněte na tlačítko **uživatelé** kartu a pak klikněte na tlačítko **přidat uživatele**.

    ![Uživatelé](./media/xmatters-ondemand-tutorial/IC781048.png "uživatelů")

3. V **přidání uživatele** části, proveďte následující kroky:

    ![Přidání uživatele](./media/xmatters-ondemand-tutorial/IC781049.png "přidat uživatele")

    a. Vyberte **aktivní**.

    b. V **ID uživatele** , jako je textové pole, typ id uživatele uživatele Brittasimon@contoso.com.

    c. V **křestní jméno** textového pole zadejte jméno uživatele, jako je Britta.

    d. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    e. V **lokality** textového pole zadejte platnou lokalitou Azure platná účet AD, které chcete zřídit.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na xMatters OnDemand dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke xMatters OnDemand, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

