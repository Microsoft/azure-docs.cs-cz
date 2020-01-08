---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Citrix NetScaler (ověřování založené na protokolu Kerberos) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Citrix NetScaler (ověřování založené na protokolu Kerberos)

V tomto kurzu se dozvíte, jak integrovat Citrix NetScaler s Azure Active Directory (Azure AD). Když integrujete Citrix NetScaler s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Citrix NetScaler.
* Umožněte uživatelům, aby se automaticky přihlásili k Citrix NetScaler pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Citrix NetScaler jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Citrix NetScaler podporuje jednotné přihlašování iniciované v **SP**

* Citrix NetScaler podporuje zřizování uživatelů **jenom v čase**

- [Konfigurace jednotného přihlašování Citrix NetScaler pro ověřování založené na protokolu Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Konfigurace jednotného přihlašování Citrix NetScaler při ověřování na základě hlaviček](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Přidání Citrix NetScaler z Galerie

Pokud chcete nakonfigurovat integraci Citrix NetScaler do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Citrix NetScaler z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Citrix NetScaler** .
1. Z panelu výsledků vyberte **Citrix NetScaler** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Citrix NetScaler

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Citrix NetScaler pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Citrix NetScaler.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Citrix NetScaler, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Citrix NETSCALER SSO](#configure-citrix-netscaler-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si uživatele Citrix NetScaler Test User](#create-citrix-netscaler-test-user)** – můžete mít protějšek B. Simon v Citrix NetScaler, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Citrix NetScaler** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<<Your FQDN>>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klientů Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

    > [!NOTE]
    > Aby bylo možné získat jednotné přihlašování, měly by být tyto adresy URL přístupné z veřejných webů. Abyste mohli token publikovat na nakonfigurované adrese URL služby ACS, musíte povolit bránu firewall nebo jiná nastavení zabezpečení na straně NetScaler a enble službu Azure AD.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **adresu URL federačních metadat aplikace**, zkopírujte adresu URL a uložte ji do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Citrix NetScaler** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Citrix NetScaler.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Citrix NetScaler**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-citrix-netscaler-sso"></a>Konfigurace Citrix NetScaler SSO

- [Konfigurace jednotného přihlašování Citrix NetScaler pro ověřování založené na protokolu Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Konfigurace jednotného přihlašování Citrix NetScaler při ověřování na základě hlaviček](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Publikování webového serveru 

1. Vytvořte **virtuální server**.

    a. Přejít na **správu provozu > služby Vyrovnávání zatížení > Services**.
    
    b. Klikněte na tlačítko **Add** (Přidat).

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/web01.png)

    c. Zadejte podrobnosti webového serveru, na kterém běží aplikace níže:
    * **Název služby**
    * **Server IP/existující server**
    * **Protokol**
    * **Port**

     ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Konfigurace Load Balancer

1. Pokud chcete nakonfigurovat Load Balancer, proveďte následující kroky:

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/load01.png)

    a. Přejít na **správu provozu > vyrovnávání zatížení > virtuálních serverech**.

    b. Klikněte na tlačítko **Add** (Přidat).

    c. Zadejte následující podrobnosti:

    * **Název**
    * **Protokol**
    * **IP adresa**
    * **Port**
    * Klikněte na **OK** .

### <a name="bind-virtual-server"></a>Virtuální server BIND

Připojte Load Balancer k virtuálnímu serveru, který jste vytvořili dříve.

![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/bind01.png)

![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Certifikát vazby

Vzhledem k tomu, že budeme tuto službu publikovat jako SSL, propojíme certifikát serveru a pak otestujete svoji aplikaci.

![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/bind03.png)

![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil SAML pro Citrix ADC

### <a name="create-authentication-policy"></a>Vytvoření zásad ověřování

1. Přejít na **Security > AAA – data aplikací > zásady > ověřování > zásady ověřování**.

2. Klikněte na tlačítko **Přidat** a zadejte podrobnosti.

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/policy01.png)

    a. Název **zásad ověřování**.

    b. Výraz: **true**.

    c. Typ akce **SAML**.

    d. Akce = klikněte na **Přidat** (postupujte podle pokynů Průvodce vytvořením serveru SAML pro ověřování).
    
    e. V **zásadách ověřování**klikněte na vytvořit.

### <a name="create-authentication-saml-server"></a>Vytvoření ověřování serveru SAML

1. Proveďte následující kroky:

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/server01.png)

    a. Zadejte **název**.

    b. Importujte metadata (zadejte adresu URL federačních metadat z uživatelského rozhraní SAML Azure, které jste zkopírovali výše).
    
    c. Zadejte **název vystavitele**.

    d. Klikněte na **vytvořit**.

### <a name="create-authentication-virtual-server"></a>Vytvořit ověřovací virtuální server

1.  Přejít na **zabezpečení > AAA – přenos aplikací > > virtuálních serverech ověřování**.

2.  Klikněte na **Přidat** a proveďte následující kroky:

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/server02.png)

    a.  Zadejte **název**.

    b.  Vyberte možnost **bez možnosti řešení**.

    c.  Protokol **SSL**.

    d.  Klikněte na **OK**.

    e.  Klikněte na **Pokračovat**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurace virtuálního serveru pro ověřování pro použití Azure AD

Budete muset upravit 2 oddíly virtuálního serveru pro ověřování.

1.  **Rozšířené zásady ověřování**

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual01.png)

    a. Vyberte **zásady ověřování** , které jste vytvořili dříve.

    b. Klikněte na **BIND**.

      ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual02.png)

2. **Virtuální servery založené na formulářích**

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  Od svého vykonání uživatelského rozhraní bude nutné zadat **plně kvalifikovaný název domény** .

    b.  Vyberte **virtuální Server Load Balancer** , který chcete chránit pomocí ověřování Azure AD.

    c.  Klikněte na **BIND**.

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Ujistěte se také, že jste na stránce konfigurace virtuálního serveru pro ověřování **provedli práci** .

3. Ověřte změny. Přejděte na adresu URL aplikace. Měla by se zobrazit stránka pro přihlášení tenanta namísto neověřeného přístupu.

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>Konfigurace jednotného přihlašování Citrix NetScaler pro ověřování založené na protokolu Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Vytvoření účtu delegování protokolu Kerberos pro Citrix ADC

1. Vytvořte uživatelský účet (v tomto příkladu AppDelegation).

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos01.png)

2. Nastavte u těchto účtů hostitele hlavní název služby (SPN).

    * SetSPN-S HOST/AppDelegation. IDENTT. WORK identt\appdelegation
    
        V předchozím příkladu

        a. Identt. Work (plně kvalifikovaný název domény)

        b. Identt (název domény pro rozhraní NetBIOS)

        c. AppDelegation (název uživatelského účtu pro delegování)

3. Konfigurace delegování pro webserver 
 
    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >V příkladu nad interním názvem webového serveru, na kterém je spuštěný Server WIA, je cweb2

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA KCD (účty delegování Kerberos)

1.  V **účtu Citrix Gateway > AAA KCD (vynucené delegování protokolu Kerberos)** .

2.  Klikněte na Přidat a zadejte následující podrobnosti:

    a.  Zadejte **název**.

    b.  **Sféra**:

    c.  **Hlavní název služby**`http/<host/fqdn>@DOMAIN.COM`.
    
    >[!NOTE]
    >@DOMAIN.com je povinný a velkými písmeny.

    d.  Zadejte **delegovaný uživatelský účet**.

    e.  Ověřte heslo delegovaného uživatele a zadejte **heslo**.

    f.  Klikněte na **OK**.
 
    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Zásady provozu a profil přenosů Citrix

1.  Přejít na **zabezpečení > AAA – aplikační přenosy > zásady > zásady provozu, profily a zásady PROFILESTRAFFIC jednotného přihlašování**.

2.  Vyberte **profily přenosů dat**.

3.  Klikněte na tlačítko **Add** (Přidat).

4.  Nakonfigurujte profil přenosů dat.

    a.  Zadejte **název**.

    b.  Zadejte **jednotné přihlašování**.

    c.  V rozevíracím seznamu zadejte **účet KCD** vytvořený v předchozím kroku.

    d.  Klikněte na **OK**.

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  Vyberte **zásady provozu**.

6.  Klikněte na tlačítko **Add** (Přidat).

7.  Nakonfigurujte zásady provozu.

    a.  Zadejte **název**.

    b.  V rozevíracím seznamu vyberte dříve vytvořený **profil přenosů dat** .

    c.  Nastavte výraz na **hodnotu true**.

    d.  Klikněte na tlačítko **OK**.

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>Zásady provozu Citrix BIND na virtuální servery

Vazba zásad provozu na konkrétní virtuální server pomocí grafického uživatelského rozhraní.

* Přejděte do **správy provozu > vyrovnávání zatížení > virtuálních serverech**.

* V podokně podrobností v seznamu virtuálních serverů vyberte **virtuální server** , na který chcete vytvořit zásadu přepisování, a pak klikněte na **otevřít**.

* V dialogovém okně konfigurace virtuálního serveru (vyrovnávání zatížení) vyberte **kartu zásady**. Všechny zásady nakonfigurované v NetScaler se zobrazí v seznamu.
 
    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  **Zaškrtněte políčko vedle názvu** zásady, kterou chcete vytvořit pomocí tohoto virtuálního serveru.
 
    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Pouze zásada je vázána, klikněte na tlačítko **Hotovo**.
 
    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Otestujte pomocí integrovaného webu Windows.

    ![Konfigurace Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>Vytvoření testovacího uživatele Citrix NetScaler

V této části se v Citrix NetScaler vytvoří uživatel nazvaný B. Simon. Citrix NetScaler podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Citrix NetScaler neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory klienta Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Citrix NetScaler na přístupovém panelu byste měli být automaticky přihlášení k Citrix NetScaler, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte Citrix NetScaler s Azure AD](https://aad.portal.azure.com/)

- [Konfigurace jednotného přihlašování Citrix NetScaler při ověřování na základě hlaviček](header-citrix-netscaler-tutorial.md)
