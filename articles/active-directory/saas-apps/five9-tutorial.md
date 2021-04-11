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
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218715"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Kurz: Azure Active Directory integrace s adaptérem Five9 plus (CTI, agenti kontaktních center)

V tomto kurzu se dozvíte, jak integrovat Five9 plus Adapter (CTI, agenti kontaktních center) s Azure Active Directory (Azure AD). Když integrujete adaptér Five9 plus (CTI, agenti centra kontaktů) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k adaptéru Five9 plus (CTI, agenti kontaktních center).
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Five9 a adaptéru (CTI, agentům kontaktního centra) se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s adaptérem Five9 plus (CTI, agenti kontaktního centra), budete potřebovat tyto položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Five9 plus adaptér (CTI, agenti kontaktního centra) odběr povolený jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Five9 plus adaptér (CTI, agenti kontaktních center) podporuje jednotné přihlašování **IDP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Přidání adaptéru Five9 plus (CTI, agenti kontaktního centra) z Galerie

Pokud chcete nakonfigurovat integraci adaptéru Five9 plus (CTI, agenti centra kontaktů) do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Five9 adaptér (CTI, agenti centra kontaktů).

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Five9 plus Adapter (cti, agenti kontaktních center)** .
1. Z panelu výsledků vyberte **Five9 plus adaptér (cti, agenti kontaktních center)** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Five9 plus Adapter (CTI, agenti kontaktního centra)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Five9 plus Adapter (CTI, agenti kontaktního centra) pomocí testovacího uživatele s názvem **B. Simon**. K tomu, aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Five9 plus Adapter (CTI, agenti kontaktního centra).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Five9 plus Adapter (CTI, agenti kontaktního centra), proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[(cti, agenti centra kontaktů) pro Five9 plus](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele Five9 plus adaptér (cti, agenti kontaktního centra)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – bude mít protějšek B. Simon v adaptéru Five9 plus (cti, agenti kontaktních center), který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **adaptéru Five9 plus (cti, agenti centra kontaktů)** na stránce integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte jednu z následujících adres URL:
    
    |    Prostředí      |       URL      |
    | :-- | :-- |
    | Pro adaptér Five9 Plus pro Microsoft Dynamics CRM " | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Pro "adaptér Five9 Plus pro Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Pro sadu adaptérů Five9 Plus pro sadu agent Desktop Toolkit " | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Do textového pole **Adresa URL odpovědi** zadejte jednu z těchto adres URL:

    |      Prostředí     |      URL      |
    | :--                  | :--           |
    | Pro adaptér Five9 Plus pro Microsoft Dynamics CRM " | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Pro "adaptér Five9 Plus pro Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Pro sadu adaptérů Five9 Plus pro sadu agent Desktop Toolkit " | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavení adaptéru Five9 plus (cti, agenti centra kontaktů)** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k adaptéru Five9 plus (CTI, agentům kontaktního centra).

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Five9 plus adaptér (cti, agenti kontaktních center)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Konfigurace Five9 a adaptéru (CTI, agenti kontaktního centra) jednotného přihlašování

1. Chcete-li nakonfigurovat jednotné přihlašování na straně **adaptéru Five9 plus (cti, agenti centra kontaktů)** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL do [týmu podpory Five9 (cti, agenti centra kontaktů)](https://www.five9.com/about/contact). Kromě toho pro konfiguraci jednotného přihlašování (SSO) dále postupujte podle následujících kroků v závislosti na adaptéru:

    a. "Five9 plus Adapter for agent Desktop Toolkit" – Příručka pro správce: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Příručka pro správce Five9 plus Adapter pro Microsoft Dynamics CRM: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Příručka pro správce "Five9 plus Adapter pro Zendesk": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Vytvořit testovacího uživatele Five9 plus Adapter (CTI, agenti kontaktního centra)

V této části vytvoříte uživatele s názvem Britta Simon v adaptéru Five9 plus (CTI, agenti kontaktních center). Pokud chcete přidat uživatele do platformy Five9 plus Adapter (CTI, agenti centra kontaktů), pracujte s [týmem podpory Five9 plus Adapter (cti, agenti centra kontaktů](https://www.five9.com/about/contact) ). Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k adaptéru Five9 plus (CTI, agentům kontaktního centra), pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici adaptér Five9 plus (CTI, agenti centra kontaktů) v okně moje aplikace byste měli být automaticky přihlášeni k adaptéru Five9 plus (CTI, agentům kontaktního centra), pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete adaptér Five9 plus (CTI, agenti kontaktního centra), můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
