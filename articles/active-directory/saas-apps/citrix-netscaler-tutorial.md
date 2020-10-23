---
title: 'Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí Citrix NetScaler (ověřování založené na protokolu Kerberos) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování (SSO) mezi Azure Active Directory a Citrix NetScaler pomocí ověřování založeného na protokolu Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.openlocfilehash: 8557c830aec2dd30de0a99a19d7950928d36e894
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456016"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí Citrix NetScaler (ověřování založené na protokolu Kerberos)

V tomto kurzu se dozvíte, jak integrovat Citrix NetScaler s Azure Active Directory (Azure AD). Když integrujete Citrix NetScaler s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Citrix NetScaler.
* Umožněte uživatelům, aby se automaticky přihlásili k Citrix NetScaler pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Citrix NetScaler jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Tento kurz zahrnuje tyto scénáře:

* **SP-iniciované** Jednotné přihlašování pro Citrix NetScaler

* Zřizování uživatelů **jen v čase** pro Citrix NetScaler

* [Ověřování založené na protokolu Kerberos pro Citrix NetScaler](#publish-the-web-server)

* [Ověřování na základě hlaviček pro Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

* Jakmile nakonfigurujete Citrix NetScaler, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-citrix-netscaler-from-the-gallery"></a>Přidání Citrix NetScaler z Galerie

Pokud chcete integrovat Citrix NetScaler s Azure AD, nejdřív přidejte Citrix NetScaler do seznamu spravovaných aplikací SaaS z Galerie:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

1. V nabídce vlevo vyberte **Azure Active Directory**.

1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.

1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Citrix NetScaler** .

1. Ve výsledcích vyberte **Citrix NetScaler**a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Citrix NetScaler

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Citrix NetScaler pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Citrix NetScaler.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Citrix NetScaler, dokončete následující stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) – umožníte uživatelům používat tuto funkci.

    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) – k otestování jednotného přihlašování Azure AD pomocí B. Simon.

    1. [Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.

1. [Nakonfigurujte Citrix NETSCALER SSO](#configure-citrix-netscaler-sso) – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.

    * [Vytvořte testovacího uživatele Citrix NetScaler](#create-a-citrix-netscaler-test-user) , abyste měli protějšek B. Simon v Citrix NetScaler, která je propojená se zastoupením uživatele v Azure AD.

1. [Test SSO](#test-sso) – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování Azure AD pomocí Azure Portal, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)v podokně integrace aplikací **Citrix NetScaler** v části **Spravovat**vyberte **jednotné přihlašování**.

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte ikonu pro **úpravu** pera **základní konfigurace SAML** pro úpravu nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** nakonfigurujte aplikaci v režimu **iniciované IDP** :

    1. Do textového pole **identifikátor** zadejte adresu URL, která má následující vzor: `https://<Your FQDN>`

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která má následující vzor: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Chcete-li nakonfigurovat aplikaci v režimu **iniciované SP** , vyberte možnost **nastavit další adresy URL** a proveďte následující krok:

    * Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která má následující vzor: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Adresy URL použité v této části nejsou reálné hodnoty. Aktualizujte tyto hodnoty skutečnými hodnotami pro identifikátor, adresu URL odpovědi a přihlašovací adresu URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienta Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.
    > * Aby bylo možné nastavit jednotné přihlašování, adresy URL musí být přístupné z veřejných webů. Abyste mohli token publikovat na konfigurované adrese URL, musíte povolit bránu firewall nebo jiné nastavení zabezpečení na straně Citrix NetScaler a enble službu Azure AD.

1. V podokně **nastavit jeden Sign-On s** podoknem SAML v části **podpisový certifikát SAML** pro **adresu URL FEDERAČNÍCH metadat aplikace**zkopírujte adresu URL a uložte ji do programu Poznámkový blok.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Citrix NetScaler** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V nabídce vlevo v Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

1. V horní části podokna vyberte **Nový uživatel** .

1. V nastavení vlastnosti **uživatele** proveďte tyto kroky:

   1. Jako **název**zadejte `B.Simon` .  

   1. Jako **uživatelské jméno**zadejte _username@companydomain.extension_ . Například, `B.Simon@contoso.com`.

   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište nebo zkopírujte hodnotu zobrazenou v **hesle**.

   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte uživateli B. Simon používat jednotné přihlašování Azure tím, že udělíte přístup k uživatelům Citrix NetScaler.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

1. V seznamu aplikace vyberte **Citrix NetScaler**.

1. V přehledu aplikace v části **Spravovat**vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** . Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak zvolte **Vybrat**.

1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurace Citrix NetScaler SSO

Vyberte odkaz pro kroky pro druh ověřování, které chcete konfigurovat:

- [Konfigurace služby Citrix NetScaler SSO pro ověřování pomocí protokolu Kerberos](#publish-the-web-server)

- [Konfigurace jednotného přihlašování pro Citrix NetScaler pro ověřování na základě hlaviček](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publikování webového serveru 

Vytvoření virtuálního serveru:

1. Vyberte **Traffic Management**  >  **služby Vyrovnávání zatížení**správy provozu  >  **Services**.
    
1. Vyberte **Přidat**.

    ![Konfigurace Citrix NetScaler – podokno služby](./media/citrix-netscaler-tutorial/web01.png)

1. Nastavte následující hodnoty webového serveru, na kterém běží aplikace:

   * **Název služby**
   * **Server IP/existující server**
   * **Protokol**
   * **Port**

### <a name="configure-the-load-balancer"></a>Konfigurace nástroje pro vyrovnávání zatížení

Konfigurace nástroje pro vyrovnávání zatížení:

1. Přejít na **Traffic Management**  >  virtuální servery**Vyrovnávání zatížení**pro správu provozu  >  **Virtual Servers**.

1. Vyberte **Přidat**.

1. Nastavte následující hodnoty, jak je popsáno na následujícím snímku obrazovky:

    * **Název**
    * **Protokol**
    * **IP adresa**
    * **Port**

1. Vyberte **OK**.

    ![Konfigurace Citrix NetScaler – podokno nastavení Basic](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Vytvoření vazby virtuálního serveru

Vytvoření vazby nástroje pro vyrovnávání zatížení s virtuálním serverem:

1. V podokně **služby a skupiny služeb** vyberte možnost **žádná vazba služby Virtual Server vyrovnávání zatížení**.

   ![Konfigurace Citrix NetScaler – podokno vazby služby virtuálního serveru pro vyrovnávání zatížení](./media/citrix-netscaler-tutorial/bind01.png)

1. Ověřte nastavení, jak je znázorněno na následujícím snímku obrazovky, a pak vyberte **Zavřít**.

   ![Konfigurace Citrix NetScaler – ověření vazby služeb virtuálního serveru](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Vytvoření vazby certifikátu

Pokud chcete tuto službu publikovat jako TLS, navažte certifikát serveru a pak otestujte svoji aplikaci:

1. V části **certifikát**vyberte možnost **žádný certifikát serveru**.

   ![Konfigurace Citrix NetScaler – podokno certifikát serveru](./media/citrix-netscaler-tutorial/bind03.png)

1. Ověřte nastavení, jak je znázorněno na následujícím snímku obrazovky, a pak vyberte **Zavřít**.

   ![Konfigurace Citrix NetScaler – ověření certifikátu](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil SAML pro Citrix ADC

Pokud chcete nakonfigurovat profil SAML ADC pro Citrix ADC, proveďte následující oddíly.

### <a name="create-an-authentication-policy"></a>Vytvoření zásad ověřování

Postup vytvoření zásad ověřování:

1. Přejít na **Security**  >  **AAA – zásady provozu**ověřování zásady  >  **Policies**  >  **Authentication**  >  **ověřování**.

1. Vyberte **Přidat**.

1. V podokně **vytvořit zásadu ověřování** zadejte nebo vyberte následující hodnoty:

    * **Název**: zadejte název pro zásady ověřování.
    * **Akce**: zadejte **SAML**a pak vyberte **Přidat**.
    * **Výraz**: zadejte **hodnotu true**.     
    
    ![Konfigurace Citrix NetScaler – vytvoření podokna zásad ověřování](./media/citrix-netscaler-tutorial/policy01.png)

1. Vyberte **Vytvořit**.

### <a name="create-an-authentication-saml-server"></a>Vytvoření ověřování serveru SAML

Pokud chcete vytvořit server SAML ověřování, klikněte na podokno **vytvořit ověření serveru SAML** a pak proveďte následující kroky:

1. Do pole **název**zadejte název pro ověřování serveru SAML.

1. V části **exportovat metadata SAML**:

   1. Zaškrtněte políčko **importovat metadata** .

   1. Zadejte adresu URL federačních metadat z uživatelského rozhraní služby Azure SAML, které jste zkopírovali dříve.
    
1. Jako **název vystavitele**zadejte příslušnou adresu URL.

1. Vyberte **Vytvořit**.

![Konfigurace Citrix NetScaler – vytvoření ověřování v podokně serveru SAML](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Vytvoření virtuálního serveru pro ověřování

Vytvoření virtuálního serveru pro ověřování:

1.  Přejít na **Security**  >  **AAA – zásady provozu aplikací**ověřování  >  **Policies**  >  **Authentication**  >  **virtuální servery**.

1.  Vyberte **Přidat**a pak proveďte následující kroky:

    1. Do pole **název**zadejte název virtuálního serveru pro ověřování.

    1. Zaškrtněte políčko **bez adres** .

    1. V případě **protokolu**vyberte možnost **SSL**.

    1. Vyberte **OK**.
    
1. Vyberte **Pokračovat**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurace virtuálního serveru pro ověřování pro použití Azure AD

Úprava dvou částí pro ověřovací virtuální server:

1.  V podokně **Upřesnit zásady ověřování** vyberte možnost **bez zásad ověřování**.

    ![Konfigurace Citrix NetScaler – podokno pokročilé zásady ověřování](./media/citrix-netscaler-tutorial/virtual01.png)

1. V podokně **vazba zásad** vyberte zásadu ověřování a pak vyberte **svázat**.

    ![Konfigurace Citrix NetScaler – podokno vazby zásad](./media/citrix-netscaler-tutorial/virtual02.png)

1. V podokně **virtuální servery založené na formulářích** vyberte možnost **žádný virtuální server vyrovnávání zatížení**.

    ![Konfigurace Citrix NetScaler – podokno virtuální servery založené na formulářích](./media/citrix-netscaler-tutorial/virtual03.png)

1. Jako plně kvalifikovaný název domény pro **ověřování**zadejte plně kvalifikovaný název domény (FQDN) (povinné).

1. Vyberte virtuální server vyrovnávání zatížení, který chcete chránit pomocí ověřování Azure AD.

1. Vyberte **BIND**.

    ![Konfigurace Citrix NetScaler – podokno vazby virtuálního serveru pro vyrovnávání zatížení](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Ujistěte se, že jste v podokně **Konfigurace ověřovacího virtuálního serveru** vybrali **Hotovo** .

1. Chcete-li ověřit změny, v prohlížeči přejdete na adresu URL aplikace. Měli byste vidět přihlašovací stránku tenanta místo neověřeného přístupu, který byste viděli předtím.

    ![Konfigurace Citrix NetScaler – přihlašovací stránka ve webovém prohlížeči](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Konfigurace služby Citrix NetScaler SSO pro ověřování pomocí protokolu Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Vytvoření účtu delegování protokolu Kerberos pro Citrix ADC

1. Vytvořte uživatelský účet (v tomto příkladu používáme _AppDelegation_).

    ![Konfigurace Citrix NetScaler – podokno vlastností](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Nastavte pro tento účet hostitele hlavního názvu služby (SPN). 

    Příklad: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    V tomto příkladu:

    * `IDENTT.WORK` je plně kvalifikovaný název domény.
    * `identt` je název domény pro rozhraní NetBIOS.
    * `appdelegation` je název uživatelského účtu delegování.

1. Nakonfigurujte delegování pro webový server, jak je znázorněno na následujícím snímku obrazovky:
 
    ![Konfigurace Citrix NetScaler – delegování v podokně vlastnosti](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > V příkladu snímku obrazovky je interní název webového serveru, na kterém běží server s integrovaným ověřováním systému Windows (WIA), _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (účty delegování Kerberos)

Konfigurace účtu Citrix NetScaler AAA KCD:

1.  Přejít na účty **Citrix Gateway**  >  **AAA KCD (omezené delegování Kerberos)**.

1.  Vyberte **Přidat**a potom zadejte nebo vyberte následující hodnoty:

    * **Název**: zadejte název účtu KCD.

    * **Sféra**: zadejte doménu a rozšíření velkými písmeny.

    * **Hlavní název služby**: `http/<host/fqdn>@<DOMAIN.COM>` .
    
        > [!NOTE]
        > `@DOMAIN.COM` je povinné a musí být velká. Příklad: `http/cweb2@IDENTT.WORK`.

    * **Delegovaný uživatel**: zadejte delegované uživatelské jméno.

    * Zaškrtněte políčko **heslo pro delegovaného uživatele** a zadejte a potvrďte heslo.

1. Vyberte **OK**.
 
    ![Konfigurace Citrix NetScaler – konfigurace podokna účet KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

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

    ![Konfigurace Citrix NetScaler – konfigurace podokna profil přenosu](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Vyberte **zásady provozu**.

1.  Vyberte **Přidat**.

1.  Pokud chcete nakonfigurovat zásady provozu, zadejte nebo vyberte následující hodnoty:

    * **Název**: zadejte název pro zásady provozu.

    * **Profil**: vyberte profil přenosů, který jste vytvořili v předchozí části.

    * **Výraz**: zadejte **hodnotu true**.

1. Vyberte **OK**.

    ![Konfigurace Citrix NetScaler – konfigurace podokna zásady provozu](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Vazba zásady provozu na virtuální server v Citrix

Svázání zásad provozu s virtuálním serverem pomocí grafického uživatelského rozhraní:

1. Přejít na **Traffic Management**  >  virtuální servery**Vyrovnávání zatížení**pro správu provozu  >  **Virtual Servers**.

1. V seznamu virtuálních serverů vyberte virtuální server, na který chcete vytvořit zásadu přepisování, a pak vyberte **otevřít**.

1. V podokně **virtuální server vyrovnávání zatížení** v části **Upřesnit nastavení**vyberte **zásady**. V seznamu se zobrazí všechny zásady, které jsou nakonfigurované pro vaši instanci NetScaler.
 
    ![Konfigurace Citrix NetScaler – podokno virtuálního serveru pro vyrovnávání zatížení](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Konfigurace Citrix NetScaler – dialogové okno zásady](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Zaškrtněte políčko vedle názvu zásady, kterou chcete vytvořit pomocí tohoto virtuálního serveru.
 
    ![Konfigurace Citrix NetScaler – podokno vazby zásad provozu virtuálního serveru pro vyrovnávání zatížení](./media/citrix-netscaler-tutorial/kerberos09.png)

1. V dialogovém okně **zvolit typ** :

    1. V **Možnosti zvolit zásadu**vyberte **provoz**.

    1. Jako **Typ výběru**vyberte **požadavek**.

    ![Konfigurace Citrix NetScaler – volba podokna typ](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Když je zásada vázaná, vyberte **Hotovo**.
 
    ![Konfigurace Citrix NetScaler – podokno zásady](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Otestujte vazbu pomocí webu WIA.

    ![Konfigurace Citrix NetScaler – testovací stránka ve webovém prohlížeči](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Vytvoření testovacího uživatele Citrix NetScaler

V této části se v Citrix NetScaler vytvoří uživatel nazvaný B. Simon. Citrix NetScaler podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel ještě v Citrix NetScaler neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory pro klienta Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když vyberete dlaždici Citrix NetScaler na přístupovém panelu, měli byste se automaticky přihlásit k NetScaler Citrix, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte Citrix NetScaler s Azure AD](https://aad.portal.azure.com/)

- [Konfigurace jednotného přihlašování Citrix NetScaler při ověřování na základě hlaviček](header-citrix-netscaler-tutorial.md)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Postup ochrany Citrix NetScaler s pokročilou viditelností a ovládacími prvky](/cloud-app-security/proxy-intro-aad)