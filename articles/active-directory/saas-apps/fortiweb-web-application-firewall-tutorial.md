---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s firewallem webových aplikací FortiWeb | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FortiWeb Firewall webových aplikací.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: b2da377ac6498940ed2fc260ad89494f13803ba5
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s bránou firewall webových aplikací FortiWeb

V tomto kurzu se dozvíte, jak integrovat bránu firewall webových aplikací FortiWeb s Azure Active Directory (Azure AD). Když integrujete bránu firewall webových aplikací FortiWeb s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k firewallu webových aplikací FortiWeb.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k FortiWeb firewallu webových aplikací pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* FortiWeb odběr brány firewall webových aplikací jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Firewall webových aplikací FortiWeb podporuje jednotné přihlašování (SSO) iniciované v **SP** .

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Přidání firewallu webových aplikací FortiWeb z Galerie

Pokud chcete nakonfigurovat integraci brány firewall webových aplikací FortiWeb do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat bránu firewall webových aplikací FortiWeb z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **FortiWeb Firewall webových aplikací** .
1. Z panelu výsledků vyberte **FortiWeb Firewall webových aplikací** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro bránu firewall webových aplikací FortiWeb

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí brány firewall webových aplikací FortiWeb pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v bráně firewall webových aplikací FortiWeb.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí brány firewall webových aplikací FortiWeb, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování brány firewall webových aplikací FortiWeb](#configure-fortiweb-web-application-firewall-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele firewallu webových aplikací FortiWeb](#create-fortiweb-web-application-firewall-test-user)** – Pokud chcete mít protějšek B. Simon v bráně firewall webových aplikací FortiWeb, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **firewall webové aplikace FortiWeb** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://www.<CUSTOMER_DOMAIN>.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.<CUSTOMER_DOMAIN>.com`

    d. Do textového pole **odhlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` je identifikátor názvu, který se použije později při zadávání konfigurace do FortiWeb.
    > Chcete-li získat reálné hodnoty adresy URL, obraťte se na [tým podpory firewallu webových aplikací FortiWeb](mailto:support@fortinet.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)


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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k firewallu webových aplikací FortiWeb.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **FortiWeb Firewall webových aplikací**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Konfigurace jednotného přihlašování firewallu webových aplikací FortiWeb

1.  Přejděte na `https://<address>:8443` místo, kde `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači FortiWeb.

2.  Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiWeb.

1. Na následující stránce proveďte následující kroky.

    ![Stránka serveru SAML](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  V nabídce vlevo klikněte na **uživatel**.

    b.  V části uživatel klikněte na **vzdálený server**.

    c.  Klikněte na možnost **Server SAML**.

    d.  Klikněte na **vytvořit nový**.

    e.  Do pole **název** zadejte hodnotu pro `<fwName>` použití v části Konfigurace služby Azure AD.

    f.  Do textového pole **ID entity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    například Vedle pole **metadata** klikněte na **zvolit soubor** a vyberte soubor **XML s federačními metadaty** , který jste stáhli z Azure Portal.

    h.  Klikněte na **OK**.

### <a name="create-a-site-publishing-rule"></a>Vytvoření pravidla publikování lokality

1.  Přejděte na `https://<address>:8443` místo, kde `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači FortiWeb.

1.  Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiWeb.
1. Na následující stránce proveďte následující kroky.

    ![Pravidlo publikování lokality](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  V nabídce na levé straně klikněte na **doručení aplikace**.
    
    b.  V části **doručování aplikací** klikněte na **Publikovat web**.
    
    c.  V části **publikování webu** klikněte na **Publikovat web**.
    
    d.  Klikněte na tlačítko **pravidlo publikování webu**.
    
    e.  Klikněte na **vytvořit nový**.
    
    f.  Zadejte název pravidla publikování lokality.
    
    například  Vedle **pole publikovaný typ webu** klikněte na možnost **regulární výraz**.
    
    i.  Vedle **publikovaného webu** zadejte řetězec, který se bude shodovat s hlavičkou hostitele webu, který publikujete.
    
    j.  V poli **cesta** zadejte a/.
    
    k.  V poli **metoda ověřování klienta** vyberte **ověřování SAML**.
    
    l.  V rozevíracím seznamu **Server SAML** vyberte server SAML, který jste vytvořili dříve.
    
    m.  Klikněte na **OK**.


### <a name="create-a-site-publishing-policy"></a>Vytvoření zásady publikování webu

1.  Přejděte na `https://<address>:8443` místo, kde `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači FortiWeb.

2.  Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiWeb.

1. Na následující stránce proveďte následující kroky.

    ![Zásada publikování lokality](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  V nabídce na levé straně klikněte na **doručení aplikace**.

    b.  V části **doručování aplikací** klikněte na **Publikovat web**.

    c.  V části **publikování webu** klikněte na **Publikovat web**.

    d.  Klikněte na položku **zásady publikování webu**.

    e.  Klikněte na **vytvořit nový**.

    f.  Zadejte název pro zásadu publikování lokality.

    například  Klikněte na **OK**.

    h.  Klikněte na **vytvořit nový**.

    i.  V rozevíracím seznamu **pravidlo** vyberte pravidlo publikování lokality, které jste vytvořili dříve.

    j.  Klikněte na **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Vytvoření a přiřazení profilu webové ochrany

1.  Přejděte na `https://<address>:8443` místo, kde `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači FortiWeb.

2.  Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiWeb.
3.  V nabídce na levé straně klikněte na **zásady**.
4.  V části **zásady** klikněte na **Profil webové ochrany**.
5.  Klikněte na možnost **vložená standardní ochrana** a klikněte na **klonovat**.
6.  Zadejte název nového profilu webové ochrany a klikněte na tlačítko **OK**.
7.  Vyberte nový profil webové ochrany a klikněte na **Upravit**.
8.  U pole **publikovat na webu** vyberte zásadu publikování lokality, kterou jste vytvořili dříve.
9.  Klikněte na **OK**.
 
    ![publikování webu](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. V nabídce na levé straně klikněte na **zásady**.
11. V části **zásady** klikněte na **zásady serveru**.
12. Vyberte zásady serveru používané k publikování webu, pro který chcete použít Azure Active Directory pro ověřování.
13. Klikněte na **Upravit**.
14. V rozevíracím seznamu **Profil webové ochrany** vyberte profil webové ochrany, který jste právě vytvořili.
15. Klikněte na **OK**.
16. Pokus o přístup k externí adrese URL, na kterou FortiWeb publikuje Web. Pro ověřování byste měli být přesměrováni na Azure Active Directory.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Vytvořit testovacího uživatele firewallu webových aplikací FortiWeb

V této části vytvoříte uživatele s názvem Britta Simon v bráně firewall webových aplikací FortiWeb. Pokud chcete přidat uživatele na platformě brány firewall webových aplikací FortiWeb, pracujte se [seskupením podpory firewallu webových aplikací FortiWeb](mailto:support@fortinet.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k webové aplikaci FortiWeb, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení do webové aplikace FortiWeb přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici webové aplikace FortiWeb v části Moje aplikace se přesměruje na přihlašovací adresu URL webové aplikace FortiWeb. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování firewallu webových aplikací FortiWeb můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


