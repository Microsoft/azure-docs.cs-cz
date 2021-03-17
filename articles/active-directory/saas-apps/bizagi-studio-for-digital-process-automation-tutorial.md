---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s bizagi pro automatizaci digitálních procesů | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a bizagi pro automatizaci digitálních procesů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457057"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s bizagi pro automatizaci digitálních procesů

V tomto kurzu se dozvíte, jak integrovat bizagi pro služby automatizace digitálního procesu nebo server pomocí Azure Active Directory (Azure AD). Při integraci bizagi pro automatizaci digitálních procesů pomocí Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k projektu bizagi pro služby nebo server pro automatizaci digitálních procesů.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k projektu bizagi pro digitální proces AutomationServices nebo server s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Bizagi projekt pomocí automatizačních služeb nebo serverů. 
* Mít vlastní certifikáty pro signatury kontrolního výrazu SAML. Tento certifikát musí být vygenerován ve formátu P12 nebo PFX.
* Mít soubor metadat ve formátu XML generovaném z projektu bizagi. 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v projektu bizagi pomocí služby Automation Services nebo serveru.

* Bizagi pro automatizaci digitálních procesů podporuje jednotné přihlašování v **SP** .
* Po nakonfigurování bizagi pro automatizaci digitálních procesů můžete vynutili řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Přidání bizagi pro automatizaci digitálních procesů z Galerie

Pokud chcete nakonfigurovat integraci bizagi pro automatizaci digitálních procesů do služby Azure AD, musíte přidat bizagi pro automatizaci digitálních procesů z Galerie do vašeho seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **bizagi pro automatizace digitálního procesu** .
1. Z panelu výsledků vyberte **bizagi pro automatizaci digitálního procesu** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Konfigurace a testování jednotného přihlašování Azure AD pro bizagi pro automatizaci digitálních procesů

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí bizagi pro automatizaci digitálních procesů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v projektu bizagi.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí bizagi pro automatizaci digitálních procesů, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte bizagi pro jednotné přihlašování digitálního procesu](#configure-bizagi-for-digital-process-automation-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte bizagi pro testovacího uživatele automatizace digitálního procesu](#create-bizagi-for-digital-process-automation-test-user)** – Pokud chcete mít protějšek B. Simon v bizagi pro automatizaci digitálních procesů, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Automatizace digitálního procesu pro automatizaci** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Nahrajte soubor metadat bizagi v možnosti **nahrát soubor metadat** .
1. Zkontrolujte konfiguraci. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL vašeho projektu bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL vašeho projektu bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru. Pokud chcete získat tyto hodnoty, kontaktujte [bizagi pro tým podpory automatizace digitálního procesu](mailto:jarvein.rivera@bizagi.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)
    
    Tato adresa URL metadat musí být registrovaná v možnostech ověřování vašeho projektu bizagi.
    
1. Na stránce **nastavit jednotné přihlašování pomocí SAML**klikněte na ikonu pro úpravy/pero pro **atributy uživatele & deklarace identity** pro úpravu jedinečného identifikátoru uživatele.
    
    Nastavte jedinečný identifikátor uživatele jako uživatel. mail.

### <a name="create-an-azure-ad-test"></a>Vytvoření testu Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k bizagi pro automatizaci digitálního procesu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **bizagi pro automatizaci digitálního procesu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Konfigurace bizagi pro jednotné přihlašování pro automatické zpracování digitálního procesu

Pokud chcete nakonfigurovat jednotné přihlašování na **bizagi pro automatizaci digitálních procesů** , musíte poslat **adresu URL federačních metadat aplikace** do [bizagi pro tým podpory automatizace digitálního procesu](mailto:jarvein.rivera@bizagi.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Vytvořit bizagi pro testovacího uživatele pro automatizaci digitálního procesu

V této části vytvoříte uživatele s názvem Britta Simon v bizagi pro automatizaci digitálních procesů. Pracujte s [bizagi pro tým podpory automatizace digitálního procesu](mailto:jarvein.rivera@bizagi.com) a přidejte uživatele v bizagi pro platformu automatizace digitálního procesu. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici bizagi for Digital Process Automation na přístupovém panelu, měli byste být automaticky přihlášeni k portálu bizagi pro automatizaci digitálních procesů, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si bizagi pro automatizaci digitálních procesů pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)