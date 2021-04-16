---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s kontrolou identity Check Point | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a sledováním identity bodu kontroly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520889"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s kontrolou identity Check Point

V tomto kurzu se dozvíte, jak integrovat sledování identity kontrolních bodů pomocí Azure Active Directory (Azure AD). Když integrujete sledování identity kontrolního bodu s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k povědomí o identitě Check Point.
* Umožněte uživatelům, aby se automaticky přihlásili, aby se zkontrolovala dostupnost identity bodů pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Ověřte, že odběr identity kontrolního bodu pro jednotné přihlašování (SSO) je povolený.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sledování identity kontrolního bodu podporuje přihlášení k jednotnému přihlašování přes **SP** .

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Přidání povědomí o identitě kontrolního bodu z Galerie

Pokud chcete nakonfigurovat integraci kontroly identity kontrolního bodu do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat sledování identity kontrolního bodu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **sledování identity kontrolního bodu** .
1. Vyberte z panelu výsledků možnost **zjištění identity kontrolního bodu** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro sledování identity kontrolního bodu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s vědomím Check Point identity pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v povědomí o identitě kontrolního bodu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD se sledováním identity kontrolního bodu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte si jednotné přihlašování k identitám kontrolního bodu](#configure-check-point-identity-awareness-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele sledování identity pro identitu kontrolního bodu](#create-check-point-identity-awareness-test-user)** – Chcete-li mít protějšek B. Simon ve sledování identity kontrolního bodu, které je propojeno s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace **sledování identity kontrolního bodu** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL pro přihlášení. Pro získání těchto hodnot [tým technické podpory o kontaktování identity kontrolního bodu](mailto:support@checkpoint.com) Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení sledování přítomnosti identity kontrolního bodu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k povědomí o identitě kontrolního bodu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **sledování identity v bodu kontroly**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-check-point-identity-awareness-sso"></a>Nakonfigurovat jednotné přihlašování identity kontrolního bodu

1. Přihlaste se k webu ověření identity kontrolního bodu jako správce.

1. V zobrazení SmartConsole > **bran & serverech** klikněte na **nový > další > uživatele/identita > poskytovatel identity**.

    ![snímek obrazovky pro nového zprostředkovatele identity](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. V **novém okně zprostředkovatele identity** proveďte následující kroky.

    ![snímek obrazovky oddílu zprostředkovatele identity](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. V poli **Brána** vyberte bránu zabezpečení, která musí provést ověřování SAML.

    b. V poli **Služba** vyberte z rozevíracího seznamu **povědomí identity** .

    c. Zkopírujte hodnotu **identifikátor (ID entity)** , vložte tuto hodnotu do textového pole **identifikátor** v základní části **Konfigurace SAML** v Azure Portal.

    d. Zkopírujte hodnotu **adresy URL odpovědi** , vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    e. Vyberte **importovat soubor metadat** a nahrajte stažený **certifikát (Base64)** z Azure Portal.

    > [!NOTE]
    > Případně můžete také vybrat možnost **Vložit ručně** , chcete-li ručně vložit hodnoty **ID entity** a **přihlašovací adresy URL** do odpovídajících polí a odeslat **soubor certifikátu** z Azure Portal.

    f. Klikněte na **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Vytvořit testovacího uživatele povědomí o identitě kontrolního bodu

V této části vytvoříte uživatele s názvem Britta Simon na vědomí identity kontrolního bodu. Pokud chcete přidat uživatele na platformě kontroly identity kontrolního bodu, pracujte s [týmem podpory pro sledování identity kontrolního bodu](mailto:support@checkpoint.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ke službě Check Point identity, kde můžete spustit tok přihlášení. 

* Přejděte na adresu URL služby Check Point identity pro přihlášení přímo a zahajte tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když na dlaždici moje aplikace kliknete na dlaždici sledování identity kontrolního bodu, přesměruje se na adresu URL pro přihlášení ke službě Check Point identity. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete povědomí o identitě kontrolního bodu, můžete vyhovět řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


