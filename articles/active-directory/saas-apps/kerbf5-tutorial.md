---
title: 'Kurz: integrace s jednotným přihlašováním v Azure AD s F5 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování (SSO) mezi Azure Active Directory a F5.
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
ms.openlocfilehash: 2fd96c47e98a6f8a66a5fda555304c4448d772e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646091"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s F5

V tomto kurzu se naučíte integrovat F5 s Azure Active Directory (Azure AD). Při integraci F5 se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k F5.
* Umožněte uživatelům, aby se automaticky přihlásili k F5 pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

* Předplatné s povoleným jednotným přihlašováním (SSO).

* Nasazení společného řešení vyžaduje následující licenci:
    * Nejlepší sada pro BIG-IP &reg; (nebo) F5

    * Verze F5 – samostatná licence pro správce zásad přístupu &trade; (APM) pro velké IP adresy

    * F5 licence na doplněk pro správce zásad pro Big-IP Access Manager &trade; (APM) ve stávajícím místním Traffic Manager pro Big-IP F5 s velkým IP adresou &reg; &trade; (LTM).

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

2. Na stránce **s asistencí** klikněte v levém horním rohu na možnost **upgradovat konfiguraci s asistencí** .

    ![Snímek obrazovky se stránkou "konfigurace s asistencí", která je vybrána v akci "upgrade s asistencí".](./media/kerbf5-tutorial/configure14.png) 

3. Na zobrazené obrazovce konfigurace příručky pro upgrade vyberte **možnost zvolit soubor** . načte se stažený balíček pro použití a klikněte na tlačítko **nahrát a nainstalovat** .

    ![Snímek obrazovky, na které se zobrazuje automaticky otevíraná okna konfigurace s asistencí s výběrem možnosti soubor a nahrání a instalace](./media/kerbf5-tutorial/configure15.png) 

4. Po dokončení upgradu klikněte na tlačítko **pokračovat** .

    ![Snímek obrazovky zobrazující dialog aktualizace s asistencí je dokončen a vybrané tlačítko pokračovat.](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* F5 podporuje **SP a IDP** iniciované jednotné přihlašování.
* Jednotné přihlašování (SSO) F5 se dá nakonfigurovat třemi různými způsoby.

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](headerf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scénáře ověřování klíčů

Kromě Azure Active Directory podpora nativní integrace pro moderní ověřovací protokoly, jako je Open ID Connect, SAML a WS-dodáváme, F5 rozšiřuje zabezpečený přístup pro aplikace založené na starších verzích pro interní i externí přístup s Azure AD a povoluje pro tyto aplikace moderní scénáře (třeba přístup k heslům bez hesla). Patří mezi ně:

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

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **metadata** a **certifikát federace (Base64)** a pak vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit na F5** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k F5.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **F5**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .
1. Klikněte na **podmíněný přístup** .
1. Klikněte na **nové zásady**.
1. Teď můžete aplikaci F5 zobrazit jako prostředek pro zásady podmíněného přístupu a použít jakýkoliv podmíněný přístup, včetně vícefaktorového ověřování, řízení přístupu na základě zařízení nebo zásad ochrany identit.

## <a name="configure-f5-sso"></a>Konfigurace F5 SSO

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](headerf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos

### <a name="guided-configuration"></a>Konfigurace s asistencí

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti F5 (Kerberos) jako správce a proveďte následující kroky:

1. Certifikát metadat bude nutné importovat do F5, který bude později použit v procesu instalace.

1. Přejděte do **seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** . Zadejte **název certifikátu** (bude odkazován později v konfiguraci). Ve **zdroji certifikátu** vyberte Odeslat soubor a při konfiguraci jednotného přihlašování SAML zadejte certifikát stažený z Azure. Klikněte na **importovat**.

    ![Snímek obrazovky se stránkou s názvem "s S certifikátem/zdrojem klíčů", která obsahuje zvýrazněný název certifikátu, nahrát soubor a vybrané tlačítko Import](./media/kerbf5-tutorial/configure01.png) 

1. Kromě toho budete vyžadovat **certifikát SSL pro název hostitele aplikace. Přejděte do seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** . **Typ importu** bude **PKCS 12 (IIS)**. Zadejte **název klíče** (bude odkazován později v konfiguraci) a zadejte soubor PFX. Zadejte **heslo** pro PFX. Klikněte na **importovat**.

    >[!NOTE]
    >V příkladu našeho názvu aplikace `Kerbapp.superdemo.live` používáme certifikát se zástupnými kartami. naše KeyName je `WildCard-SuperDemo.live`

    ![Snímek obrazovky se stránkou "S S certifikátem/zdrojem klíčů" s zadanými hodnotami a vybraným tlačítkem import.](./media/kerbf5-tutorial/configure02.png) 
 
1. K nastavení federace služby Azure AD a přístupu k aplikacím použijeme prostředí s asistencí. Přejděte na – F5 BIG-IP **Main** a vyberte **přístup > s asistencí konfigurace > federaci > poskytovatele služeb SAML**. Klikněte na **Další** a potom na **Další** . tím spustíte konfiguraci.

    ![Snímek obrazovky zobrazující stránku s asistencí konfigurací se zvýrazněnou ikonou "federace" a "S" poskytovatelem služeb A.](./media/kerbf5-tutorial/configure03.png) 

    ![Snímek obrazovky se stránkou "konfigurace s asistencí-S" poskytovatelem služby M "s vybraným tlačítkem" Další ".](./media/kerbf5-tutorial/configure04.png)

1. Zadejte **název konfigurace**. Zadejte **ID entity** (stejné jako to, co jste nakonfigurovali v konfiguraci aplikace Azure AD). Zadejte **název hostitele**. Přidejte **Popis** pro referenci. Přijměte zbývající výchozí položky a vyberte a pak klikněte na **uložit & další**.

    ![Snímek obrazovky s zvýrazněnými textovými poli "Service Provider Properties" a "Description" a vybraným tlačítkem Uložit & Next.](./media/kerbf5-tutorial/configure05.png) 

1. V tomto příkladu vytváříme nový virtuální server jako 192.168.30.200 s portem 443. Zadejte IP adresu virtuálního serveru v **cílové adrese**. Vyberte **profil SSL** klienta, vyberte vytvořit novou. Zadejte dříve nahraný certifikát aplikace (v tomto příkladu certifikát zástupné karty) a související klíč a potom klikněte na **uložit & další**.

    >[!NOTE]
    >v tomto příkladu náš interní webserver běží na portu 80 a chceme ho publikovat v 443.

    ![Snímek obrazovky zobrazující stránku vlastností virtuálního serveru se zvýrazněným textovým polem cílová adresa a vybraným tlačítkem Uložit & další.](./media/kerbf5-tutorial/configure06.png)

1. V části **Vybrat metodu Nakonfigurujte konektor IDP**, zadejte metadata, klikněte na vybrat soubor a nahrajte soubor XML s metadaty staženými dříve ze služby Azure AD. Zadejte jedinečný **název** pro IDP konektor SAML. Vyberte **certifikát pro podpis metadat** , který se nahrál dříve. Klikněte na **uložit & další**.

    ![Snímek obrazovky zobrazující stránku nastavení konektoru externího zprostředkovatele identity se zvýrazněným textovým polem název a vybraným tlačítkem Uložit & další.](./media/kerbf5-tutorial/configure07.png)  

1. V části **Vybrat fond** zadejte **vytvořit novou** (případně vyberte fond, který už existuje). Nechte výchozí hodnotu.    V části servery fondů zadejte IP adresu do pole **IP adresa/název uzlu**. Zadejte **port**. Klikněte na **uložit & další**.
 
    ![Snímek obrazovky zobrazující stránku vlastností fondu se zvýrazněnými textovými poli IP adresa/název uzlu a portem a vybraným tlačítkem Uložit & další.](./media/kerbf5-tutorial/configure08.png)

1. Na obrazovce nastavení jednoho Sign-On vyberte **Povolit jednotné přihlašování**. V části **vybraný typ jednoho Sign-On** vyberte možnost **Kerberos**. Nahraďte **Session. SAML. Last. identity**  pomocí **Session. SAML. Last. attr. Name. identity** v rámci **zdroje uživatelského jména** (Tato proměnná se nastaví pomocí mapování deklarací v Azure AD). Vyberte **Zobrazit upřesňující nastavení**. V části **sféra protokolu Kerberos** zadejte název domény. V části **název účtu/heslo účtu** zadejte účet delegování APM a heslo. Zadejte IP adresu řadiče domény v poli **KDC** . Klikněte na **uložit & další**.

    ![Snímek obrazovky zobrazující nastavení "jednotné Sign-On" se zvýrazněnými textovými poli a vybraným tlačítkem Uložit & další.](./media/kerbf5-tutorial/configure09.png)   

1. Pro účely tohoto návodu provedeme přeskočení kontrol koncových bodů.  Podrobnosti najdete v dokumentaci k F5.  Na obrazovce vyberte **uložit & další**.

    ![Snímek obrazovky, na kterém je zobrazená stránka kontroly koncových bodů a vybrané tlačítko Uložit & další](./media/kerbf5-tutorial/configure10.png) 

1. Přijměte výchozí hodnoty a klikněte na **uložit & další**. Podrobnosti o nastavení správy relace SAML najdete v dokumentaci ke službě F5.


    ![Snímek obrazovky zobrazující stránku nastavení časového limitu s vybraným tlačítkem Uložit & další](./media/kerbf5-tutorial/configure11.png) 
 
1. Zkontrolujte obrazovku souhrnu a vyberte **nasadit** a NAKONFIGURUJTE tak Big-IP.
 
    ![Snímek obrazovky zobrazující, že vaše aplikace je připravená k nasazení, se zvýrazněným oddílem souhrn a vybraným tlačítkem nasadit.](./media/kerbf5-tutorial/configure12.png)

1. Po nakonfigurování aplikace klikněte na **Dokončit**.

    ![Snímek obrazovky zobrazující stránku aplikace, která je nasazená, s vybraným tlačítkem Dokončit](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Pokročilá konfigurace

>[!NOTE]
>Kliknutím [sem](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) zobrazíte odkaz.

### <a name="configuring-an-active-directory-aaa-server"></a>Konfigurace serveru služby Active Directory AAA

Server služby Active Directory AAA nakonfigurujete v Access Policy Manageru (APM) a určíte řadiče domény a přihlašovací údaje, které se mají použít k ověřování uživatelů.

1. Na hlavní kartě klikněte na **zásady přístupu > AAA servery > Active Directory**. Otevře se obrazovka seznam serverů služby Active Directory.

2. Klikněte na **Vytvořit**. Otevře se obrazovka nové vlastnosti serveru.

3. Do pole **název** zadejte jedinečný název pro Server ověřování.

4. Do pole **název domény** zadejte název domény systému Windows.

5. V nastavení **připojení k serveru** vyberte jednu z těchto možností:

   * Vyberte možnost **použít fond** pro nastavení vysoké dostupnosti pro Server AAA.

   * Vyberte **přímo** a nastavte Server AAA pro samostatnou funkci.

6. Pokud jste vybrali možnost **přímý**, zadejte název do pole **řadič domény** .

7. Pokud jste vybrali možnost použít **fond**, nakonfigurujte fond:

   * Do pole **název fondu řadičů domény** zadejte název.

   * Určete **řadiče domény** ve fondu tak, že pro každý z nich zadáte IP adresu a název hostitele a kliknete na tlačítko **Přidat** .

   * Chcete-li monitorovat stav serveru AAA, máte možnost vybrat monitorování stavu: v tomto případě je vhodný pouze monitor **gateway_icmp** . můžete ji vybrat ze seznamu **monitorování fondu serverů** .

8. V poli **název správce** zadejte pro správce, který má oprávnění správce služby Active Directory, název s rozlišováním velkých a malých písmen. Funkce APM používá informace v polích **jméno správce** a **heslo správce** pro dotaz AD. Pokud je pro anonymní dotazy nakonfigurovaná služba Active Directory, nemusíte zadávat jméno správce. V opačném případě funkce APM potřebuje účet s dostatečným oprávněním k vytvoření vazby na server služby Active Directory, načtení informací o skupině uživatelů a načtení zásad hesel služby Active Directory pro podporu funkcí souvisejících s heslem. (Funkce APM musí načítat zásady hesel, například pokud v akci dotazu AD vyberete možnost Výzva uživatele na změnu hesla před vypršením platnosti.) Pokud v této konfiguraci neposkytnete informace o účtu správce, funkce APM použije uživatelský účet k načtení informací. Tato funkce funguje, pokud má uživatelský účet dostatečná oprávnění.

9. Do pole **heslo správce** zadejte heslo správce přidružené k názvu domény.

10. V poli **ověřit heslo správce** zadejte heslo správce přidružené k nastavení **název domény** .

11. Do pole **Doba života mezipaměti skupiny** zadejte počet dní. Výchozí doba platnosti je 30 dní.

12. Do pole **Doba platnosti mezipaměti objektu zabezpečení hesla** zadejte počet dní. Výchozí doba platnosti je 30 dní.

13. V seznamu **typ šifrování předběžného ověření protokolu Kerberos** vyberte typ šifrování. Výchozí hodnota je **none**. Pokud zadáte typ šifrování, systém BIG-IP obsahuje data předběžného ověření protokolu Kerberos v rámci prvního paketu žádosti ověřovací služby (AS-REQ).

14. Do pole **časový limit** zadejte časový interval (v sekundách) pro Server AAA. (Toto nastavení je volitelné.)

15. Klikněte na **Hotovo**. Nový server se zobrazí v seznamu. Tím se nový server služby Active Directory přidá do seznamu serverů služby Active Directory.

    ![Snímek obrazovky zobrazující oddíly "Obecné vlastnosti" a "konfigurace".](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Konfigurace SAML

1. Certifikát metadat bude nutné importovat do F5, který bude později použit v procesu instalace. Přejděte do **seznamu certifikát > Správa certifikátů > přenos provozu > seznam certifikátů protokolu SSL**. V pravém horním rohu vyberte **importovat** .

    ![Snímek obrazovky se stránkou "Import s certifikátem nebo zdrojem klíčů" s vybraným tlačítkem import.](./media/kerbf5-tutorial/configure18.png)

2. Pro nastavení IDP SAML **přejděte na přístup > federace > SAML: Service Provider > externí konektory IDP** a klikněte na **vytvořit > z metadat**.

    ![Snímek obrazovky se stránkou poskytovatele "S A M L" s "z metadat", která je vybrána v rozevíracím seznamu "vytvořit".](./media/kerbf5-tutorial/configure19.png)

    ![Snímek obrazovky s dialogovým oknem vytvořit novou S A M L A d P konektor](./media/kerbf5-tutorial/configure20.png)

    ![Snímek obrazovky, který zobrazuje okno "upravit S A M L I d P konektor" s vybraným obecným nastavením.](./media/kerbf5-tutorial/configure21.png)

    ![Snímek obrazovky, který zobrazuje okno "upravit S A M L I d P konektor" s vybraným nastavením služby Jednotné přihlašování.](./media/kerbf5-tutorial/configure22.png)

    ![Snímek obrazovky, který zobrazuje okno "upravit S A M L I d P konektor" s vybraným nastavením zabezpečení.](./media/kerbf5-tutorial/configure23.png)

    ![Snímek obrazovky, který zobrazuje okno "upravit S A M L I d P konektor" s vybraným S nastavením služby S.](./media/kerbf5-tutorial/configure24.png)

1. Pokud chcete nastavit službu SAML SP, přejděte k části **přístup > federaci > poskytovatele služby saml > místní služby SP** a klikněte na **vytvořit**. Vyplňte následující informace a klikněte na tlačítko **OK**.

    * Název typu: KerbApp200SAML
    * ID entity *: https://kerb-app.com.cutestat.com
    * Nastavení názvu SP
    * Schéma: https
    * Hostitel: kerbapp200.superdemo.live
    * Popis: kerbapp200.superdemo.live

     ![Snímek obrazovky s oknem upravit S A M L S P Service s vybraným obecným nastavením](./media/kerbf5-tutorial/configure25.png)

     b. Vyberte konfiguraci SP, KerbApp200SAML a klikněte na **BIND/zrušit konektory IDP**.

     ![Snímek obrazovky se stránkou "S A M L Service Provider-Local S P Services", která má vybranou možnost "KerbAPP200 S A M L".](./media/kerbf5-tutorial/configure26.png)

     ![Snímek obrazovky zobrazující, že je vybrané tlačítko pro konektory vytvořit vazbu/vyvážet I d P](./media/kerbf5-tutorial/configure27.png)

     c. Klikněte na **Přidat nový řádek** a vyberte **externí konektor IDP** vytvořený v předchozím kroku, klikněte na **aktualizovat** a pak klikněte na **OK**.

     ![Snímek obrazovky s vybraným oknem pro úpravy S A M L A d PS, který v okně Přidat nový řádek používá toto okno S](./media/kerbf5-tutorial/configure28.png)

1. Pokud chcete nakonfigurovat jednotné přihlašování Kerberos, přejděte na **Access > jednotné přihlašování > Kerberos**, vyplňte informace a klikněte na **Hotovo**.

    >[!Note]
    > Budete potřebovat vytvořit a zadat účet delegování protokolu Kerberos. Odkazování na oddíl KCD (viz příloha odkazy na proměnné)

    * **Username source**: Session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/identity/claims/givenName

    * **Zdroj sféry uživatele**: Session. Logon. Last. Domain

        ![Snímek obrazovky se zvýrazněnou stránkou "Single Sign-On-Properties" se zvýrazněnými textovými poli "zdroj uživatelského jména" a "zdroj sféry uživatele".](./media/kerbf5-tutorial/configure29.png)

1. Pro konfiguraci přístupového profilu přejděte na **access > Profile/zásady > přístup k profilu (zásady pro každou relaci)**, klikněte na **vytvořit**, dokončete následující informace a klikněte na **Hotovo**.

    * Název: KerbApp200
    * Typ profilu: vše
    * Rozsah profilu: Profil
    * Jazyky: angličtina

        ![Snímek obrazovky zobrazující stránku Profiles/policies-Properties s zvýrazněnými textovými poli "název", "typ profilu" a "langauges".](./media/kerbf5-tutorial/configure30.png)

1. Klikněte na název, KerbApp200, vyplňte následující informace a klikněte na **aktualizovat**.

    * Soubor cookie domény: superdemo.live
    * Konfigurace jednotného přihlašování: KerAppSSO_sso

        ![Snímek obrazovky, na které se zobrazuje stránka s názvem domény s D/ověřením se zvýrazněným rozevíracím seznamem "doména soubor cookie" a "S S konfigurací", a vybraným tlačítkem aktualizovat.](./media/kerbf5-tutorial/configure31.png)

1. Klikněte na **zásady přístupu** a pak klikněte na **upravit zásady přístupu** pro profil "KerbApp200".

    ![Snímek obrazovky se stránkou "zásady přístupu" se zvolenou akcí upravit zásady přístupu pro KerbApp200 Profile.](./media/kerbf5-tutorial/configure32.png)

    ![Snímek obrazovky zobrazující stránku "zásady přístupu" a dialogové okno S A M L ověřováním S P](./media/kerbf5-tutorial/configure33.png)

    ![Snímek obrazovky se zvýrazněnou stránkou "zásady přístupu" a dialog přiřazení "proměnné" se zvýrazněnými textovými poli přiřazení.](./media/kerbf5-tutorial/configure34.png)

    * **Session. Logon. Last. usernameUPN expr {[mcget {Session. SAML. Last. identity}]}**

    * **Session. AD. lastactualdomain TEXT superdemo.live**

        ![Snímek obrazovky zobrazující stránku "zásady přístupu" a dialogové okno Active Directory se zvýrazněným textovým polem "SearchFilter".](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {Session. Logon. Last. usernameUPN})**

        ![Snímek obrazovky se stránkou "zásada přístupu" s dialogovým oknem pravidla pro dotazování A D](./media/kerbf5-tutorial/configure36.png)

        ![Snímek obrazovky se zvýrazněnými textovými poli vlastní proměnná a vlastní výraz](./media/kerbf5-tutorial/configure37.png)

    * **Session. Logon. Last. username expr {"[mcget {Session. AD. Last. attr. sAMAccountName}]"}**

        ![Snímek obrazovky, který zobrazuje zvýrazněné textové pole uživatelské jméno z přihlašovací stránky](./media/kerbf5-tutorial/configure38.png)

    * **mcget {Session. Logon. Last. username}**
    * **mcget {Session. Logon. Last. Password**

1. Pokud chcete přidat nový uzel, přejděte na **místní přenos > uzly > seznam uzlů, klikněte na vytvořit**, vyplňte následující informace a pak klikněte na **Hotovo**.

    * Název: KerbApp200
    * Popis: KerbApp200
    * Adresa: 192.168.20.200

        ![Snímek obrazovky zobrazující stránku nový uzel s zvýrazněnými textovými poli "název", popis "a" adresa "a vybrané tlačítko" dokončeno ".](./media/kerbf5-tutorial/configure39.png)

1. Pokud chcete vytvořit nový fond, přejděte na **místní provoz > fondy > seznam fondů, klikněte na vytvořit**, dokončete následující informace a klikněte na **Hotovo**.

    * Název: KerbApp200-Pool
    * Popis: KerbApp200-Pool
    * Monitory stavu: http
    * Adresa: 192.168.20.200
    * Port služby: 81

        ![Snímek obrazovky se stránkou "nový fond" s zadanými hodnotami a vybraným tlačítkem "dokončeno".](./media/kerbf5-tutorial/configure40.png)

1. Pro vytvoření virtuálního serveru přejděte na **místní provoz > virtuální servery > seznam virtuálních serverů > +**, dokončete následující informace a klikněte na **Hotovo**.

    * Název: KerbApp200
    * Cílová adresa/maska: hostitel 192.168.30.200
    * Port služby: port 443 HTTPS
    * Profil přístupu: KerbApp200
    * Zadejte profil přístupu vytvořený v předchozím kroku.

        ![Snímek obrazovky zobrazující stránku "seznam virtuálních serverů" s názvem "název", "cílová adresa/maska" a "port služby", kde jsou zvýrazněna.](./media/kerbf5-tutorial/configure41.png)

        ![Snímek obrazovky zobrazující stránku seznam virtuálních serverů s zvýrazněným rozevíracím seznamem profil přístupu](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Nastavení delegování protokolu Kerberos 

>[!NOTE]
>Kliknutím [sem](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) zobrazíte odkaz.

*  **Krok 1:** Vytvoření účtu delegování

    **Příklad:**
    * Název domény: **superdemo.Live**

    * Název účtu SAM: **Big-ipuser**

    * New-ADUser-Name "účet delegování APM"-UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -sAMAccountName "Big-ipuser"-PasswordNeverExpires $true-Enabled $true-AccountPassword (Read-Host-AsSecureString "Password! 1234")

* **Krok 2:** Nastavit hlavní název služby (SPN) (na účtu delegování APM)

    **Příklad:**
    * setspn – A **Host/Big-ipuser. demo. Live** Big-ipuser

* **Krok 3:** Delegování hlavního názvu služby (pro účet App Service) nastaví příslušné delegování pro účet delegace F5.
    V následujícím příkladu je účet delegování APM nakonfigurovaný pro KCD pro FRP-app1. demo. živá aplikace

    ![F5 (Kerberos) – konfigurace](./media/kerbf5-tutorial/configure43.png)

* Zadejte podrobnosti uvedené v předchozím referenčním [dokumentu.](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="create-f5-test-user"></a>Vytvořit testovacího uživatele F5

V této části vytvoříte na F5 uživatele s názvem B. Simon. Pokud chcete přidat uživatele na platformě F5, pracujte s nástrojem [F5 Client Support Team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici F5 na přístupovém panelu byste měli být automaticky přihlášeni k F5, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Zkuste F5 pomocí Azure AD](https://aad.portal.azure.com/)

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](headerf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

- [Služba F5 BIG-IP APM a Integrace Azure AD pro zabezpečený hybridní přístup](../manage-apps/f5-aad-integration.md)

- [Kurz nasazení virtuálního počítače F5 BIG-IP Virtual Edition ve službě Azure IaaS pro zajištění zabezpečeného hybridního přístupu](../manage-apps/f5-bigip-deployment-guide.md)

- [Kurz pro Azure Active Directory integraci s jednotným přihlašováním pomocí F5 BIG-IP pro heslo bez hesla – bez VPN](../manage-apps/f5-aad-password-less-vpn.md)