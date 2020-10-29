---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Workware | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f3f48695f0ef26b6e54073dbfc4bb0c61802fe58
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905706"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Workware

V tomto kurzu se dozvíte, jak integrovat Workware s Azure Active Directory (Azure AD). Když integrujete Workware s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Workware.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Workware svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Workware odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Workware podporuje jednotné přihlašování **IDP** .

## <a name="adding-workware-from-the-gallery"></a>Přidání Workware z Galerie

Pokud chcete nakonfigurovat integraci Workware do služby Azure AD, musíte přidat Workware z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Workware** .
1. Na panelu výsledků vyberte **Workware** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Workware

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Workware pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Workware.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Workware postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte WORKWARE SSO](#configure-workware-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Workware Test User](#create-workware-test-user)** -to, abyste měli protějšek B. Simon v Workware, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Workware** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<WORKWARE_URL>/WW/AuthServices`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Workware](mailto:support@activeops.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Workware** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Workware.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte **Workware** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-workware-sso"></a>Konfigurace jednotného přihlašování Workware

Chcete-li použít funkci jednotného přihlašování v Workware, je nutné provést následující instalaci:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Povolení oprávnění SSO pro správce systému Workware

* Aby správci systému Workware mohli nastavit ověřování SSO, musí být oprávnění k ověřování SSO (v **kategorii oprávnění pro správu > systému v nastavení systému > oprávnění na obrazovku role** ) povolené pro správce systému Workware.

    ![Oprávnění k ověření jednotného přihlašování](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Nastavení ověřování jednotného přihlašování v Workware

1. Přejděte na stránku **nastavení systému** a klikněte na **ověřování SSO** .

1. V části **ověřování jednotného přihlašování** klikněte na tlačítko **Přidat ověřování SSO** a proveďte následující kroky: 

    ![Ověřování SSO](./media/workware-tutorial/authentication.png)

    1. V **externím zprostředkovateli identity** zadejte název IDP.
    1. Jako **typ ověřování** vyberte **SAML 2.0** .
    1. Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** zadejte hodnotu **adresy URL** , kterou jste zkopírovali z Azure Portal.
    1. Do textového pole **Adresa URL vystavitele zprostředkovatele identity** zadejte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.
    1. V textovém poli **Adresa URL pro odhlášení zprostředkovatele identity** zadejte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
    1. Klikněte na **Povolit** .
    1. Nahrajte stažený **certifikát** do **certifikátu zprostředkovatele identity** z Azure Portal.
    1. Klikněte na **Uložit** .


### <a name="create-workware-test-user"></a>Vytvořit testovacího uživatele Workware

1. Přihlaste se k webu Workware jako správce.

1. Vyberte **správce > vytvořit/zobrazit > uživatelské účty > přidat nový.**

1. Na následující stránce proveďte následující kroky.

    ![Testovací uživatel](./media/workware-tutorial/create-user.png)

    a. Do pole **název** zadejte platný název.

    b. Jako **jednotné přihlašování** vyberte **typ ověřování** .

    c. Zadejte požadovaná pole a klikněte na **Uložit** .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k Workware, pro které jste nastavili jednotné přihlašování.

* Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici Workware, měli byste se automaticky přihlásit k Workware, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Workware můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


