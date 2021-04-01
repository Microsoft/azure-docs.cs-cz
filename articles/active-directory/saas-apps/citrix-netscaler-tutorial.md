---
title: 'Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí systému Citrix ADC (ověřování pomocí protokolu Kerberos) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování (SSO) mezi Azure Active Directory a Citrix ADC pomocí ověřování založeného na protokolu Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 75d46edb332fb28132592e414e78bad64e75fef5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-kerberos-based-authentication"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí Citrix ADC (ověřování pomocí protokolu Kerberos)

V tomto kurzu se dozvíte, jak integrovat konektor Citrix ADC s Azure Active Directory (Azure AD). Když integruje Citrix ADC s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Citrix ADC.
* Umožněte uživatelům, aby se automaticky přihlásili k Citrix ADC pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Citrix ADC jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Tento kurz zahrnuje tyto scénáře:

* **SP-iniciované** Jednotné přihlašování pro Citrix ADC

* Zřizování uživatelů **jen v čase** pro Citrix ADC

* [Ověřování založené na protokolu Kerberos pro Citrix ADC](#publish-the-web-server)

* [Ověřování na základě hlaviček pro Citrix ADC](header-citrix-netscaler-tutorial.md#publish-the-web-server)


## <a name="add-citrix-adc-from-the-gallery"></a>Přidání programu Citrix ADC z Galerie

Pokud chcete integrovat službu Citrix ADC s Azure AD, nejdřív přidejte do seznamu spravovaných aplikací pro SaaS z Galerie nejprve Citrix ADC:

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

1. V nabídce vlevo vyberte **Azure Active Directory**.

1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.

1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Citrix ADC** .

1. Ve výsledcích vyberte **Citrix ADC** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Citrix ADC

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí systému Citrix ADC pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji Citrix ADC.

Při konfiguraci a testování jednotného přihlašování služby Azure AD pomocí nástroje Citrix ADC proveďte následující kroky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) – umožníte uživatelům používat tuto funkci.

    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) – k otestování jednotného přihlašování Azure AD pomocí B. Simon.

    1. [Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.

1. [Nakonfigurujte jednotné přihlašování pro Citrix ADC](#configure-citrix-adc-sso) – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.

    * [Vytvořte testovacího uživatele Citrix ADC](#create-a-citrix-adc-test-user) , abyste měli protějšek B. Simon v systému Citrix ADC, který je propojený s reprezentací uživatele Azure AD.

1. [Test SSO](#test-sso) – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování Azure AD pomocí Azure Portal, proveďte tyto kroky:

1. V Azure Portal v podokně integrace aplikace **Citrix ADC** v části **Spravovat** vyberte **jednotné přihlašování**.

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte ikonu pro **úpravu** pera **základní konfigurace SAML** pro úpravu nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** nakonfigurujte aplikaci v režimu **iniciované IDP** :

    1. Do textového pole **identifikátor** zadejte adresu URL, která má následující vzor: `https://<Your FQDN>`

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která má následující vzor: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Chcete-li nakonfigurovat aplikaci v režimu **iniciované SP** , vyberte možnost **nastavit další adresy URL** a proveďte následující krok:

    * Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která má následující vzor: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Adresy URL použité v této části nejsou reálné hodnoty. Aktualizujte tyto hodnoty skutečnými hodnotami pro identifikátor, adresu URL odpovědi a přihlašovací adresu URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienta Citrix ADC](https://www.citrix.com/contact/technical-support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.
    > * Aby bylo možné nastavit jednotné přihlašování, adresy URL musí být přístupné z veřejných webů. Abyste mohli token publikovat na konfigurované adrese URL, musíte povolit bránu firewall nebo jiná nastavení zabezpečení na straně Citrix ADC a enble službu Azure AD.

1. V podokně **nastavit jeden Sign-On s** podoknem SAML v části **podpisový certifikát SAML** pro **adresu URL FEDERAČNÍCH metadat aplikace** zkopírujte adresu URL a uložte ji do programu Poznámkový blok.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení systému Citrix ADC** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V nabídce vlevo v Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

1. V horní části podokna vyberte **Nový uživatel** .

1. V nastavení vlastnosti **uživatele** proveďte tyto kroky:

   1. Jako **název** zadejte `B.Simon` .  

   1. Jako **uživatelské jméno** zadejte _username@companydomain.extension_ . Například, `B.Simon@contoso.com`.

   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište nebo zkopírujte hodnotu zobrazenou v **hesle**.

   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte uživateli B. Simon používat jednotné přihlašování Azure tím, že uživatelům udělíte přístup k Citrix ADC.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.

1. V seznamu aplikace vyberte **Citrix ADC**.

1. V přehledu aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** . Zvolte **Vybrat**.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-citrix-adc-sso"></a>Konfigurace jednotného přihlašování k Citrix ADC

Vyberte odkaz pro kroky pro druh ověřování, které chcete konfigurovat:

- [Konfigurace jednotného přihlašování pro Citrix ADC pro ověřování pomocí protokolu Kerberos](#publish-the-web-server)

- [Konfigurace jednotného přihlašování pro Citrix ADC pro ověřování na základě hlaviček](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publikování webového serveru 

Vytvoření virtuálního serveru:

1. Vyberte   >  **služby Vyrovnávání zatížení** správy provozu  >  .
    
1. Vyberte **Přidat**.

    ![Konfigurace Citrix ADC – podokno služby](./media/citrix-netscaler-tutorial/web01.png)

1. Nastavte následující hodnoty webového serveru, na kterém běží aplikace:

   * **Název služby**
   * **Server IP/existující server**
   * **Protokol**
   * **Port**

### <a name="configure-the-load-balancer"></a>Konfigurace nástroje pro vyrovnávání zatížení

Konfigurace nástroje pro vyrovnávání zatížení:

1. Přejít na   >  virtuální servery **Vyrovnávání zatížení** pro správu provozu  >  .

1. Vyberte **Přidat**.

1. Nastavte následující hodnoty, jak je popsáno na následujícím snímku obrazovky:

    * **Název**
    * **Protokol**
    * **IP adresa**
    * **Port**

1. Vyberte **OK**.

    ![Konfigurace Citrix ADC – základní podokno nastavení](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Vytvoření vazby virtuálního serveru

Vytvoření vazby nástroje pro vyrovnávání zatížení s virtuálním serverem:

1. V podokně **služby a skupiny služeb** vyberte možnost **žádná vazba služby Virtual Server vyrovnávání zatížení**.

   ![Konfigurace Citrix ADC konfigurace – podokno vazby služby Virtual Server vyrovnávání zatížení](./media/citrix-netscaler-tutorial/bind01.png)

1. Ověřte nastavení, jak je znázorněno na následujícím snímku obrazovky, a pak vyberte **Zavřít**.

   ![Konfigurace serveru Citrix ADC – ověření vazby služeb virtuálního serveru](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Vytvoření vazby certifikátu

Pokud chcete tuto službu publikovat jako TLS, navažte certifikát serveru a pak otestujte svoji aplikaci:

1. V části **certifikát** vyberte možnost **žádný certifikát serveru**.

   ![Konfigurace Citrix ADC – podokno certifikát serveru](./media/citrix-netscaler-tutorial/bind03.png)

1. Ověřte nastavení, jak je znázorněno na následujícím snímku obrazovky, a pak vyberte **Zavřít**.

   ![Konfigurace Citrix ADC – ověření certifikátu](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil SAML pro Citrix ADC

Pokud chcete nakonfigurovat profil SAML ADC pro Citrix ADC, proveďte následující oddíly.

### <a name="create-an-authentication-policy"></a>Vytvoření zásad ověřování

Postup vytvoření zásad ověřování:

1. Přejít na **Security**  >  **AAA – zásady provozu** ověřování zásady  >    >    >  **ověřování**.

1. Vyberte **Přidat**.

1. V podokně **vytvořit zásadu ověřování** zadejte nebo vyberte následující hodnoty:

    * **Název**: zadejte název pro zásady ověřování.
    * **Akce**: zadejte **SAML** a pak vyberte **Přidat**.
    * **Výraz**: zadejte **hodnotu true**.     
    
    ![Konfigurace Citrix ADC – vytvoření podokna zásad ověřování](./media/citrix-netscaler-tutorial/policy01.png)

1. Vyberte **Vytvořit**.

### <a name="create-an-authentication-saml-server"></a>Vytvoření ověřování serveru SAML

Pokud chcete vytvořit server SAML ověřování, klikněte na podokno **vytvořit ověření serveru SAML** a pak proveďte následující kroky:

1. Do pole **název** zadejte název pro ověřování serveru SAML.

1. V části **exportovat metadata SAML**:

   1. Zaškrtněte políčko **importovat metadata** .

   1. Zadejte adresu URL federačních metadat z uživatelského rozhraní služby Azure SAML, které jste zkopírovali dříve.
    
1. Jako **název vystavitele** zadejte příslušnou adresu URL.

1. Vyberte **Vytvořit**.

![Konfigurace Citrix ADC – vytvoření ověřování v podokně serveru SAML](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Vytvoření virtuálního serveru pro ověřování

Vytvoření virtuálního serveru pro ověřování:

1.  Přejít na **Security**  >  **AAA – zásady provozu aplikací** ověřování  >    >    >  **virtuální servery**.

1.  Vyberte **Přidat** a pak proveďte následující kroky:

    1. Do pole **název** zadejte název virtuálního serveru pro ověřování.

    1. Zaškrtněte políčko **bez adres** .

    1. V případě **protokolu** vyberte možnost **SSL**.

    1. Vyberte **OK**.
    
1. Vyberte **Pokračovat**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurace virtuálního serveru pro ověřování pro použití Azure AD

Úprava dvou částí pro ověřovací virtuální server:

1.  V podokně **Upřesnit zásady ověřování** vyberte možnost **bez zásad ověřování**.

    ![Konfigurace Citrix ADC – podokno Upřesnit zásady ověřování](./media/citrix-netscaler-tutorial/virtual01.png)

1. V podokně **vazba zásad** vyberte zásadu ověřování a pak vyberte **svázat**.

    ![Konfigurace Citrix ADC – podokno vazby zásad](./media/citrix-netscaler-tutorial/virtual02.png)

1. V podokně **virtuální servery založené na formulářích** vyberte možnost **žádný virtuální server vyrovnávání zatížení**.

    ![Konfigurace systému Citrix ADC – podokno virtuální servery založené na formulářích](./media/citrix-netscaler-tutorial/virtual03.png)

1. Jako plně kvalifikovaný název domény pro **ověřování** zadejte plně kvalifikovaný název domény (FQDN) (povinné).

1. Vyberte virtuální server vyrovnávání zatížení, který chcete chránit pomocí ověřování Azure AD.

1. Vyberte **BIND**.

    ![Konfigurace Citrix ADC konfigurace – podokno vazby virtuálního serveru vyrovnávání zatížení](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Ujistěte se, že jste v podokně **Konfigurace ověřovacího virtuálního serveru** vybrali **Hotovo** .

1. Chcete-li ověřit změny, v prohlížeči přejdete na adresu URL aplikace. Měli byste vidět přihlašovací stránku tenanta místo neověřeného přístupu, který byste viděli předtím.

    ![Konfigurace Citrix ADC – přihlašovací stránka ve webovém prohlížeči](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-kerberos-based-authentication"></a>Konfigurace jednotného přihlašování pro Citrix ADC pro ověřování pomocí protokolu Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Vytvoření účtu delegování protokolu Kerberos pro Citrix ADC

1. Vytvořte uživatelský účet (v tomto příkladu používáme _AppDelegation_).

    ![Konfigurace pro Citrix ADC – podokno vlastností](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Nastavte pro tento účet hostitele hlavního názvu služby (SPN). 

    Příklad: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    V tomto příkladu:

    * `IDENTT.WORK` je plně kvalifikovaný název domény.
    * `identt` je název domény pro rozhraní NetBIOS.
    * `appdelegation` je název uživatelského účtu delegování.

1. Nakonfigurujte delegování pro webový server, jak je znázorněno na následujícím snímku obrazovky:
 
    ![Konfigurace Citrix ADC – delegování v podokně vlastnosti](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > V příkladu snímku obrazovky je interní název webového serveru, na kterém běží server s integrovaným ověřováním systému Windows (WIA), _CWEB2_.

### <a name="citrix-adc-aaa-kcd-kerberos-delegation-accounts"></a>Citrix ADC AAA KCD (účty delegování Kerberos)

Konfigurace účtu Citrix ADC AAA KCD:

1.  Přejít na účty **Citrix Gateway**  >  **AAA KCD (omezené delegování Kerberos)**.

1.  Vyberte **Přidat** a potom zadejte nebo vyberte následující hodnoty:

    * **Název**: zadejte název účtu KCD.

    * **Sféra**: zadejte doménu a rozšíření velkými písmeny.

    * **Hlavní název služby**: `http/<host/fqdn>@<DOMAIN.COM>` .
    
        > [!NOTE]
        > `@DOMAIN.COM` je povinné a musí být velká. Příklad: `http/cweb2@IDENTT.WORK`.

    * **Delegovaný uživatel**: zadejte delegované uživatelské jméno.

    * Zaškrtněte políčko **heslo pro delegovaného uživatele** a zadejte a potvrďte heslo.

1. Vyberte **OK**.
 
    ![Konfigurace Citrix ADC – konfigurace podokna účet KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Zásady provozu a profil přenosů Citrix

Konfigurace zásad provozu a přenosového profilu Citrix:

1.  Přejít na **Security**  >  **AAA –**  >  **zásady**  >  **provozu, profily a formuláře SSO ProfilesTraffic jednotného přihlašování**.

1.  Vyberte **profily přenosů dat**.

1.  Vyberte **Přidat**.

1.  Pokud chcete nakonfigurovat profil přenosů dat, zadejte nebo vyberte následující hodnoty.

    * **Název**: zadejte název pro profil přenosů dat.

    * **Jednotné přihlašování**: vyberte **zapnuto**.

    * **Účet KCD**: vyberte účet KCD, který jste vytvořili v předchozí části.

1. Vyberte **OK**.

    ![Konfigurace Citrix ADC – konfigurace podokna profil přenosu](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Vyberte **zásady provozu**.

1.  Vyberte **Přidat**.

1.  Pokud chcete nakonfigurovat zásady provozu, zadejte nebo vyberte následující hodnoty:

    * **Název**: zadejte název pro zásady provozu.

    * **Profil**: vyberte profil přenosů, který jste vytvořili v předchozí části.

    * **Výraz**: zadejte **hodnotu true**.

1. Vyberte **OK**.

    ![Konfigurace Citrix ADC – konfigurace podokna zásady provozu](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Vazba zásady provozu na virtuální server v Citrix

Svázání zásad provozu s virtuálním serverem pomocí grafického uživatelského rozhraní:

1. Přejít na   >  virtuální servery **Vyrovnávání zatížení** pro správu provozu  >  .

1. V seznamu virtuálních serverů vyberte virtuální server, na který chcete vytvořit zásadu přepisování, a pak vyberte **otevřít**.

1. V podokně **virtuální server vyrovnávání zatížení** v části **Upřesnit nastavení** vyberte **zásady**. V seznamu se zobrazí všechny zásady, které jsou nakonfigurované pro vaši instanci NetScaler.
 
    ![Konfigurace Citrix ADC konfigurace – podokno virtuálního serveru pro vyrovnávání zatížení](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Konfigurace Citrix ADC – dialogové okno zásady](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Zaškrtněte políčko vedle názvu zásady, kterou chcete vytvořit pomocí tohoto virtuálního serveru.
 
    ![Konfigurace Citrix ADC konfigurace – podokno vazby zásad provozu virtuálního serveru pro vyrovnávání zatížení](./media/citrix-netscaler-tutorial/kerberos09.png)

1. V dialogovém okně **zvolit typ** :

    1. V **Možnosti zvolit zásadu** vyberte **provoz**.

    1. Jako **Typ výběru** vyberte **požadavek**.

    ![Konfigurace pro Citrix ADC – volba podokna typ](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Když je zásada vázaná, vyberte **Hotovo**.
 
    ![Konfigurace pro Citrix ADC – podokno zásady](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Otestujte vazbu pomocí webu WIA.

    ![Konfigurace Citrix ADC – testovací stránka ve webovém prohlížeči](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-adc-test-user"></a>Vytvoření testovacího uživatele Citrix ADC

V této části se v nástroji Citrix ADC vytvoří uživatel nazvaný B. Simon. Citrix ADC podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel v nástroji Citrix ADC ještě neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory klienta Citrix ADC](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k systému Citrix ADC, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k systému Citrix ADC přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Citrix ADC v okně moje aplikace, přesměruje se na adresu URL pro přihlášení k systému Citrix ADC. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování systému Citrix ADC můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).