---
title: 'Kurz: Azure Active Directory integrace s integrací sady webMethods | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a integrací sady webMethods.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: c3eaac19ffbf3fd93311073ff69e28532b1c15e6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520050"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Kurz: Azure Active Directory integrace s integrací sady webMethods

V tomto kurzu se dozvíte, jak integrovat integrační sadu webMethods s Azure Active Directory (Azure AD). Když integruje integrační sadu webMethods s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k integrační sadě webMethods.
* Umožněte uživatelům, aby se automaticky přihlásili k integrační sadě webMethods s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* předplatné webMethods Integration Suite s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sada Integration Suite webMethods podporuje **SP** a **IDP** , které iniciovaly jednotné přihlašování.

* Integration Suite webMethods podporuje zřizování uživatelů **za běhu** .

## <a name="add-webmethods-integration-suite-from-the-gallery"></a>Přidání sady Integration Suite webMethods z Galerie

Pokud chcete nakonfigurovat integraci integrační sady webMethods do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat integrační sadu webMethods z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Integration Suite webMethods** .
1. Z panelu výsledků vyberte možnost **integrační sada webMethods** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-webmethods-integration-suite"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro integrační sadu webMethods

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí integrační sady webMethods pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v integrační sadě webMethods.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí integrační sady WebMethods, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování pro integrační sadu webMethods](#configure-webmethods-integration-suite-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele pro sadu webMethods Integration Suite](#create-webmethods-integration-suite-test-user)** , abyste měli protějšek B. Simon v integrační sadě WebMethods, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Integration Suite** pro webové metody najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat **integrační Cloud webMethods**, v **základní části Konfigurace SAML** , pokud chcete nakonfigurovat aplikaci v režimu spuštěném v **IDP** , proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL identifikátoru |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |
    |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL odpovědi |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`|
    |

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Přihlašovací adresa URL |
    |--------------------------------|
    |`https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`|
    |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klientů pro integraci webMethods Integration Suite](https://empower.softwareag.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Pokud chcete nakonfigurovat **Cloud API webMethods**, v **základní části Konfigurace SAML** , pokud chcete nakonfigurovat aplikaci v režimu, který je spuštěný v **IDP** , proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL identifikátoru |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    |`<SUBDOMAIN>.webmethodscloud.eu`|
    | `<SUBDOMAIN>.webmethodscloud.de`|
    |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL odpovědi |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`|
    |

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:
    
    | Přihlašovací adresa URL |
    |--------------------------------|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klientů pro integraci webMethods Integration Suite](https://empower.softwareag.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavit integrační sadu webMethods** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k integrační sadě webMethods.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **integrační sada webMethods**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-webmethods-integration-suite-sso"></a>Konfigurace jednotného přihlašování pro integrační sadu webMethods

Chcete-li nakonfigurovat jednotné přihlašování na straně **integračních metod webMethods** , je třeba odeslat stažený **kód XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory pro integrační sady webMethods](https://empower.softwareag.com/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-webmethods-integration-suite-test-user"></a>Vytvořit testovacího uživatele pro integrační sadu webMethods

V této části se v integrační sadě webMethods vytvoří uživatel s názvem Britta Simon. Integration Suite webMethods podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v integrační sadě webMethods ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro integrační sadu WebMethods, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlašování k rozhraní webMethods Integration Suite přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste být automaticky přihlášeni k integrační sadě WebMethods, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici integračních metod webMethods v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k integrační sadě WebMethods, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete integrační sadu WebMethods, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
