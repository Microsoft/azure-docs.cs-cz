---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s pulsem zabezpečenými počítači | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pulsem zabezpečenými počítači.
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
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511439"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s pulsem zabezpečenými počítači

V tomto kurzu se dozvíte, jak integrovat Pulse zabezpečené počítače s Azure Active Directory (Azure AD). Když integrujete Pulse zabezpečené počítače s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k pulsem zabezpečeným počítačům.
* Umožněte uživatelům, aby se automaticky přihlásili k pulsně zabezpečeným počítačům s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr Pulse zabezpečeného jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pulsní zabezpečené počítače podporují jednotné přihlašování **SP** .

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Přidání Pulsově zabezpečených počítačů z Galerie

Pokud chcete nakonfigurovat integraci pulsně zabezpečených počítačů do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Pulse zabezpečené počítače z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Pulse Secure PC** .
1. Vyberte **Pulse Secure PC** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro počítače Pulse Secure

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Pulse zabezpečených počítačů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v počítačích Pulse Secure.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Pulse zabezpečených počítačů, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Pulse zabezpečeného počítače](#configure-pulse-secure-pcs-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření pulsně zabezpečených počítačů testovacího uživatele](#create-pulse-secure-pcs-test-user)** – Chcete-li mít protějšek B. Simon v Pulse zabezpečených počítačích, které jsou propojeny s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Pulse Secure počítače** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, adresy URL odpovědi a identifikátoru. Pro získání těchto hodnot se obraťte na [tým podpory pro klienta podpory Pulse Secure](mailto:support@pulsesecure.net) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit počítače se zabezpečením Pulse** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k pulsům zabezpečeným počítačům.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Pulse Secure počítače**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-pulse-secure-pcs-sso"></a>Konfigurace jednotného přihlašování pro Pulse zabezpečené počítače

Tato část popisuje konfigurace SAML potřebné ke konfiguraci počítačů jako SAML SP. Další základní konfigurace, jako je vytváření sfér a rolí, se netýkají.

**Mezi bezpečná připojení Pulse Connect patří:**

* Konfigurace služby Azure AD jako zprostředkovatele metadat SAML
* Konfigurace ověřovacího serveru SAML
* Přiřazení k příslušným sférám a rolím

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Konfigurace služby Azure AD jako zprostředkovatele metadat SAML

Na následující stránce proveďte následující kroky:

![Konfigurace zabezpečení Pulse Connect](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Přihlaste se do konzoly pro správu zabezpečení Pulse Connect.
1. Přejít na **konfiguraci System-> – > SAML**
1. Klikněte na **Nový poskytovatel metadat** .
1. Zadejte platný název do textového pole **název** .
1. Nahrajte stažený soubor XML s metadaty z Azure Portal do **souboru metadat služby Azure AD**.
1. Vybrat **přijmout nepodepsaná metadata**
1. Vybrat role jako **zprostředkovatele identity**
1. Klikněte na **Uložit změny**.

#### <a name="steps-to-create-a-saml-auth-server"></a>Postup vytvoření ověřovacího serveru SAML:

1. Přechod na **ověřování – > ověřovací servery**
1. Vyberte **Nový: Server SAML** a klikněte na **Nový server** .

    ![Pulse Connect Secure auth Server](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Na stránce Nastavení proveďte následující kroky:

    ![Nastavení zabezpečeného ověřování serveru Pulse Connect](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Do textového pole zadejte **název serveru** .

    b. Jako **metadata** vyberte **SAML verze 2,0** a **režim konfigurace** .

    c. Zkopírujte hodnotu **ID zabezpečené entity Connect** a vložte ji do pole **Adresa URL identifikátoru** v DIALOGOVÉM okně **základní konfigurace SAML** v Azure Portal.

    d. Z **rozevíracího seznamu ID entity zprostředkovatele identity** vyberte hodnotu ID entity Azure AD.

    e. Z **rozevíracího seznamu Adresa URL pro přihlášení k jedné Sign-On službě zprostředkovatele identity** vyberte hodnotu URL pro přihlášení Azure AD.

    f. **Jediným odhlášením** je volitelné nastavení. Pokud je vybraná tato možnost, po odhlášení se zobrazí výzva k zadání nového ověřování. Pokud tuto možnost nevyberete a nezavřeli jste prohlížeč, můžete se znovu připojit bez ověřování.

    například Vyberte **požadovanou třídu kontextu Authn** jako **heslo** a **metodu porovnání** jako **přesnou**.

    h. Nastavte **platnost metadat** v podobě počtu dnů.
    
    i. Klikněte na **Uložit změny** .

### <a name="create-pulse-secure-pcs-test-user"></a>Vytvořit testovacího uživatele pro Pulse zabezpečené počítače

V této části vytvoříte uživatele s názvem Britta Simon v počítačích Pulse Secure. Pracujte s [týmem podpory Pulse Secure PC](mailto:support@pulsesecure.net) a přidejte uživatele na platformě Pulse Secure PC Platform. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na přihlašovací adresu URL Pulse Secure PC, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL Pulse Secure počítače přímo a spustit tok přihlášení z tohoto účtu.

3. Můžete použít panel Microsoft Access. Když kliknete na dlaždici Pulse Secure PC na přístupovém panelu, přesměruje se na adresu URL pro přihlášení Pulse Secure PC. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po konfiguraci pulsně zabezpečených počítačů můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).