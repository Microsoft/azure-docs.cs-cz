---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s integrací služby Azure AD v kemp LoadMaster | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a kemp LoadMaster integrací Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 68869d464df01d3cc89493c64d66511b4a6f369d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299987"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s integrací služby Azure AD v kemp LoadMaster

V tomto kurzu se dozvíte, jak integrovat integraci služby Azure AD kemp LoadMaster s Azure Active Directory (Azure AD). Když integruje integraci služby Azure AD kemp LoadMaster s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k integraci služby Azure AD v kemp LoadMaster.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke službě kemp LoadMaster integrací Azure AD s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Kemp LoadMaster, předplatné služby Azure AD Integration jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Integrace Azure AD kemp LoadMaster podporuje **IDP** iniciované jednotného přihlašování.
* Jakmile nakonfigurujete integraci služby Azure AD kemp LoadMaster, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Přidání integrace služby Azure AD kemp LoadMaster z Galerie

Pokud chcete nakonfigurovat integraci služby Azure AD kemp LoadMaster do služby Azure AD, musíte do svého seznamu spravovaných aplikací přidat kemp LoadMaster Azure AD Integration z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **kemp LoadMaster Integration Azure AD** .
1. Vyberte **kemp LoadMaster Integration Azure AD** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro integraci služby Azure AD v kemp LoadMaster

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s kemp LoadMaster integrací Azure AD pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v integraci služby Azure AD kemp LoadMaster.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí integrace služby Azure AD kemp LoadMaster, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné PŘIhlašování pro integraci služby Azure AD kemp LoadMaster](#configure-kemp-loadmaster-azure-ad-integration-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.

1. **[Publikování webového serveru](#publishing-web-server)**
    1. **[Vytvoření virtuální služby](#create-a-virtual-service)**
    1. **[Certifikáty a zabezpečení](#certificates-and-security)**
    1. **[Kemp LoadMaster – profil SAML pro integraci Azure AD](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Ověření změn](#verify-the-changes)**
1. **[Konfigurace ověřování založeného na protokolu Kerberos](#configuring-kerberos-based-authentication)**
    1. **[Vytvoření účtu delegování protokolu Kerberos pro integraci služby Azure AD v kemp LoadMaster](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp LoadMaster Integration KCD Azure AD (účty delegování Kerberos)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Integration ESP Azure AD](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Vytvořte si test kemp LoadMaster pro Azure AD Integration User](#create-kemp-loadmaster-azure-ad-integration-test-user)** – abyste měli protějšek B. Simon v kemp LoadMaster integraci služby Azure AD, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Integration Application kemp LoadMaster služby Azure AD** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory kemp LoadMaster pro Azure AD Integration Client](mailto:support@kemp.ax) . Můžete se také podívat na vzory uvedené v části základní konfigurace SAML v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a **soubor XML federačních metadat**, vyberte **Stáhnout** a Stáhněte si certifikát a soubory XML federačních metadat a uložte je do počítače.

    ![Odkaz na stažení certifikátu](./media/kemp-tutorial/certificate-base-64.png)

1. V části **Nastavení integrace služby Azure AD kemp LoadMaster** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k integraci kemp LoadMaster Azure AD.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **kemp LoadMaster Integration Azure AD**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Konfigurace jednotného přihlašování pro integraci služby Azure AD kemp LoadMaster

## <a name="publishing-web-server"></a>Publikování webového serveru 

### <a name="create-a-virtual-service"></a>Vytvoření virtuální služby 

1. Na webu kemp LoadMaster Integration LoadMaster web UI Azure AD > Virtual Services > přidat nový.

1. Klikněte na Přidat nový.

1. Zadejte parametry pro virtuální službu.

    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-1.png)

    a. Virtuální adresa
    
    b. Port
    
    c. Název služby (volitelné)
    
    d. Protokol 

1. Přejděte do části reálné servery.

1. Klikněte na Přidat nový.

1. Zadejte parametry reálného serveru.
    
    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-2.png)

    a. Vyberte možnost povolení vzdálených adres.
    
    b. Zadejte adresu reálného serveru.
    
    c. Port
    
    d. Forward – Metoda
    
    e. Hmotnost
    
    f. Limit připojení
    
    například Klikněte na Přidat tento skutečný server.

## <a name="certificates-and-security"></a>Certifikáty a zabezpečení
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Import certifikátu v integraci služby Azure AD v kemp LoadMaster 
 
1. Přejít na web kemp LoadMaster Integration Web Portal služby Azure AD > certifikáty & zabezpečení > certifikátů SSL.

1. V části Správa certifikátů > konfigurace certifikátů.

1. Klikněte na importovat certifikát.

1. Zadejte název souboru, který obsahuje certifikát. Soubor může také obsahovat privátní klíč. Pokud soubor neobsahuje privátní klíč, je nutné zadat také soubor, který obsahuje privátní klíč. Certifikát může být v jednom z těchto. PEM nebo. Formát PFX (IIS).

1. Klikněte na vybrat soubor na soubor certifikátu.

1. Klikněte na soubor klíče (volitelné).

1. Klikněte na Uložit.

### <a name="ssl-acceleration"></a>Akcelerace SSL
 
1. Přejít na webové uživatelské rozhraní kemp LoadMaster > služby Virtual Services > Zobrazit/upravit služby.

1. V části operace klikněte na Upravit.

1. Klikněte na vlastnosti SSL (které fungují ve vrstvě 7).
    
    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-3.png)
    
    a. V akceleraci SSL klikněte na zapnuto.
    
    b. V části dostupné certifikáty vyberte importovaný certifikát a klikněte na `>` symbol.
    
    c. Jakmile se požadovaný certifikát SSL zobrazí v části přiřazené certifikáty, klikněte na **nastavit certifikáty**.

    > [!NOTE]
    > Ujistěte se, že kliknete na **nastavit certifikáty**.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Kemp LoadMaster – profil SAML pro integraci Azure AD
 
### <a name="import-idp-certificate"></a>Importovat certifikát IdP

Přejít na webovou konzolu integrace služby Azure AD v kemp LoadMaster 

1. Klikněte na zprostředkující certifikáty v části certifikáty a autorita.

    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-6.png)

    a. Klikněte na zvolit soubor v Přidat nový zprostředkující certifikát.
    
    b. Přejděte do souboru certifikátu, který jste předtím stáhli z aplikace Azure AD Enterprise.
    
    c. Klikněte na otevřít.
    
    d. Zadejte název v názvu certifikátu.
    
    e. Klikněte na Přidat certifikát.

### <a name="create-authentication-policy"></a>Vytvoření zásad ověřování 
 
V části virtuální služby můžete spravovat jednotné přihlašování.

   ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-7.png)
   
   a. Po přidělení názvu klikněte na Přidat v části Přidat novou konfiguraci na straně klienta.

   b. V části protokol ověřování vyberte SAML.

   c. V části zřizování IdP vyberte soubor metadat. 

   d. Klikněte na zvolit soubor.

   e. Přejít na dříve stažený kód XML z Azure Portal.

   f. Klikněte na otevřít a pak klikněte na Importovat soubor metadat IdP.

   například Vyberte zprostředkující certifikát z IdP certifikátu.

   h. Nastavte ID entity SP, které by se mělo shodovat s identitou vytvořenou v Azure Portal 

   i. Klikněte na nastavit ID entity SP.

### <a name="set-authentication"></a>Nastavení ověřování  
 
Webová konzola integrace služby Azure AD v kemp LoadMaster

1. Klikněte na virtuální služby.

1. Klikněte na Zobrazit/upravit služby.

1. Klikněte na Upravit a přejděte na možnosti ESP.
    
    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-8.png)

    a. Klikněte na Povolit protokol ESP.
    
    b. V režimu ověřování klientů vyberte SAML.
    
    c. Vyberte dříve vytvořené ověřování na straně klienta v doméně jednotného přihlašování.
    
    d. V povolených virtuálních hostitelích zadejte název hostitele a klikněte na nastavit povolené virtuální hostitele.
    
    e. Zadejte/* v povolených virtuálních adresářích (na základě požadavků na přístup) a klikněte na nastavit povolené adresáře.

### <a name="verify-the-changes"></a>Ověření změn 
 
Přejít na adresu URL aplikace 

V minulosti by se měla zobrazit stránka pro přihlášení klienta namísto neověřeného přístupu. 

![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Konfigurace ověřování založeného na protokolu Kerberos 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Vytvoření účtu delegování protokolu Kerberos pro integraci služby Azure AD v kemp LoadMaster 

1. Vytvořte uživatelský účet (v tomto příkladu AppDelegation).
    
    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-10.png)


    a. Vyberte kartu Editor atributů.

    b. Přejděte na servicePrincipalName. 

    c. Vyberte servicePrincipalName a klikněte na Upravit.

    d. Do pole Přidat hodnotu zadejte http/kcduser a klikněte na Přidat. 

    e. Klikněte na použít a pak na OK. Okno je nutné před opětovným otevřením zavřít (aby se zobrazila nová karta delegování). 

1. Otevřete znovu okno Vlastnosti uživatele a karta delegování bude k dispozici. 

1. Vyberte kartu Delegování.

    ![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-11.png)

    a. Vyberte Důvěřovat tomuto uživateli pro delegování pouze určeným službám.

    b. Vyberte možnost pro použití libovolného protokolu pro ověřování.

    c. Přidejte skutečné servery a jako službu přidejte http. 
    
    d. Zaškrtněte políčko Rozšířené. 

    e. Můžete zobrazit všechny servery s názvem hostitele a plně kvalifikovaným názvem domény.
    
    f. Klikněte na OK.

> [!NOTE]
> Podle potřeby nastavte hlavní název služby (SPN) na aplikaci nebo na webu. Pro přístup k aplikaci, když je nastavena identita fondu aplikací. Chcete-li získat přístup k aplikaci služby IIS pomocí názvu FQDN, přejděte na reálný příkazový řádek a zadejte příkaz SetSpn s požadovanými parametry. Například SetSPN – S HTTP/sescoindc. sunehes. co. v suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Integration KCD Azure AD (účty delegování Kerberos) 

Navštivte web kemp LoadMaster Integration webová Konzola služby Azure AD > služby Virtual Services > spravovat jednotné přihlašování.

![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-12.png)

a. Přejděte na konfigurace jednotného přihlašování na straně serveru.

b. Do rozevíracího okna Přidat novou konfiguraci na straně serveru a klikněte na Přidat.

c. V ověřovacím protokolu vyberte omezené delegování protokolu Kerberos.

d. Do sféry protokolu Kerberos zadejte název domény.

e. Klikněte na nastavit sféru protokolu Kerberos.

f. Zadejte IP adresu řadiče domény v služba KDC (Key Distribution Center) protokolu Kerberos.

například Klikněte na nastavit službu Kerberos KDC.

h. Do názvu důvěryhodného uživatelského jména protokolu Kerberos zadejte KCD uživatelské jméno.

i. Klikněte na nastavit důvěryhodné uživatelské jméno služby KDC.

j. Do hesla důvěryhodného uživatele protokolu Kerberos zadejte heslo.

k. Klikněte na nastavit důvěryhodné heslo uživatele KCD.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Integration ESP Azure AD 

Přejít na virtuální služby > Zobrazit/upravit služby.

![Kemp LoadMaster, webserver Integrace Azure AD](./media/kemp-tutorial/kemp-13.png)

a. Klikněte na Upravit na Přezdívka virtuální služby.
    
b. Klikněte na možnost ESP.
    
c. V části režim ověřování serveru vyberte KCD.
        
d. V části konfigurace na straně serveru vyberte dříve vytvořený profil na straně serveru.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Vytvoření testovacího uživatele integrace služby Azure AD v kemp LoadMaster

V této části vytvoříte uživatele s názvem B. Simon v integraci kemp LoadMaster Azure AD. Pokud chcete přidat uživatele na kemp LoadMaster integrační platformě Azure AD, pracujte s [týmem podpory kemp LoadMaster pro klienta pro integraci Azure AD](mailto:support@kemp.ax) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici integrace služby Azure AD kemp LoadMaster, měli byste se automaticky přihlášeni k integraci Azure AD kemp LoadMaster, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte kemp LoadMaster integrací Azure AD se službou Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit kemp LoadMaster integrací Azure AD s pokročilou viditelností a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
