---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Oracle PeopleSoft spouštěných místně-Protected pomocí programu F5 BIG-IP APM | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 7d2dc1d5d02f1a371d6d94f9eeddf395d49126d7
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620133"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Oracle PeopleSoft spouštěných místně-Protected pomocí programu F5 BIG-IP APM

V tomto kurzu se dozvíte, jak integrovat Oracle PeopleSoft spouštěných místně-Protected pomocí programu F5 BIG-IP APM s Azure Active Directory (Azure AD). Když integruje Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.
* Tento kurz se zabývá pokyny pro Oracle PeopleSoft spouštěných místně ELM.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

1. Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
1. Oracle PeopleSoft spouštěných místně-Protected pomocí standardu F5 BIG-IP APM Single Sign-on (SSO) s povoleným odběrem.

1. Nasazení společného řešení vyžaduje následující licenci:

    1. ® Nejlepší sada pro BIG-IP 
    2. F5 – samostatná licence pro správce zásad pro BIG-IP Access™ (APM) 
    3. F5 licence na™ (APM) pro správce zásad pro Big-IP Access v existující® místní Traffic Manager™ (LTM).
    4. Kromě výše uvedených licencí může být systém F5 také licencován: 
        * Adresa URL pro filtrování předplatného pro použití databáze kategorií URL 
        * Předplatné funkce F5 IP Intelligence pro detekci a blokování známých útočníci a škodlivých přenosů 
        * Modul hardwarového zabezpečení (HSM), který chrání a spravuje digitální klíče pro silné ověřování
1. Systém F5 BIG-IP System je zřízený s moduly APM (LTM je volitelné). 
1. I když je volitelná, důrazně doporučujeme nasadit systémy F5 do [skupiny zařízení Sync/převzetí služeb při selhání](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), která zahrnuje aktivní pohotovostní režim s plovoucí IP adresou pro vysokou dostupnost (ha). Další redundanci rozhraní je možné dosáhnout pomocí LACP (Link Aggregator Control Protocol). LACP spravuje připojená fyzická rozhraní jako jedno virtuální rozhraní (agregovanou skupinu) a detekuje selhání rozhraní v rámci skupiny.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Oracle PeopleSoft spouštěných místně-Protected pomocí serveru F5 BIG-IP APM podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Přidání nástroje Oracle PeopleSoft spouštěných místně-Protected pomocí programu F5 BIG-IP APM z Galerie

Pokud chcete nakonfigurovat integraci Oracle PeopleSoft spouštěných místně-Protected pomocí programu F5 BIG-IP APM do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat rozhraní Oracle PeopleSoft spouštěných místně, které je chráněno pomocí programu F5 BIG-IP APM z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **příkaz Oracle PeopleSoft spouštěných místně-Protected-IP APM** .
1. Vyberte **Oracle PeopleSoft spouštěných místně-Protected pomocí F5 Big-IP APM** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 BIG-IP APM

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte pro Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 Big-IP APM SSO](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 Big-IP APM Test User](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** -to znamená, že má protějšek B. Simon v Oracle PeopleSoft spouštěných místně-Protected s funkcí APM Big-IP, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace PeopleSoft spouštěných místně, která je **chráněná pomocí F5 Big-IP APM** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN>.peoplesoft.f5.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. Do textového pole **odhlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru, adresy URL odpovědi a adresy URL pro odhlášení. Pokud chcete získat hodnotu, kontaktujte [Oracle PeopleSoft spouštěných místně – chráněný pomocí nástroje F5 Big-IP APM Client Support Team](https://support.f5.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Oracle PeopleSoft spouštěných místně-Protected pomocí aplikace F5 BIG-IP APM očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává Oracle PeopleSoft spouštěných místně-Protected pomocí aplikace F5 BIG-IP APM několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedeny níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Název |  Zdrojový atribut|
    | ------------------ | --------- |
    | EMPLID | User. ČísloZaměstnance |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** stáhněte **XML metadata federace** a **certifikát (Base64)** a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 BIG-IP APM.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 Big-IP APM**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Konfigurace Oracle PeopleSoft spouštěných místně-Protected pomocí serveru F5 BIG-IP APM SSO

### <a name="f5-saml-sp-configuration"></a>F5 SP konfigurace SAML

Importujte certifikát metadat do F5, který bude použit později v procesu instalace. Přejděte do **seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** .

![F5 SP konfigurace SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Nastavení konektoru IDP SAML 

1. Přejděte na **přístup > federaci > SAML: Service Provider > External konektory IDP** a klikněte na **vytvořit > z metadat**.

    ![F5 konektor SAML IDP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Na následující stránce klikněte na tlačítko **Procházet** a nahrajte soubor XML.

1. Zadejte platný název do textového pole **název zprostředkovatele identity** a pak klikněte na **OK**.

    ![nový konektor SAML IDP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Proveďte požadované kroky na kartě **nastavení zabezpečení** a pak klikněte na tlačítko **OK**.

    ![upravit konektor SAML IDP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Nastavení SAML SP

1. Přejděte na **přístup > federaci > poskytovatele služby SAML > místní služby SP** a klikněte na **vytvořit**. Vyplňte následující informace a klikněte na tlačítko **OK**.

    * Název: `<Name>`
    * ID entity: `https://<FQDN>.peoplesoft.f5.com`
    * Nastavení názvu SP
        * Programu `https`
        * Provoz `<FQDN>.peoplesoft.f5.com`
        * Název `<Description>`

    ![nové služby SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Vyberte konfiguraci SP, PeopleSoftAppSSO a **klikněte na BIND/zrušit konektory IDP**.
Klikněte na **Přidat nový řádek** a vyberte **externí konektor IDP** vytvořený v předchozím kroku, klikněte na **aktualizovat**a pak klikněte na **OK**.

    ![vytváření služeb SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Konfigurace aplikace

### <a name="create-a-new-pool"></a>Vytvořit nový fond
1. Přejděte na **místní provoz > fondy > fond**, klikněte na **vytvořit**, vyplňte následující informace a klikněte na **Hotovo**.

    * Název: `<Name>`
    * Název `<Description>`
    * Monitory stavu: `http`
    * Adresáře `<Address>`
    * Port služby: `<Service Port>`

    ![Vytvoření nového fondu](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Vytvořit nový profil SSL klienta

Přejděte na **místní provoz > profily > SSL > klient > +**, vyplňte následující informace a klikněte na **Hotovo**.

* Název: `<Name>`
* Certifikát `<Certificate>`
* Klíč: `<Key>`

![nový profil SSL klienta](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Vytvořit nový virtuální server

1. Přejděte na **místní provoz > virtuální servery > seznam virtuálních serverů > +**, dokončete následující informace a klikněte na **Hotovo**.
    * Název: `<Name>`
    * Cílová adresa/maska: `<Address>`
    * Port služby: port 443 HTTPS
    * Profil HTTP (klient): http

    ![Vytvořit nový virtuální server](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Na následující stránce vyplňte následující hodnoty:

    * Profil SSL (klient): `<SSL Profile>`
    * Překlad zdrojové adresy: Automatická mapa
    * Profil přístupu: `<Access Profile>`
    * Výchozí fond: `<Pool>`


    ![Vytvořit nový fond PeopleSoft spouštěných místně virtuálních serverů](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Nastavení aplikace v PeopleSoft spouštěných místně pro podporu funkce s funkcí F5 Big-IP APM jako řešení jednotného přihlašování

>[!Note]
> Odkaz https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Přihlášení ke konzole PeopleSoft spouštěných místně `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` pomocí přihlašovacích údajů správce (příklad: PS/PS)

    ![Správce – samostatné služby](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. V aplikaci PeopleSoft spouštěných místně vytvořte **OAMPSFT** jako nový profil uživatele a přidružte nízkou roli zabezpečení, například **PeopleSoft spouštěných místně uživatele**.
Přejděte na **Peopletools > zabezpečení > profily uživatelů > profily** uživatelů, abyste vytvořili nový profil uživatele, například: **OAMPSFT** a přidejte **PeopleSoft spouštěných místně uživatele**.

    ![Uživatel PeopleSoft spouštěných místně](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Přihlaste se k webovému profilu a jako **ID uživatele**veřejného přístupu zadejte **OAMPSFT** .

    ![Profily uživatelů](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. Z **Návrhář aplikací PeopleTools**otevřete záznam **FUNCLIB_LDAP** .

    ![Konfigurace webového profilu](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Aktualizuje hlavičku uživatele pomocí **PS_SSO_UID** pro funkci **OAMSSO_AUTHENTICATION** .
Ve funkci **getWWWAuthConfig ()** nahraďte hodnotu, která je přiřazena k **&defaultUserId** s **OAMPSFT** , které jsme definovali ve webovém profilu. Uložte definici záznamu.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Přístup ke stránce **jednotného PeopleCode** (PeopleTools, Security, Security Objects, jednotného PeopleCode) a povolení funkce **OAMSSO_AUTHENTICATION** – jednotného PeopleCode pro Oracle Access Manager Single jednotného.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Nastavování nástroje F5 Big-IP APM k naplnění hlavičky "PS_SSO_UID" protokolu HTTP s ID uživatele PeopleSoft spouštěných místně

### <a name="configuring-per-request-policy"></a>Konfigurace zásad pro požadavky
1. Přejděte na **Access > Profile/zásady > zásady pro žádosti**, klikněte na **vytvořit**, dokončete následující informace a klikněte na **Hotovo**.

    * Název: `<Name>`
    * Typ profilu: vše
    * Jazyky `<Language>`

    ![Konfigurace zásad pro požadavky ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Klikněte na **Upravit** zásadu podle požadavku `<Name>` ![ upravit zásady podle požadavků PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Přiřazení zásad pro požadavky na virtuální server

Přejděte na **místní provoz > virtuální servery > seznam virtuálních serverů > PeopleSoftApp** určete `<Name>` jako zásadu podle požadavků.

![PeopleSoftSSO podle zásad pro žádosti ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Nastavení klávesy F5 s funkcí APM Big-IP pro podporu jednotného odhlašování z aplikace PeopleSoft spouštěných místně

Pokud chcete přidat podporu jednotného odhlašování pro všechny uživatele PeopleSoft spouštěných místně, postupujte podle těchto kroků:

1. Určení správné adresy URL pro odhlášení pro portál PeopleSoft spouštěných místně
    * Chcete-li určit adresu, kterou aplikace PeopleSoft spouštěných místně používá k ukončení uživatelské relace, je nutné otevřít portál pomocí libovolného webového prohlížeče a povolit ladicí nástroje prohlížeče, jak je znázorněno v následujícím příkladu:

        ![odhlašovací adresa URL pro portál PeopleSoft spouštěných místně ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Vyhledejte element s `PT_LOGOUT_MENU` ID a uložte cestu k adrese URL pomocí parametrů dotazu. V našem příkladu máme následující hodnotu: `/psp/ps/?cmd=logout`

1. Vytvořte LTM iRule, který přesměruje uživatele na odhlašovací adresu URL pro odhlášení APM: `/my.logout.php3`

    * Přejděte na **místní provoz > iRule**, klikněte na **vytvořit**, vyplňte následující informace a klikněte na **Hotovo**.

        Název: `<Name>`  
        Definice:  
                    _Při HTTP_REQUEST {Switch-glob--[HTTP:: URI] { `/psp/ps/?cmd=logout` {http:: Redirect `/my.logout.php3` }}}_

1. Přiřaďte vytvořené iRule k virtuálnímu serveru.

    * Přejděte na **místní provoz > virtuální servery > seznam virtuálních serverů > PeopleSoftApp > prostředky**. Klikněte na **Spravovat...** tlačítko   

    * Zadejte `<Name>` jako povolené iRule a klikněte na **Hotovo** .

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Zadejte hodnotu **název** textového pole jako `<Name>` 

        ![_iRule_PeopleSoftApp dokončen](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Vytvořit Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 BIG-IP APM Test User

V této části vytvoříte uživatele s názvem B. Simon v Oracle PeopleSoft spouštěných místně – chráněný pomocí nástroje F5 BIG-IP APM. Pracujte s [Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 pro podporu APM Big-IP APM](https://support.f5.com) a přidejte uživatele na platformě Oracle PeopleSoft spouštěných místně-Protected pomocí nástroje F5 Big-IP APM. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL PeopleSoft spouštěných místně chráněnou pomocí nástroje F5 BIG-IP APM, kde můžete spustit tok přihlášení.  

* Přejít na adresu Oracle PeopleSoft spouštěných místně-Protected pomocí aplikace F5 BIG-IP APM URL přímo a zahájit tok přihlášení z tohoto účtu.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM Big-IP APM, pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici PeopleSoft spouštěných místně, která je chráněná pomocí nástroje Oracle Protected na úrovni služby, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlašovat k Oracle PeopleSoft spouštěných místně chráněnému funkcí programu F5 BIG-IP APM, pro kterou nastavíte jednotné přihlašování Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Oracle PeopleSoft spouštěných místně-Protected pomocí funkce APM BIG-IP APM, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
