---
title: 'Kurz: Integrace Azure Active Directory s Five9 Plus adaptérem (CTI, agenti System Center kontakt) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Five9 Plus adaptérem (CTI, agenti System Center kontakt).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: daec6e169805c193b48781dfecbabd9349bdc59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60278670"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Kurz: Integrace Azure Active Directory s Five9 Plus adaptérem (CTI, agenti System Center kontakt)

V tomto kurzu se dozvíte, jak integrovat Five9 Plus adaptér (CTI, kontaktujte agenty System Center) se službou Azure Active Directory (Azure AD).
Integrace Five9 Plus adaptér (CTI, agenti System Center kontakt) s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Five9 Plus adaptér (CTI, agenti System Center kontakt).
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Five9 Plus adaptéru (CTI, agenti System Center kontakt) (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Five9 Plus adaptérem (CTI, agenti System Center kontaktu), potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Five9 Plus adaptér (CTI, agenti System Center kontakt) jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Five9 Plus adaptér (CTI, agenti System Center kontakt) podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Přidání Five9 Plus adaptér (CTI, agenti System Center kontakt) z Galerie

Ke konfiguraci integrace Five9 Plus adaptér (CTI, agenti System Center kontakt) do služby Azure AD, musíte přidat Five9 Plus adaptér (CTI, agenti System Center kontakt) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Five9 Plus adaptér (CTI, agenti System Center kontakt) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Five9 Plus adaptér (CTI, agenti System Center kontakt)** vyberte **Five9 Plus adaptér (CTI, agenti System Center kontakt)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Five9 Plus adaptér (CTI, agenti System Center kontakt) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, agenti System Center kontakt) podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Five9 Plus adaptérem (CTI, agenti System Center kontakt).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, agenti System Center kontaktu), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Five9 Plus adaptér (CTI, agenti System Center kontakt) Single Sign-On](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Five9 Plus adaptér (CTI, agenti System Center kontakt)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)**  – Pokud chcete mít protějšek Britta Simon Five9 Plus adaptérem (CTI, agenti System Center kontaktu), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, agenti System Center kontaktu), proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Five9 Plus adaptér (CTI, agenti System Center kontakt)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Five9 Plus adaptér (CTI, agenti System Center kontakt) domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    
    |    Prostředí      |       zprostředkovatele identity      |
    | :-- | :-- |
    | Za "Five9 i adaptér pro Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Za "Five9 i adaptér pro Zendesku." | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Za "Five9 i adaptér pro klasické pracovní plochy Toolkit agenta" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    |      Prostředí     |      zprostředkovatele identity      |
    | :--                  | :--           |
    | Za "Five9 i adaptér pro Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Za "Five9 i adaptér pro Zendesku." | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Za "Five9 i adaptér pro klasické pracovní plochy Toolkit agenta" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení Five9 Plus adaptér (CTI, agenti System Center kontakt)** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Konfigurace Five9 Plus adaptér (CTI, agenti System Center kontakt) jednotného přihlašování

1. Ke konfiguraci jednotného přihlašování na **Five9 Plus adaptér (CTI, agenti System Center kontakt)** straně, je nutné odeslat na stažený **Certificate(Base64)** a vhodné zkopírovaný adresy URL do [Five9 Plus Tým podpory adaptér (CTI, agenti System Center kontakt)](https://www.five9.com/about/contact). Kromě toho také pro další konfiguraci jednotného přihlašování postupujte prosím podle níže uvedený postup podle adaptér:

    a. "Five9 Plus adaptér pro klasické pracovní plochy Toolkit agenta" příručky pro správce: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adaptér pro Microsoft Dynamics CRM" příručky pro správce: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adaptér pro Zendesk" příručky pro správce: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Five9 Plus adaptéru (CTI, agenti System Center kontakt).

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Five9 Plus adaptér (CTI, agenti System Center kontakt)**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Five9 Plus adaptér (CTI, agenti System Center kontakt)**.

    ![Propojení Five9 Plus adaptéru (CTI, agenti System Center kontakt) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Vytvořit testovacího uživatele Five9 Plus adaptér (CTI, agenti System Center kontakt)

V této části vytvořte uživatele Britta Simon Five9 Plus adaptérem (CTI, agenti System Center kontakt). Práce s [Five9 Plus (CTI, agenti System Center kontakt) podporu adaptérů](https://www.five9.com/about/contact) přidat uživatele na platformě Five9 Plus adaptér (CTI, agenti System Center kontakt). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na adaptér Plus Five9 (CTI, agenti System Center kontakt dlaždici na přístupovém panelu, které by měl být automaticky přihlášeni k Five9 Plus adaptér (CTI, agenti System Center kontakt) u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

