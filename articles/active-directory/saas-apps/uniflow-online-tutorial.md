---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s uniFLOW online | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a uniFLOW online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952671"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s uniFLOW online

V tomto kurzu se dozvíte, jak integrovat uniFLOW online s Azure Active Directory (Azure AD). Když integrujete uniFLOW online s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k uniFLOW online
* Umožněte uživatelům, aby se přihlásili k uniFLOW online pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* uniFLOW online tenant.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* uniFLOW online podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-uniflow-online-from-the-gallery"></a>Přidání uniFLOW online z Galerie

Pokud chcete nakonfigurovat integraci uniFLOW online do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat uniFLOW online z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **uniFLOW online** .
1. Na panelu výsledků vyberte **UniFLOW online** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro uniFLOW online

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí uniFLOW online s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v uniFLOW online.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí uniFLOW online, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
   1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
   1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte UNIFLOW jednotné](#configure-uniflow-online-sso)** přihlašování online – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Přihlaste se k UniFLOW online pomocí vytvořeného testovacího uživatele](#sign-in-to-uniflow-online-using-the-created-test-user)** – k otestování přihlášení uživatele na straně aplikace.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce online integrace aplikací **uniFLOW** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory online klientů uniFLOW](mailto:support@nt-ware.com) . Můžete se také podívat na vzory zobrazené v části **základní konfigurace SAML** v Azure Portal nebo se přečtěte na adresu URL odpovědi zobrazené v online tenantovi uniFLOW.

1. aplikace uniFLOW online očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho uniFLOW aplikace online očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | -----------| --------------- |
    | DisplayName | User. DisplayName |
    | zdívek | User. onpremisessamaccountname |

   > [!NOTE]
   > `user.onpremisessamaccountname`Atribut bude obsahovat hodnotu pouze v případě, že jsou uživatelé služby Azure AD synchronizováni z místní služby Windows Active Directory.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k uniFLOW online.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **UniFLOW online**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

> [!NOTE]
> Pokud chcete všem uživatelům dovolit přístup k aplikaci bez ručního přiřazení, přejděte do části **Správa** a vyberte **vlastnosti**. Pak změňte parametr **vyžadováno přiřazení uživatele** na **ne**.

## <a name="configure-uniflow-online-sso"></a>Konfigurace online jednotného přihlašování uniFLOW

1. V jiném okně webového prohlížeče se přihlaste k online webu uniFLOW jako správce.

1. V levém navigačním panelu vyberte kartu **uživatel** .

    ![Snímek obrazovky se zobrazí uživatel vybraný na online webu uniFLOW.](./media/uniflow-online-tutorial/configure-1.png)

1. Klikněte na **Zprostředkovatelé identity**.

    ![Snímek obrazovky ukazuje vybrané poskytovatele identity.](./media/uniflow-online-tutorial/configure-2.png)

1. Klikněte na **Přidat zprostředkovatele identity**.

    ![Snímek obrazovky s vybraným přidat poskytovatele identity](./media/uniflow-online-tutorial/configure-3.png)

1. V části **Přidat zprostředkovatele identity** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části Přidat zprostředkovatele IDENTITY, kde můžete zadat hodnoty, které jsou popsány.](./media/uniflow-online-tutorial/configure-4.png)

    a. Zadejte zobrazovaný název ex: *AZUREAD SSO*.

    b. Jako **Typ poskytovatele** vyberte možnost **WS-** dobalení z rozevíracího seznamu.

    c. Pro **typ WS-** dobalení vyberte možnost **Azure Active Directory** z rozevíracího seznamu.

    d. Klikněte na **Uložit**.

1. Na kartě **Obecné** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na kartě Obecné, kde můžete zadat hodnoty, které jsou popsány.](./media/uniflow-online-tutorial/configure-5.png)

    a. Zadejte zobrazovaný název ex: *AZUREAD SSO*.

    b. Vyberte možnost **z adresy URL** **federačních metadat služby ADFS**.

    c. Do textového pole **Adresa URL federačních metadat** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    d. Vyberte **zprostředkovatele identity** jako **povolený**.

    e. Vyberte možnost **Automatická registrace uživatele** jako **aktivovanou**.

    f. Klikněte na **Uložit**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Přihlaste se k uniFLOW online pomocí vytvořeného testovacího uživatele.

1. V jiném okně webového prohlížeče přejdete na adresu URL online uniFLOW pro vašeho tenanta.

1. Vyberte dříve vytvořeného poskytovatele identity a přihlaste se pomocí vaší instance služby Azure AD.

1. Přihlaste se pomocí testovacího uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na uniFLOW adresu URL pro přihlášení do online režimu, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL online přihlašování na uniFLOW a spustit tok přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici uniFLOW online v nabídce Moje aplikace, přesměruje se na uniFLOW Online přihlašovací adresu URL. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování uniFLOW online můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).