---
title: 'Kurz: Azure Active Directory integrace s adaptérem Five9 plus (CTI, agenti kontaktních center) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a adaptérem Five9 plus (CTI, agenti kontaktních center).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: 68de5b11c131fe33252178ebecdeb9c3855fe239
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453429"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Kurz: Azure Active Directory integrace s adaptérem Five9 plus (CTI, agenti kontaktních center)

V tomto kurzu se dozvíte, jak integrovat Five9 plus Adapter (CTI, agenti kontaktních center) s Azure Active Directory (Azure AD).
Integrací adaptéru Five9 plus (CTI, agenti centra kontaktů) k Azure AD získáte následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k adaptéru Five9 plus (CTI, agentům kontaktního centra).
* Uživatelům můžete povolit, aby se automaticky přihlásili k adaptéru Five9 plus (CTI, agentům kontaktního centra) (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s adaptérem Five9 plus (CTI, agenti kontaktního centra), budete potřebovat tyto položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Five9 plus adaptér (CTI, agenti kontaktního centra) odběr povolený jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Five9 plus adaptér (CTI, agenti kontaktního centra) podporuje jednotné přihlašování **IDP** .

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Přidávání adaptérů Five9 plus (CTI, agenti kontaktních center) z Galerie

Pokud chcete nakonfigurovat integraci adaptéru Five9 plus (CTI, agenti centra kontaktů) do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Five9 adaptér (CTI, agenti centra kontaktů).

**Chcete-li přidat adaptér Five9 plus (CTI, agenti kontaktních center) z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Five9 plus Adapter (cti, agenti kontaktních center)**, vyberte **Five9 plus adaptér (cti, agenti kontaktních center)** z panelu výsledků a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Five9 plus adaptér (CTI, agenti kontaktních center) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí adaptéru Five9 plus (CTI, agenti kontaktního centra) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Five9 plus Adapter (CTI, agenti kontaktních center).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí adaptéru Five9 plus (CTI, agentů kontaktního centra), musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Five9 plus Adapter (cti, agenti kontaktního centra)](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** a nakonfigurujte nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele adaptéru Five9 plus (cti, agenti centra kontaktů)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – aby bylo možné mít protějšek Britta Simon v Five9 plus Adapter (cti, agenti kontaktních center), který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí adaptéru Five9 plus (CTI, agentů kontaktního centra), proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **adaptér FIVE9 plus (cti, agenti centra kontaktů)** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Five9 plus adaptér (CTI, agenti kontaktního centra) doména a adresy URL jednotného přihlašování](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    |    Prostředí      |       URL      |
    | :-- | :-- |
    | Pro adaptér Five9 Plus pro Microsoft Dynamics CRM " | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Pro "adaptér Five9 Plus pro Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Pro sadu adaptérů Five9 Plus pro sadu agent Desktop Toolkit " | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    |      Prostředí     |      URL      |
    | :--                  | :--           |
    | Pro adaptér Five9 Plus pro Microsoft Dynamics CRM " | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Pro "adaptér Five9 Plus pro Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Pro sadu adaptérů Five9 Plus pro sadu agent Desktop Toolkit " | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavení adaptéru Five9 plus (cti, agenti centra kontaktů)** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Konfigurace adaptéru Five9 plus (CTI, agenti centra kontaktů) jedna Sign-On

1. Chcete-li nakonfigurovat jednotné přihlašování na straně **adaptéru Five9 plus (cti, agenti centra kontaktů)** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL do [týmu podpory Five9 (cti, agenti centra kontaktů)](https://www.five9.com/about/contact). Kromě toho pro konfiguraci jednotného přihlašování (SSO) dále postupujte podle následujících kroků v závislosti na adaptéru:

    a. "Five9 plus Adapter for agent Desktop Toolkit" – Příručka pro správce: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Příručka pro správce Five9 plus Adapter pro Microsoft Dynamics CRM: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Příručka pro správce "Five9 plus Adapter pro Zendesk": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k adaptéru Five9 plus (CTI, agentům kontaktního centra).

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **Five9 plus adaptér (cti, agenti kontaktních center)**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Five9 plus adaptér (cti, agenti kontaktních center)**.

    ![Odkaz na adaptér Five9 plus (CTI, agenti centra kontaktů) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Vytvořit testovacího uživatele Five9 plus Adapter (CTI, agenti kontaktního centra)

V této části vytvoříte uživatele s názvem Britta Simon v adaptéru Five9 plus (CTI, agenti kontaktních center). Pokud chcete přidat uživatele do platformy Five9 plus Adapter (CTI, agenti centra kontaktů), pracujte s [týmem podpory Five9 plus Adapter (cti, agenti centra kontaktů](https://www.five9.com/about/contact) ). Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Five9 plus Adapter (CTI; agenti centra kontaktů na přístupovém panelu byste měli být automaticky přihlášeni k adaptéru Five9 plus (CTI, agentům kontaktního centra), pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)