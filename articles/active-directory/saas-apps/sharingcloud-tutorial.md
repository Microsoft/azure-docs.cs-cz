---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SharingCloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a okamžitou sadou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 7ae447a9577feba8b43b5b03a757ec4095ee2cb4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177891"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SharingCloud

V tomto kurzu se dozvíte, jak integrovat SharingCloud s Azure Active Directory (Azure AD). Když integrujete SharingCloud s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SharingCloud.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SharingCloud svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Pokud se chcete dozvědět víc o integraci aplikací SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* SharingCloud odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SharingCloud podporuje jednotné přihlašování (SSO) **a IDP** .
* SharingCloud podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-sharingcloud-from-the-gallery"></a>Přidání SharingCloud z Galerie

Pokud chcete nakonfigurovat integraci SharingCloud do služby Azure AD, musíte přidat SharingCloud z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)
    
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)
    
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.

    ![Tlačítko Nová aplikace](common/add-new-app.png)
    
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SharingCloud** .

    ![SharingCloud v seznamu výsledků](common/search-new-app.png)
    
1. Na panelu výsledků vyberte **SharingCloud** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharingcloud"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SharingCloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SharingCloud pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SharingCloud.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SharingCloud, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SHARINGCLOUD SSO](#configure-sharingcloud-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SharingCloud Test User](#create-sharingcloud-test-user)** -to, abyste měli protějšek B. Simon v SharingCloud, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SharingCloud** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
    
    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)
    
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu **Upravit** pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    Nahrajte soubor metadat se souborem XML, který poskytuje SharingCloud. Pokud chcete získat soubor, obraťte se na [tým podpory klienta SharingCloud](mailto:support@sharingcloud.com) .

    ![image](common/upload-metadata.png)
    
    Vyberte poskytnutý soubor metadat a klikněte na **nahrát**.

    ![image](common/browse-upload-metadata.png)

1. SharingCloud aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit_attribute.png)

1. Kromě toho očekává aplikace SharingCloud několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    | ---------------| --------- |
    | urn: sharingcloud: SSO: FirstName | User. křestní jméno |
    | urn: sharingcloud: SSO: LastName | User. příjmení |
    | urn: sharingcloud: SSO: email | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na ikonu **Kopírovat** a zkopírujte **adresu URL federačních metadat** z daných možností dle vašeho požadavku.

    ![Adresa URL metadat ke zkopírování](common/copy_metadataurl.png)

## <a name="configure-sharingcloud-sso"></a>Konfigurace jednotného přihlašování SharingCloud

Ke konfiguraci jednotného přihlašování na straně **SharingCloud** je potřeba odeslat zkopírovaná **Adresa URL federačních metadat** z Azure Portal do [týmu podpory SharingCloud](mailto:support@sharingcloud.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SharingCloud.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SharingCloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sharingcloud-test-user"></a>Vytvořit testovacího uživatele SharingCloud

V této části se v SharingCloud vytvoří uživatel s názvem Britta Simon. SharingCloud podporuje zřizování uživatelů jenom v čase, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v SharingCloud neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

* Přejít na adresu URL SharingCloud přímo a zahájit tok přihlášení.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování SharingCloud můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

