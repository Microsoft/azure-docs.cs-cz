---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s monitorem výkonu AppNeta | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppNeta Performance Monitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: b2558b4b3bcd60acba3bf47d4a973a2b6de7424f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736000"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s monitorováním výkonu AppNeta

V tomto kurzu se dozvíte, jak integrovat AppNeta Performance Monitor s Azure Active Directory (Azure AD). Když integrujete AppNeta Performance Monitor s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AppNeta Performance Monitor.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AppNeta sledování výkonu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AppNeta Performance Monitor (jednotné přihlašování) – odběr povolený.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AppNeta Performance Monitor podporuje jednotné přihlašování spouštěné v **SP**

* AppNeta Performance Monitor podporuje **jenom dobu** zřizování uživatelů

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Přidání sledování výkonu AppNeta z Galerie

Pokud chcete nakonfigurovat integraci sledování výkonu AppNeta do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat AppNeta sledování výkonu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AppNeta Performance Monitor** .
1. Vyberte **AppNeta Performance Monitor** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-monitor"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro AppNeta Performance Monitor

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s AppNeta sledováním výkonu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v monitorování výkonu AppNeta.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástroje AppNeta Performance Monitor, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování ke **[sledování výkonu AppNeta](#configure-appneta-performance-monitor-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)** -to znamená, že má protějšek B. Simon v monitoru výkonu AppNeta, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **sledování výkonu AppNeta** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.pm.appneta.com`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Pokud chcete získat tuto hodnotu, kontaktujte [tým podpory pro monitorování výkonu AppNeta](mailto:support@appneta.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace AppNeta Performance Monitor očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace AppNeta Performance Monitor několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | --------| ----------------|
    | firstName| User. křestní jméno|
    | lastName| User. příjmení|
    | e-mail| User. userPrincipalName|
    | name| User. userPrincipalName|
    | skupiny  | User. assignedroles |
    | Android| User. telephoneNumber |
    | title| User. jobtitle|
    | | |

    > [!NOTE]
    > **skupiny** odkazují na skupinu zabezpečení v Appneta, která je namapovaná na **roli** ve službě Azure AD. Podívejte se prosím na [Tento](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) dokument, který vysvětluje, jak vytvářet vlastní role v Azure AD.

    1. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    1. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    1. Ponechte **obor názvů** prázdný.

    1. Jako **atribut** vyberte zdroj.

    1. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    1. Klikněte na **OK** .

    1. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení sledování výkonu AppNeta** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke sledování výkonu AppNeta.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **AppNeta Performance Monitor**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .
## <a name="configure-appneta-performance-monitor-sso"></a>Konfigurace jednotného přihlašování AppNeta Performance Monitor

Chcete-li nakonfigurovat jednotné přihlašování na straně **nástroje AppNeta Performance Monitor** , je třeba odeslat stažená **metadata federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory AppNeta Performance Monitor](mailto:support@appneta.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-appneta-performance-monitor-test-user"></a>Vytvořit testovacího uživatele pro sledování výkonu AppNeta

V této části se v monitorování výkonu AppNeta vytvoří uživatel s názvem Britta Simon. AppNeta Performance Monitor podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v nástroji Sledování výkonu AppNeta neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory AppNeta Performance Monitor](mailto:support@appneta.com).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ke sledování výkonu AppNeta, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení ke sledování výkonu AppNeta přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici sledování výkonu AppNeta v části Moje aplikace, přesměruje se na adresu URL pro přihlášení ke sledování výkonu AppNeta. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování AppNeta Performance Monitor můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
