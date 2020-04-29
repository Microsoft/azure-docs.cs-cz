---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s nástrojem bizagi Studio pro automatizaci digitálních procesů | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a bizagi Studio pro automatizaci digitálních procesů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3d4613-c3fb-485c-b7b9-c385713e6f8f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f7dd96bc2767b98174bee2cadaa93a6bfa1459
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78207509"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-studio-for-digital-process-automation"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s nástrojem bizagi Studio pro automatizaci digitálních procesů

V tomto kurzu se dozvíte, jak integrovat bizagi Studio pro automatizaci digitálních procesů pomocí Azure Active Directory (Azure AD). Když integruje bizagi Studio pro automatizaci digitálních procesů pomocí Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k bizagi studiu pro automatizaci digitálního procesu.
* Umožněte uživatelům, aby se automaticky přihlásili do bizagi studia pro automatizaci digitálních procesů pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Bizagi Studio pro předplatné digitálního procesu Automation s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Bizagi Studio pro automatizaci digitálních procesů podporuje jednotné přihlašování (SSO) iniciované **SP**
* Jakmile nakonfigurujete bizagi Studio pro automatizaci digitálních procesů, můžete vynutili řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-studio-for-digital-process-automation-from-the-gallery"></a>Přidání bizagi studia pro automatizaci digitálních procesů z Galerie

Pokud chcete nakonfigurovat integraci bizagi studia pro automatizaci digitálních procesů do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat bizagi Studio pro automatizaci digitálních procesů z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **bizagi Studio pro automatizaci digitálního procesu** .
1. Z panelu výsledků vyberte **bizagi Studio pro automatizaci digitálního procesu** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-studio-for-digital-process-automation"></a>Konfigurace a testování jednotného přihlašování Azure AD pro bizagi Studio pro automatizaci digitálních procesů

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí bizagi studia pro automatizaci digitálních procesů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v bizagi studiu pro automatizaci digitálních procesů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí bizagi studia pro automatizaci digitálních procesů, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte bizagi Studio pro jednotné přihlašování digitálního procesu](#configure-bizagi-studio-for-digital-process-automation-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte bizagi Studio pro testovacího uživatele pro automatizaci digitálního procesu](#create-bizagi-studio-for-digital-process-automation-test-user)** – bude mít protějšek B. Simon v bizagi studiu pro automatizaci digitálních procesů, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací **pro automatizaci aplikace bizagi Studio pro digitální zpracování** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory bizagi studia pro automatizaci digitálního procesu](mailto:jarvein.rivera@bizagi.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k bizagi studiu pro automatizaci digitálního procesu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **bizagi Studio pro automatizaci digitálních procesů**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-bizagi-studio-for-digital-process-automation-sso"></a>Konfigurace bizagi studia pro jednotné přihlašování digitálního procesu

Pokud chcete nakonfigurovat jednotné přihlašování v **bizagi studiu pro automatizaci digitálních procesů** , musíte poslat **adresu URL federačních metadat aplikace** do [bizagi studia pro tým podpory pro automatizaci digitálního procesu](mailto:jarvein.rivera@bizagi.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-bizagi-studio-for-digital-process-automation-test-user"></a>Vytvořit bizagi Studio pro testovacího uživatele pro automatizaci digitálního procesu

V této části vytvoříte v bizagi studiu uživatele s názvem Britta Simon pro automatizaci digitálních procesů. Pracujte s [bizagi Studio pro tým podpory automatizace digitálního procesu](mailto:jarvein.rivera@bizagi.com) a přidejte uživatele v bizagi studiu pro platformu automatizace digitálního procesu. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici bizagi Studio pro automatizaci digitálního procesu na přístupovém panelu byste měli být automaticky přihlášení do bizagi studia pro automatizaci digitálních procesů, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si bizagi Studio pro automatizaci digitálních procesů pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)