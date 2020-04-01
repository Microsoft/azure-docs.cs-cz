---
title: 'Kurz: Integrace služby Azure Active Directory s adaptérem Five9 Plus (CTI, agenti kontaktního centra) | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a adaptérem Five9 Plus (CTI, agenti kontaktního centra).
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
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102455"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Kurz: Integrace služby Azure Active Directory s adaptérem Five9 Plus (CTI, agenti kontaktního centra)

V tomto kurzu se dozvíte, jak integrovat Five9 Plus adaptér (CTI, Contact Center Agenti) s Azure Active Directory (Azure AD).
Integrace adaptéru Five9 Plus (CTI, agenti kontaktního centra) s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Five9 Plus adaptér (CTI, kontakt center agenti).
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k adaptéru Five9 Plus (CTI, Agenti kontaktního centra) (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s adaptérem Five9 Plus (CTI, agenti kontaktního centra), potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jedním přihlášením adaptéru Five9 Plus (CTI, Contact Center Agents)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Five9 Plus Adaptér (CTI, Kontakt Center Agenti) podporuje **IDP** iniciované sso

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Přidání adaptéru Five9 Plus (CTI, agenti kontaktního centra) z galerie

Chcete-li nakonfigurovat integraci adaptéru Five9 Plus (CTI, agenti kontaktního centra) do Azure AD, je třeba přidat adaptér Five9 Plus (CTI, agenti kontaktního centra) z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li z galerie přidat adaptér Five9 Plus (CTI, agenti kontaktního centra), proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **adaptér Five9 Plus (CTI, agenti kontaktního centra),** z panelu výsledků vyberte **adaptér Five9 Plus (CTI, agenti kontaktního centra)** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Adaptér Five9 Plus (CTI, agenti kontaktního centra) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí adaptéru Five9 Plus (CTI, agenti kontaktního centra) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v adaptéru Five9 Plus (CTI, Agenti kontaktního centra).

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí adaptéru Five9 Plus (CTI, agenti kontaktního centra), musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace one sign-On adaptéru Five9 Plus (CTI, Contact Center Agents)](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte Five9 Plus Adaptér (CTI, Contact Center Agenti) testovací uživatel](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – mít protějšek Britta Simon v Five9 Plus adaptér (CTI, kontaktní centrum agenti), který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí adaptéru Five9 Plus (CTI, agenti kontaktního centra), proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **Five9 Plus Adapter (CTI, Contact Center Agents)** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Five9 Plus Adaptér (CTI, Contact Center Agenti) Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    |    Prostředí      |       zprostředkovatele identity      |
    | :-- | :-- |
    | Pro "Adaptér Five9 Plus pro aplikaci Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Pro "Five9 Plus Adaptér pro Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Pro "Five9 Plus adaptér pro sadu nástrojů Pro agenta Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:

    |      Prostředí     |      zprostředkovatele identity      |
    | :--                  | :--           |
    | Pro "Adaptér Five9 Plus pro aplikaci Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Pro "Five9 Plus Adaptér pro Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Pro "Five9 Plus adaptér pro sadu nástrojů Pro agenta Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit adaptér Five9 Plus (CTI, Agenti kontaktního centra)** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Konfigurace jednotného přihlašování adaptéru Five9 Plus (CTI, agenti kontaktního centra)

1. Chcete-li nakonfigurovat jednotné přihlašování na straně **Five9 Plus Adapter (CTI, Contact Center Agents),** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL do [týmu podpory adaptéru Five9 Plus (CTI, Contact Center Agents).](https://www.five9.com/about/contact) Také navíc pro konfiguraci spřiby dále postupujte podle níže uvedených kroků podle adaptéru:

    a. "Five9 Plus adaptér pro agenta Desktop Toolkit" Admin Guide:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Průvodce pro správu "Five9 Plus Adapter for Microsoft Dynamics CRM":[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Průvodce správcem "Five9 Plus Adapter for Zendesk":[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Five9 Plus adaptér (CTI, kontaktní centrum agenti).

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Adaptér Five9 Plus (CTI, Agenti kontaktního centra).**

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Five9 Plus Adapter (CTI, Contact Center Agents)**.

    ![Odkaz Five9 Plus Adapter (CTI, Contact Center Agents) v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Vytvořit five9 plus adaptér (CTI, kontakt center agenti) testovací uživatel

V této části vytvoříte uživatele s názvem Britta Simon v Five9 Plus adaptér (CTI, kontakt center agenti). Práce s [Five9 Plus Adaptér (CTI, Contact Center Agenti) tým podpory](https://www.five9.com/about/contact) přidat uživatele v Five9 Plus adaptér (CTI, Kontaktní centrum agenti) platformy. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po klepnutí na dlaždici Five9 Plus Adapter (CTI, Contact Center Agents dlaždice na přístupovém panelu, měli byste být automaticky přihlášeni k Five9 Plus adaptér (CTI, agenti kontaktního centra), pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

