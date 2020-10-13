---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s F5 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: d85e6bbaf85ebb84d3664d90e3d645f61b2cb60a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91817370"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Kurz: Konfigurace jednotného přihlašování (SSO) mezi Azure Active Directory a F5

V tomto kurzu se naučíte integrovat F5 s Azure Active Directory (Azure AD). Při integraci F5 se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k F5.
* Umožněte uživatelům, aby se automaticky přihlásili k F5 pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s jednotným přihlašováním ve službě Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

* Předplatné s povoleným jednotným přihlašováním (SSO).

* Nasazení společného řešení vyžaduje následující licenci:

    * ® Nejlepší sada pro BIG-IP 

    * F5 – samostatná licence pro správce zásad pro BIG-IP Access™ (APM) 

    * F5 licence na™ (APM) pro správce zásad pro Big-IP Access v existující® místní Traffic Manager™ (LTM).

    * Kromě výše uvedených licencí může být systém F5 také licencován: 

        * Adresa URL pro filtrování předplatného pro použití databáze kategorií URL

        * Předplatné funkce F5 IP Intelligence pro detekci a blokování známých útočníci a škodlivých přenosů
     
        * Modul hardwarového zabezpečení (HSM), který chrání a spravuje digitální klíče pro silné ověřování

* Systém F5 BIG-IP System je zřízený s moduly APM (LTM je volitelné).

* I když je volitelná, důrazně doporučujeme nasadit systémy F5 do [skupiny zařízení Sync/převzetí služeb při selhání](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), která zahrnuje aktivní pohotovostní režim s plovoucí IP adresou pro vysokou dostupnost (ha). Další redundanci rozhraní je možné dosáhnout pomocí LACP (Link Aggregator Control Protocol). LACP spravuje připojená fyzická rozhraní jako jedno virtuální rozhraní (agregovanou skupinu) a detekuje selhání rozhraní v rámci skupiny.

* Pro aplikace Kerberos je místní účet služby AD pro omezené delegování.  Informace o vytvoření účtu delegování AD najdete v dokumentaci k nástroji [F5](https://support.f5.com/csp/article/K43063049) .

## <a name="access-guided-configuration"></a>Konfigurace s asistencí pro přístup

* Přístup s asistencí konfigurace se podporuje na F5 TMOS verze 13.1.0.8 a vyšší. Pokud je v systému BIG-IP spuštěná verze nižší než 13.1.0.8, přečtěte si část **Pokročilá konfigurace** .

* Přístup s asistencí konfigurace prezentuje zcela nové a zjednodušené uživatelské prostředí. Tato architektura založená na pracovním postupu poskytuje intuitivní a znovu zabrané konfigurační kroky přizpůsobené vybraným topologií.

* Než začnete s konfigurací, upgradujte konfiguraci s asistencí stažením nejnovější sady případů použití z [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). K upgradu použijte následující postup.

    >[!NOTE]
    >Níže uvedené snímky obrazovky jsou pro nejnovější vydanou verzi (BIG-IP 15,0 s AGC verze 5,0). Níže uvedené konfigurační kroky jsou platné pro tento případ použití v rámci služby 13.1.0.8 na nejnovější verzi s velkou IP adresou.

1. Ve webovém uživatelském rozhraní F5 BIG-IP klikněte na **přístup >> konfigurace příručky**.

1. Na stránce **s asistencí** klikněte v levém horním rohu na možnost **upgradovat konfiguraci s asistencí** .

    ![Snímek obrazovky s odkazem konfigurace s asistencí pro aktualizaci zobrazí stránku s asistencí.](./media/headerf5-tutorial/configure14.png) 

1. Na zobrazené obrazovce konfigurace příručky pro upgrade vyberte **možnost zvolit soubor** . načte se stažený balíček pro použití a klikněte na tlačítko **nahrát a nainstalovat** .

    ![Snímek obrazovky – dialogové okno konfigurace s asistencí pro upgrade s vybraným souborem zvolit.](./media/headerf5-tutorial/configure15.png) 

1. Po dokončení upgradu klikněte na tlačítko **pokračovat** .

    ![Snímek obrazovky se zobrazí dialogové okno konfigurace s asistencí pro upgrade se zprávou o dokončení.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování (SSO) F5 se dá nakonfigurovat třemi různými způsoby.

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scénáře ověřování klíčů

* Kromě Azure Active Directory podpora nativní integrace pro moderní ověřovací protokoly, jako je Open ID Connect, SAML a WS-dodáváme, F5 rozšiřuje zabezpečený přístup pro aplikace založené na starších verzích pro interní i externí přístup s Azure AD a povoluje pro tyto aplikace moderní scénáře (třeba přístup k heslům bez hesla). Patří mezi ně:

* Ověřovací aplikace založené na hlavičkách

* Aplikace pro ověřování pomocí protokolu Kerberos

* Anonymní ověřování nebo žádné sestavené ověřovací aplikace

* Aplikace ověřování NTLM (ochrana se dvěma výzvami pro uživatele)

* Aplikace založené na formulářích (ochrana se dvěma výzvami pro uživatele)

## <a name="adding-f5-from-the-gallery"></a>Přidávání F5 z Galerie

Pokud chcete nakonfigurovat integraci F5 na Azure AD, musíte přidat F5 z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole klávesu **F5** .
1. Vyberte **F5** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurace a testování jednotného přihlašování Azure AD pro F5

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí F5 s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v F5.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí F5, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte F5 SSO](#configure-f5-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele F5](#create-f5-test-user)** , aby měl protějšek B. Simon v F5, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací **F5** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<YourCustomFQDN>.f5.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<YourCustomFQDN>.f5.com/`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory F5 pro klienty](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte metadata a certifikát **federačních metadat** **(Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit na F5** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k F5.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **F5**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .
1. Klikněte na **podmíněný přístup** .
1. Klikněte na **nové zásady**.
1. Teď můžete aplikaci F5 zobrazit jako prostředek pro zásady certifikační autority a použít jakýkoliv podmíněný přístup, včetně vícefaktorového ověřování, řízení přístupu na základě zařízení nebo zásad ochrany identit.

## <a name="configure-f5-sso"></a>Konfigurace F5 SSO

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách

### <a name="guided-configuration"></a>Konfigurace s asistencí

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti F5 (na základě záhlaví) jako správce a proveďte následující kroky:

1. Přejděte do **seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** . Zadejte **název certifikátu** (bude odkazován později v konfiguraci). Ve **zdroji certifikátu**vyberte Odeslat soubor a při konfiguraci jednotného přihlašování SAML zadejte certifikát stažený z Azure. Klikněte na **importovat**.

    ![Snímek obrazovky se zobrazí v seznamu S certifikátem S S, kde vyberete název certifikátu a zdroj certifikátu.](./media/headerf5-tutorial/configure12.png)
 
1. Kromě toho budete vyžadovat **certifikát SSL pro název hostitele aplikace. Přejděte do seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** . **Typ importu** bude **PKCS 12 (IIS)**. Zadejte **název klíče** (bude odkazován později v konfiguraci) a zadejte soubor PFX. Zadejte **heslo** pro PFX. Klikněte na **importovat**.

    >[!NOTE]
    >V příkladu našeho názvu aplikace `Headerapp.superdemo.live` používáme certifikát o zástupné kartě, který je naším `WildCard-SuperDemo.live` příponou KeyName.

    ![Snímek obrazovky se zobrazí stránka S certifikátem nebo zdrojem klíčů s s.](./media/headerf5-tutorial/configure13.png)

1. K nastavení federace služby Azure AD a přístupu k aplikacím použijeme prostředí s asistencí. Přejděte na – F5 BIG-IP **Main** a vyberte **přístup > s asistencí konfigurace > federaci > poskytovatele služeb SAML**. Klikněte na **Další** a potom na **Další** . tím spustíte konfiguraci.

    ![Snímek obrazovky zobrazující stránku konfigurace s asistencí se zvolenou federaci.](./media/headerf5-tutorial/configure01.png)

    ![Snímek obrazovky ukazuje stránku poskytovatele služby SAML.](./media/headerf5-tutorial/configure02.png)
 
1. Zadejte **název konfigurace**. Zadejte **ID entity** (stejné jako to, co jste nakonfigurovali v konfiguraci aplikace Azure AD). Zadejte **název hostitele**. Přidejte **Popis** pro referenci. Přijměte zbývající výchozí položky a vyberte a pak klikněte na **uložit & další**.

    ![Snímek obrazovky se zobrazí stránka vlastností poskytovatele služby.](./media/headerf5-tutorial/configure03.png) 

1. V tomto příkladu vytváříme nový virtuální server jako 192.168.30.20 s portem 443. Zadejte IP adresu virtuálního serveru v **cílové adrese**. Vyberte **profil SSL**klienta, vyberte vytvořit novou. Zadejte dříve nahraný certifikát aplikace (v tomto příkladu certifikát zástupné karty) a související klíč a potom klikněte na **uložit & další**.

    >[!NOTE]
    >v tomto příkladu náš interní webserver běží na portu 888 a chceme ho publikovat v 443.

    ![Snímek obrazovky ukazuje stránku vlastností virtuálního serveru.](./media/headerf5-tutorial/configure04.png) 

1. V části **Vybrat metodu Nakonfigurujte konektor IDP**, zadejte metadata, klikněte na vybrat soubor a nahrajte soubor XML s metadaty staženými dříve ze služby Azure AD. Zadejte jedinečný **název** pro IDP konektor SAML. Vyberte **certifikát pro podpis metadat** , který se nahrál dříve. Klikněte na **uložit & další**.

    ![Snímek obrazovky ukazuje stránku nastavení konektoru externího zprostředkovatele identity.](./media/headerf5-tutorial/configure05.png)
 
1. V části **Vybrat fond**zadejte **vytvořit novou** (případně vyberte fond, který už existuje). Nechte výchozí hodnotu. V části servery fondů zadejte IP adresu do pole **IP adresa/název uzlu**. Zadejte **port**. Klikněte na **uložit & další**.

    ![Snímek obrazovky se zobrazí stránka vlastností fondu.](./media/headerf5-tutorial/configure06.png)

1. Na obrazovce nastavení jednoho Sign-On vyberte **Povolit jednotné přihlašování**. V části vybraný typ jednoho Sign-On vyberte možnost **na základě hlaviček protokolu HTTP**. Nahraďte **Session. SAML. Last. identity** pomocí **Session. SAML. Last. attr. Name. identity** v rámci zdroje uživatelského jména (Tato proměnná se nastaví pomocí mapování deklarací v Azure AD). V části hlavičky jednotného přihlašování.

    * **Záhlaví: MyAuthorization**

    * **Hodnota hlavičky:% {Session. SAML. Last. attr. Name. identity}**

    * Klikněte na **uložit & další** .

    Úplný seznam proměnných a hodnot najdete v příloze. V případě potřeby můžete přidat další záhlaví.

    >[!NOTE]
    >Název účtu je vytvořený účet pro delegování F5 (podívejte se na dokumentaci F5).

    ![Snímek obrazovky se zobrazí stránka nastavení s jedním Sign-On.](./media/headerf5-tutorial/configure07.png) 

1. Pro účely tohoto návodu provedeme přeskočení kontrol koncových bodů.  Podrobnosti najdete v dokumentaci k F5. Vyberte **uložit & další**.

    ![Snímek obrazovky se zobrazí stránka vlastnosti kontroly koncových bodů.](./media/headerf5-tutorial/configure08.png)

1. Přijměte výchozí hodnoty a klikněte na **uložit & další**. Podrobnosti o nastavení správy relace SAML najdete v dokumentaci k nástroji F5.

    ![Snímek obrazovky zobrazující stránku nastavení časového limitu.](./media/headerf5-tutorial/configure09.png)

1. Zkontrolujte obrazovku souhrnu a vyberte **nasadit** a NAKONFIGURUJTE tak Big-IP. klikněte na **Dokončit**.

    ![Snímek obrazovky ukazuje, že vaše aplikace je připravená k nasazení.](./media/headerf5-tutorial/configure10.png)

    ![Snímek obrazovky se zobrazí stránka aplikace je nasazená.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Tato část je určena k použití, pokud nemůžete použít konfiguraci s asistencí nebo chcete přidat nebo změnit další parametry. Pro název hostitele aplikace budete potřebovat certifikát TLS/SSL.

1. Přejděte do **seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** . **Typ importu** bude **PKCS 12 (IIS)**. Zadejte **název klíče** (bude odkazován později v konfiguraci) a zadejte soubor PFX. Zadejte **heslo** pro PFX. Klikněte na **importovat**.

    >[!NOTE]
    >V příkladu našeho názvu aplikace `Headerapp.superdemo.live` používáme certifikát o zástupné kartě, který je naším `WildCard-SuperDemo.live` příponou KeyName.
  
    ![Snímek obrazovky se zobrazením stránky S certifikátem nebo zdrojem klíčů S/S pro pokročilou konfiguraci.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Přidání nového webového serveru do Big-IP – F5

1. Klikněte na **hlavní > IApps > Aplikační služby > aplikace > vytvořit**.

1. Zadejte **název** a v části **Šablona** vyberte **F5. http**.
 
    ![Snímek obrazovky ukazuje stránku Aplikační služby s výběrem šablony.](./media/headerf5-tutorial/configure18.png)

1. V tomto případě budeme publikovat náš HeaderApp2 externě jako HTTPS, **jak by měl systém Big-IP zpracovávat přenosy SSL**? určujeme **ukončení protokolu SSL od klienta, prostého textu na servery (přesměrování zpracování SSL)**. Zadejte svůj certifikát a klíč, pod **kterým certifikát SSL chcete použít?** a **který privátní klíč SSL chcete použít?**. Zadejte IP adresu virtuálního serveru v části **jakou IP adresu chcete použít pro virtuální server**. 

    * **Zadat další podrobnosti**

        * FQDN  

        * Určete, že se má fond aplikací ukončit, nebo vytvořte nový.

        * Pokud vytváříte nový aplikační server, zadejte **interní IP adresu** a **číslo portu**.

        ![Snímek obrazovky se zobrazí v podokně, kde můžete zadat tyto podrobnosti.](./media/headerf5-tutorial/configure19.png) 

1. Klikněte na **Hotovo**.

    ![Snímek obrazovky po dokončení zobrazí stránku.](./media/headerf5-tutorial/configure20.png) 

1. Ujistěte se, že vlastnosti aplikace lze upravovat. Klikněte na **hlavní > IApps > aplikační služby: aplikace >> HeaderApp2**. Zrušte kontrolu **striktních aktualizací** (některé nastavení upraví mimo grafické uživatelské rozhraní). Klikněte na tlačítko **aktualizovat** .

    ![Snímek obrazovky ukazuje stránku Aplikační služby s vybraným záložkou Vlastnosti.](./media/headerf5-tutorial/configure21.png) 

1. V tuto chvíli byste měli být schopni procházet virtuální server.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Konfigurace F5 jako SP a Azure jako IDP

1.  Klikněte na **přístup > federaci> poskytovatele služby SAML > místní služba SP > klikněte na vytvořit nebo + podepsat**.

    ![Snímek obrazovky ukazuje tuto velkou I P stránku. ](./media/headerf5-tutorial/configure22.png)

1. Zadejte podrobnosti pro službu poskytovatele služeb. Zadejte **název** představující konfiguraci F5 SP. Zadejte **ID entity** (obvykle stejné jako adresa URL aplikace).

    ![Snímek obrazovky se stránkou poskytovatele služby SAML zobrazuje dialogové okno vytvořit novou službu SAML S P.](./media/headerf5-tutorial/configure23.png)

    ![Snímek obrazovky se zobrazí dialogové okno vytvořit novou službu SAML S P s vybraným nastavením koncového bodu.](./media/headerf5-tutorial/configure24.png)

    ![Snímek obrazovky se zobrazí dialogové okno vytvořit novou službu SAML S P s vybraným nastavením zabezpečení.](./media/headerf5-tutorial/configure25.png)

    ![Snímek obrazovky se zobrazí dialogové okno vytvořit novou službu SAML S P s vybraným kontextem ověřování.](./media/headerf5-tutorial/configure26.png)

    ![Snímek obrazovky se zobrazí dialogové okno vytvořit novou službu SAML s P s požadovanými atributy.](./media/headerf5-tutorial/configure27.png)

    ![Snímek obrazovky se zobrazí dialogové okno Upravit službu SAML S P s vybraným rozšířeným nastavením.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Vytvoření konektoru IDP

1. Klikněte na tlačítko **vazba/zrušit vazbu konektoru IDP** , vyberte **vytvořit nový konektor IDP** a zvolte možnost z **metadat** . potom proveďte následující kroky:
 
    ![Snímek obrazovky se zobrazí v dialogovém okně Upravit SAML I d PS, který používá toto dialogové okno s vybraným příkazem Vytvořit nový konektor I d P.](./media/headerf5-tutorial/configure29.png)

    a. Přejděte na metadata.xml soubor stažený z Azure AD a zadejte **název zprostředkovatele identity**.

    b. Klikněte na tlačítko **OK**.

    c. Konektor se vytvoří a certifikát je připravený automaticky ze souboru XML s metadaty.
    
    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit novou spojnici SAML I d P.](./media/headerf5-tutorial/configure30.png)

    d. Nakonfigurujte F5BIG-IP pro odeslání všech požadavků do Azure AD.

    e. Klikněte na tlačítko **Přidat nový řádek**, zvolte možnost **AzureIDP** (jak bylo vytvořeno v předchozích krocích) zadejte 

    f. **Shodný zdroj =% {Session. Server. landinguri}** 

    například **Shoda hodnoty =/***

    h. Kliknout na **aktualizovat**

    i. Klikněte na tlačítko **OK**.

    j. **Instalace SAML IDP je dokončená.**
    
    ![Snímek obrazovky s dialogovým oknem upravit SAML I d PS, který tento uživatel obsahuje.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Konfigurace zásad F5 pro přesměrování uživatelů na Azure SAML IDP

1. Pokud chcete nakonfigurovat zásadu F5 pro přesměrování uživatelů na Azure SAML IDP, proveďte následující kroky:

    a. Klikněte na **hlavní > Access > Profile/zásady > profily přístupu**.

    b. Klikněte na tlačítko **vytvořit** .

    ![Snímek obrazovky se zobrazí na stránce profily přístupu.](./media/headerf5-tutorial/configure32.png)
 
    c. V příkladu zadejte **název** (HeaderAppAzureSAMLPolicy).

    d. Můžete si přizpůsobit další nastavení. Další informace najdete v dokumentaci k F5.

    ![Snímek obrazovky se zobrazí na stránce Obecné vlastnosti.](./media/headerf5-tutorial/configure33.png)

    ![Snímek obrazovky se zobrazí na stránce Obecné vlastnosti.](./media/headerf5-tutorial/configure34.png) 

    e. Klikněte na **Hotovo**.

    f. Až se vytváření zásad dokončí, klikněte na zásadu a přejděte na kartu **zásady přístupu** .

    ![Snímek obrazovky se na kartě zásady přístupu zobrazí obecné vlastnosti.](./media/headerf5-tutorial/configure35.png)
 
    například Klikněte na **Editor vizuálních zásad**, upravte **zásady přístupu pro** odkaz na profil.

    h. V editoru vizuálních zásad klikněte na symbol + a vyberte **ověřování SAML**.

    ![Snímek obrazovky ukazuje zásady přístupu.](./media/headerf5-tutorial/configure36.png)

    ![Snímek obrazovky se zobrazí dialogové okno hledání s vybraným ověřováním SAML.](./media/headerf5-tutorial/configure37.png)
 
    i. Klikněte na tlačítko **Přidat položku**.

    j. V části **vlastnosti** zadejte **název** a v části **AAA Server** vyberte dříve nakonfigurovanou aktualizaci SP, klikněte na **Uložit**.
 
    ![Snímek obrazovky zobrazuje vlastnosti položky, včetně jejího serveru a.](./media/headerf5-tutorial/configure38.png)

    k. Základní zásady jsou připravené. můžete je přizpůsobit tak, aby zahrnovaly další úložiště zdrojů nebo atributů.

    ![Snímek obrazovky se zobrazuje přizpůsobená zásada.](./media/headerf5-tutorial/configure39.png)
 
    l. Ujistěte se, že kliknete na odkaz **použít zásady přístupu** v horní části.

### <a name="apply-access-profile-to-the-virtual-server"></a>Použít profil přístupu k virtuálnímu serveru

1. Přiřaďte profil přístupu k virtuálnímu serveru, aby funkce F5 BIG IP APM používala nastavení profilu pro příchozí provoz a spouštěla dříve definovaná zásada přístupu.

    a. Klikněte na **Hlavní**  >  **místní provoz**  >  **virtuální servery**.

    ![Snímek obrazovky se zobrazí stránka seznam virtuálních serverů.](./media/headerf5-tutorial/configure40.png)
 
    b. Klikněte na virtuální server, přejděte k části **zásady přístupu** , v rozevíracím seznamu **profil přístupu** a vyberte zásadu SAML vytvořenou (v příkladu HeaderAppAzureSAMLPolicy).

    c. Kliknout na **aktualizovat**
 
    ![Snímek obrazovky se zobrazí v podokně zásady přístupu.](./media/headerf5-tutorial/configure41.png)

    d. Vytvořte® F5 BIG-IP iRule k extrakci vlastních atributů SAML z příchozího kontrolního výrazu a jejich předání jako hlaviček protokolu HTTP do aplikace back-end test. Klikněte na **hlavní > místní provoz > iRules > IRule seznamu > klikněte na vytvořit** .

    ![Snímek obrazovky zobrazuje seznam místních přenosů iRule.](./media/headerf5-tutorial/configure42.png)
 
    e. Do okna definice vložte text F5 BIG-IP iRule.

    ![Snímek obrazovky se zobrazí nová stránka iRule.](./media/headerf5-tutorial/configure43.png)
 
    Když RULE_INIT {set static::d ebug 0}, pokud ACCESS_ACL_ALLOWED {

    Nastavte AZUREAD_USERNAME [přístup:: Session data Get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name "], pokud {$static::d ebug} {log local0. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} Pokud {! ( [HTTP:: header existuje "AZUREAD_USERNAME"]) } {HTTP:: header INSERT AZUREAD_USERNAME $AZUREAD _USERNAME}

    Nastavte AZUREAD_DISPLAYNAME [přístup:: Session data Get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname "], pokud {$static::d ebug} {log local0. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} Pokud {! ( [HTTP:: header existuje "AZUREAD_DISPLAYNAME"]) } {HTTP:: header INSERT AZUREAD_DISPLAYNAME $AZUREAD _DISPLAYNAME}

    Nastavte AZUREAD_EMAILADDRESS [přístup:: Session data Get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress "], pokud {$static::d ebug} {log local0. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} Pokud {! ( [HTTP:: header existuje "AZUREAD_EMAILADDRESS"]) } {HTTP:: header Insert "AZUREAD_EMAILADDRESS" $AZUREAD _EMAILADDRESS}}

    **Ukázkový výstup níže**

    ![Snímek obrazovky ukazuje vzorový výstup.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Vytvořit testovacího uživatele F5

V této části vytvoříte na F5 uživatele s názvem B. Simon. Pokud chcete přidat uživatele na platformě F5, pracujte s nástrojem [F5 Client Support Team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici F5 na přístupovém panelu byste měli být automaticky přihlášeni k F5, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Zkuste F5 pomocí Azure AD](https://aad.portal.azure.com/)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

