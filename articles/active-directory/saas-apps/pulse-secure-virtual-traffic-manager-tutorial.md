---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím Pulse Secure Virtual Traffic Manager | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pulsem zabezpečenou virtuální Traffic Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d487295e9aab1a56553dc9d31b0a8714688005c3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348410"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím Pulse Secure Virtual Traffic Manager

V tomto kurzu se dozvíte, jak integrovat Pulse Secure Virtual Traffic Manager s Azure Active Directory (Azure AD). Když integrujete pulsně zabezpečenou virtuální Traffic Manager s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Pulse zabezpečené virtuální Traffic Manager.
* Umožněte uživatelům, aby se automaticky přihlásili k Pulse zabezpečené virtuální Traffic Manager se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Pulse Secure Virtual Traffic Manager pro jednotné přihlašování (SSO) s povoleným odběrem.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pulse Secure Virtual Traffic Manager podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Přidání Pulsově zabezpečené virtuální Traffic Manager z Galerie

Pokud chcete nakonfigurovat integraci pulsně zabezpečené virtuální Traffic Manager do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat pulsně zabezpečenou virtuální Traffic Manager z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Pulse Secure Virtual Traffic Manager** .
1. Z panelu výsledků vyberte **Pulse Secure Virtual Traffic Manager** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Pulse Secure Virtual Traffic Manager

Pomocí testovacího uživatele s názvem **B. Simon**nakonfigurujte a otestujte jednotné přihlašování Azure AD s pulsovou zabezpečenou virtuální Traffic Manager. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Pulse Secure Virtual Traffic Manager.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Pulse zabezpečené virtuální Traffic Manager, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Pulse Secure Virtual Traffic Manager SSO](#configure-pulse-secure-virtual-traffic-manager-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si Pulse Secure virtual Traffic Manager Test User](#create-pulse-secure-virtual-traffic-manager-test-user)** – abyste měli protějšek B. Simon v Pulse secure Virtual Traffic Manager, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **Pulse Secure Virtual Traffic Manager** Application Integration Najděte oddíl **Manage (Správa** ) a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<published virtual server FQDN>/saml/consume`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<published virtual server FQDN>/saml/metadata`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienta podpory Pulse Secure pro Virtual Traffic Manager](mailto:support@pulsesecure.net) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Pulse Secure Virtual Traffic Manager** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Pulse zabezpečené virtuální Traffic Manager.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Pulse Secure Virtual Traffic Manager**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Konfigurace Pulsového zabezpečeného jednotného přihlašování k virtuálním Traffic Manager

Tato část se zabývá konfigurací potřebnými k povolení ověřování Azure AD SAML na virtuálním Traffic Manager Pulse. Všechny změny konfigurace se provádí na Pulse Virtual Traffic Manager pomocí webového uživatelského rozhraní pro správu. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Vytvoření důvěryhodného zprostředkovatele identity SAML

a. Přejděte do **katalogu > uživatelského rozhraní Pulse Virtual Traffic Manager zařízení > na stránce katalogu zprostředkovatelů důvěryhodných identit > SAML** a klikněte na **Upravit**.

![Stránka katalogů SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Přidejte podrobnosti o novém zprostředkovateli důvěryhodných identit SAML. zkopírujte informace z podnikové aplikace Azure AD na stránce nastavení jednotného přihlašování a pak klikněte na **vytvořit nového důvěryhodného zprostředkovatele identity**.

![Vytvořit nového důvěryhodného zprostředkovatele identity](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* Do textového pole **název** zadejte název důvěryhodného zprostředkovatele identity. 

* Do textového pole **Entity_id** zadejte hodnotu **identifikátoru služby Azure AD** , kterou jste zkopírovali z Azure Portal.  

* Do textového pole **Adresa URL** zadejte hodnotu **adresy URL** , kterou jste zkopírovali z Azure Portal. 

* Otevřete stažený **certifikát** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu** .

c. Ověřte, že nového zprostředkovatele identity SAML byl úspěšně vytvořen. 

![Ověřit důvěryhodného zprostředkovatele identity](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Konfigurace virtuálního serveru pro použití ověřování Azure AD

a. Přejděte na stránku **uživatelské rozhraní pro správu zařízení Pulse virtual Traffic Manager > Services > virtuální servery** a klikněte na **Upravit** vedle dříve vytvořeného virtuálního serveru.

![Upravit virtuální servery](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. V části **ověřování** klikněte na **Upravit**. 

![Oddíl ověřování](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Nakonfigurujte následující nastavení ověřování pro virtuální server: 

1. Přihlašovací

    ![nastavení ověřování pro virtuální server](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. V **typu auth!** vyberte **poskytovatel služeb SAML** . 

    b. V **podrobném ověřování auth**nastavte na hodnotu Ano, pokud chcete řešit problémy s ověřováním, jinak ponechte výchozí hodnotu ne. 

2. Správa relace ověřování –

    ![Správa relace ověřování](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Pro **ověřování typu relace. cookie_name**ponechte výchozí hodnotu "VS_SamlSP_Auth". 

    b. V případě příkazu **auth! Session! timeout**ponechejte výchozí hodnotu "7200". 

    c. Pokud chcete řešit problémy s ověřováním, v části **ověření! relace! log_external_state**nastavte na hodnotu Ano, jinak ponechte výchozí hodnotu ne. 

    d. V **cookie_attributes ověření! relace!**, přejděte na "HttpOnly". 

3. Poskytovatel služeb SAML –

    ![Poskytovatel služeb SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. V textovém poli **auth! SAML! sp_entity_id** nastavte na stejnou adresu URL, která se používá jako identifikátor konfigurace jednotného přihlašování Azure AD (ID entity). Podobně jako `https://pulseweb.labb.info/saml/metadata` . 

    b. V **sp_acs_url auth! SAML!** nastavte na stejnou adresu URL, která se používá jako adresa URL pro opětovné přehrání konfigurace jednotného přihlašování Azure AD (adresa URL služby pro příjemce kontrolního výrazu). Podobně jako `https://pulseweb.labb.info/saml/consume` . 

    c. V poli **auth! SAML! IDP**vyberte **důvěryhodného poskytovatele identity** , kterého jste vytvořili v předchozím kroku. 

    d. V time_tolerance auth! SAML! ponechejte výchozí hodnotu "5" sekund. 

    e. V nameid_format auth! SAML! vyberte **Neurčeno**.

    f. Změny použijte kliknutím na tlačítko **aktualizovat** v dolní části stránky.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Vytvořit testovacího uživatele Pulse Secure Virtual Traffic Manager

V této části vytvoříte uživatele s názvem Britta Simon v Pulse Secure Virtual Traffic Manager. Pokud chcete přidat uživatele z Pulse zabezpečené virtuální Traffic Manager platformy, pracujte se [seskupením podpory Pulse Secure virtual Traffic Manager](mailto:support@pulsesecure.net) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na pulsně zabezpečenou adresu URL pro přihlášení k virtuálním Traffic Manager, kde můžete spustit tok přihlášení. 

2. Přejít na Pulse Secure Virtual Traffic Manager adresa URL pro přihlášení přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici Pulse Secure Virtual Traffic Manager, přesměruje se to na pulsně zabezpečenou adresu URL pro přihlášení k virtuálnímu Traffic Manager. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete pulsně zabezpečenou virtuální Traffic Manager, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).