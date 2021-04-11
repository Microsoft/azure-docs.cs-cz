---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Akamai | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: a22a214104357b9ad99238a8db157839a1c9fd46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Akamai

V tomto kurzu se dozvíte, jak integrovat Akamai s Azure Active Directory (Azure AD). Když integrujete Akamai s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Akamai.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Akamai svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Integrace podnikových aplikací Azure Active Directory a Akamai umožňuje bezproblémový přístup ke starším aplikacím hostovaným v cloudu nebo místním prostředí. Integrované řešení využívá výhod všech moderních možností Azure Active Directory jako je [Azure AD podmíněný přístup](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) a [Azure AD identity governance](../governance/identity-governance-overview.md) pro starší verze aplikací, které nemají přístup k změnám aplikací nebo instalacím agentů.

Následující obrázek popisuje, kde Akamai EAA zapadá do širšího scénáře hybridního zabezpečeného přístupu.

![Akamai EAA se zahodí do širšího scénáře hybridního zabezpečeného přístupu](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Scénáře ověřování klíčů

Kromě Azure Active Directory podpora nativní integrace pro moderní protokoly ověřování, jako je Open ID Connect, SAML a WS-EAA, rozšiřuje zabezpečený přístup pro aplikace založené na starších verzích pro interní i externí přístup s Azure AD a povoluje pro tyto aplikace moderní scénáře (například přístup bez hesla). Sem patří:

* Ověřovací aplikace založené na hlavičkách
* Vzdálená plocha
* SSH (Secure Shell)
* Aplikace pro ověřování pomocí protokolu Kerberos
* VNC (Virtual Network Computing)
* Anonymní ověřování nebo žádné sestavené ověřovací aplikace
* Aplikace ověřování NTLM (ochrana se dvěma výzvami pro uživatele)
* Aplikace Forms-Based (ochrana se dvěma výzvami pro uživatele)

### <a name="integration-scenarios"></a>Scénáře integrace

Partnerství s Microsoftem a Akamai EAA umožňuje flexibilitu v souladu s vašimi podnikovými požadavky tím, že podporuje více scénářů integrace podle požadavků vaší firmy. Ty je možné použít k zajištění nulového pokrytí napříč všemi aplikacemi a postupně klasifikovat a konfigurovat vhodné klasifikace zásad.

#### <a name="integration-scenario-1"></a>Scénář integrace 1

Akamai EAA je nakonfigurovaný jako jediná aplikace v Azure AD. Správce může konfigurovat zásady podmíněného přístupu v aplikaci a jakmile se podmínky splní, můžou získat přístup k portálu Akamai EAA.

**Profesionály**:

* Musíte nakonfigurovat IDP jenom jednou.

**Nevýhody**:

* Uživatelé mají dva portály aplikací.

* Jedno společné pokrytí zásad podmíněného přístupu pro všechny aplikace.

![Scénář integrace 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Scénář integrace 2

Aplikace Akamai EAA se nastavuje individuálně na portálu Azure AD. Správce může nakonfigurovat jednotlivé zásady podmíněného přístupu v aplikacích a jakmile jsou podmínky splněné, můžou být uživatelé přesměrováni přímo na konkrétní aplikaci.

**Profesionály**:

* Můžete definovat jednotlivé zásady certifikační autority.

* Všechny aplikace jsou reprezentovány na panelu 0365 dlaždice a myApps.microsoft.com.


**Nevýhody**:

* Je potřeba nakonfigurovat víc IDP.

![Scénář integrace 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Akamai odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

- Akamai podporuje jednotné přihlašování (SSO) iniciované IDP.

#### <a name="important"></a>Důležité

Všechna níže uvedená nastavení jsou stejná pro **scénář integrace 1** a **scénář 2**. V případě **scénáře integrace 2** musíte nastavit jednotlivé IDPy ve Akamai EAA a vlastnost URL musí být upravena tak, aby odkazovala na adresu URL aplikace.

![Snímek obrazovky karty Obecné pro AZURESSO-SP v Akamai Enterprise Application Access Pole Adresa URL konfigurace ověřování je zvýrazněné.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Přidání Akamai z Galerie

Pokud chcete nakonfigurovat integraci Akamai do služby Azure AD, musíte přidat Akamai z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Akamai** .
1. Na panelu výsledků vyberte **Akamai** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Akamai

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Akamai pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Akamai.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Akamai postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Akamai SSO](#configure-akamai-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Nastavení IDP](#setting-up-idp)**
    * **[Ověřování na základě hlaviček](#header-based-authentication)**
    * **[Vzdálená plocha](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Ověřování protokolu Kerberos](#kerberos-authentication)**
    * **[Vytvořte Akamai Test User](#create-akamai-test-user)** -to, abyste měli protějšek B. Simon v Akamai, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Akamai** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Akamai](https://www.akamai.com/us/en/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Akamai** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Akamai.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Akamai**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-akamai-sso"></a>Konfigurace jednotného přihlašování Akamai

### <a name="setting-up-idp"></a>Nastavení IDP

**AKAMAI EAA konfigurace IDP**

1. Přihlaste se ke konzole **Akamai Enterprise Application Access** .
1. V **konzole Akamai EAA** vyberte   >  **Zprostředkovatelé** identity identity a klikněte na **Přidat zprostředkovatele identity**.

    ![Snímek obrazovky okna zprostředkovatelé identit konzoly Akamai EAA V nabídce identita vyberte zprostředkovatelé identity a vyberte Přidat poskytovatele identity.](./media/header-akamai-tutorial/configure-1.png)

1. Na stránce **vytvořit nového zprostředkovatele identity** proveďte následující kroky:

    ![Snímek obrazovky dialogového okna vytvořit nové zprostředkovatele identity v konzole Akamai EAA](./media/header-akamai-tutorial/configure-2.png)

    a. Zadejte **jedinečný název**.

    b. Vyberte protokol **SAML třetí strany** a klikněte na **vytvořit zprostředkovatele identity a nakonfigurovat**.

### <a name="general-settings"></a>Obecná nastavení

1. **Zachycení identity** – zadejte název (základní adresa URL služby SP – bude použit pro konfiguraci Azure AD).

    > [!NOTE]
    > Můžete si vybrat, že budete mít vlastní doménu (budete potřebovat položku DNS a certifikát). V tomto příkladu budeme používat doménu Akamai.

1. **Akamai Cloud Zone** – vyberte příslušnou zónu cloudu.
1. **Ověření certifikátu** – Projděte si dokumentaci k Akamai (volitelné).

    ![Snímek obrazovky s kartou Akamai EAA Console General zobrazující nastavení pro zachycení identity, Akamai Cloud Zone a ověření certifikátu.](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Konfigurace ověřování

1. Adresa URL – zadejte adresu URL shodnou s průsečíkem identity (tady se uživatelé přesměrují po ověření).
2. Odhlašovací adresa URL: Aktualizujte adresu URL pro odhlášení.
3. Podepsat požadavek SAML: výchozí není zaškrtnuto.
4. V případě souboru metadat IDP přidejte aplikaci do konzoly služby Azure AD.

    ![Snímek obrazovky s konfigurací ověřování konzoly Akamai EAA s nastavením pro URL, odhlašovací URL, podpisem požadavku SAML a souborem metadat IDP](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Nastavení relace

Ponechte nastavení jako výchozí.

![Snímek obrazovky dialogového okna nastavení relace konzoly Akamai EAA](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Adresáře

Přeskočte konfiguraci adresáře.

![Snímek obrazovky karty adresáře konzoly Akamai EAA](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Přizpůsobení uživatelského rozhraní

Do IDP můžete přidat vlastní nastavení.

![Snímek obrazovky s kartou pro přizpůsobení konzoly Akamai EAA, která zobrazuje nastavení pro přizpůsobení uživatelského rozhraní, nastavení jazyka a motivů.](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Upřesnit nastavení

Přeskočit nastavení zálohy/další podrobnosti najdete v dokumentaci k Akamai.

![Snímek obrazovky s kartou Upřesnit nastavení konzoly Akamai EAA, která zobrazuje nastavení pro klienta EAA, rozšířené a OIDC pro přemostění SAML.](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Nasazení

1. Klikněte na nasadit poskytovatele identity.

    ![Snímek obrazovky s kartou nasazení konzoly Akamai EAA se zobrazeným tlačítkem nasadit poskytovatele odsazení](./media/header-akamai-tutorial/deployment.png)

2. Ověřte, že nasazení proběhlo úspěšně.

### <a name="header-based-authentication"></a>Ověřování na základě hlaviček

Akamai ověřování na základě hlaviček

1. Výběr **vlastního formuláře http** Průvodce přidáním aplikací.

    ![Snímek obrazovky s průvodcem přidat aplikace v konzole Akamai EAA zobrazující CustomHTTP uvedený v části Access Apps](./media/header-akamai-tutorial/configure-5.png)

2. Zadejte **název** a **Popis** aplikace.

    ![Snímek obrazovky s dialogem vlastní aplikace HTTP zobrazující nastavení názvu a popisu aplikace](./media/header-akamai-tutorial/configure-6.png)

    ![Snímek obrazovky s kartou Akamai EAA Console General zobrazující Obecné nastavení pro MYHEADERAPP.](./media/header-akamai-tutorial/configure-7.png)

    ![Snímek obrazovky s konzolou Akamai EAA se zobrazením nastavení pro certifikát a umístění](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Authentication

1. Vyberte kartu **ověřování** .

    ![Snímek obrazovky konzoly Akamai EAA s vybraným tabulátorem ověřování](./media/header-akamai-tutorial/configure-9.png)

2. Přiřaďte **poskytovatele identity**.

    ![Snímek obrazovky karty ověřování konzoly Akamai EAA pro MYHEADERAPP ukazující poskytovatele identity nastaveného na jednotné přihlašování Azure AD](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Služby

Klikněte na Uložit a přejděte na ověřování.

![Snímek obrazovky s kartou Akamai EAA Console Services pro MYHEADERAPP zobrazující tlačítko Uložit a přejít na AdvancedSettings v pravém dolním rohu.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

1. V **záhlaví HTTP zákazníka** zadejte atribut **CustomerHeader** a **SAML**.

    ![Snímek obrazovky s kartou Upřesnit nastavení konzoly Akamai EAA, která zobrazuje pole Adresa URL protokolu SSO zvýrazněné v oblasti ověřování](./media/header-akamai-tutorial/configure-12.png)

1. Klikněte na **Uložit a přejděte na tlačítko nasazení** .

    ![Snímek obrazovky s kartou Upřesnit nastavení konzoly Akamai EAA, která zobrazuje tlačítko Uložit a přejít k nasazení v pravém dolním rohu.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>Nasazení aplikace

1. Klikněte na tlačítko **nasadit aplikaci** .

    ![Snímek obrazovky s kartou nasazení konzoly Akamai EAA se zobrazeným tlačítkem nasadit aplikaci.](./media/header-akamai-tutorial/configure-14.png)

1. Ověřte, že aplikace byla úspěšně nasazena.

    ![Snímek obrazovky s kartou nasazení konzoly Akamai EAA, která zobrazuje stavovou zprávu aplikace: "aplikace byla úspěšně nasazena".](./media/header-akamai-tutorial/configure-15.png)

1. Prostředí End-User.

    ![Snímek obrazovky s úvodní obrazovkou pro myapps.microsoft.com s obrázkem na pozadí a dialogem pro přihlášení](./media/header-akamai-tutorial/end-user-1.png)

    ![Snímek obrazovky, který ukazuje část okna aplikace s ikonami pro doplněk, HRWEB, Akamai-CorpApps, výdaje, skupiny a kontroly přístupu. ](./media/header-akamai-tutorial/end-user-2.png)

1. Podmíněný přístup.

    ![Snímek obrazovky se zprávou: schvalte žádost o přihlášení. Poslali jsme vám oznámení na vaše mobilní zařízení. Abyste mohli pokračovat, Odpovězte prosím.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Snímek obrazovky aplikací, na které se zobrazuje ikona pro MyHeaderApp](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Vzdálená plocha

1. V Průvodci přidáním aplikací vyberte **RDP** .

    ![Snímek obrazovky s konzolou Průvodce přidáním aplikací v konzole Akamai EAA, která zobrazuje protokol RDP uvedený mezi aplikacemi v části aplikace Access Apps](./media/header-akamai-tutorial/configure-16.png)

1. Zadejte **název** a **Popis** aplikace.

    ![Snímek obrazovky dialogového okna aplikace RDP zobrazující nastavení názvu a popisu aplikace](./media/header-akamai-tutorial/configure-17.png)

    ![Snímek obrazovky s kartou Akamai EAA Console General ukazující nastavení identity aplikace pro SECRETRDPAPP.](./media/header-akamai-tutorial/configure-18.png)

1. Zadejte konektor, který bude obsluhovat.

    ![Snímek obrazovky s konzolou Akamai EAA se zobrazením nastavení pro certifikát a umístění Přidružené konektory jsou nastavené na USWST-CON1.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Authentication

Klikněte na **Uložit a přejděte na služby**.

![Snímek obrazovky karty ověřování konzoly Akamai EAA pro SECRETRDPAPP zobrazující tlačítko Uložit a přejít k službám je v pravém dolním rohu.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Služby

Klikněte na **Uložit a přejděte na Upřesnit nastavení**.

![Snímek obrazovky s kartou Akamai EAA Console Services pro SECRETRDPAPP zobrazující tlačítko Uložit a přejít na AdvancedSettings v pravém dolním rohu.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

1. Klikněte na **Uložit a přejděte na nasazení**.

    ![Snímek obrazovky karty Rozšířená nastavení konzoly Akamai EAA pro SECRETRDPAPP zobrazující nastavení pro konfiguraci vzdálené plochy](./media/header-akamai-tutorial/configure-22.png)

    ![Snímek obrazovky karty Rozšířená nastavení konzoly Akamai EAA pro SECRETRDPAPP zobrazující nastavení pro ověřování a konfiguraci kontroly stavu.](./media/header-akamai-tutorial/configure-23.png)

    ![Snímek obrazovky s vlastním nastavením hlaviček protokolu HTTP v konzole Akamai EAA pro SECRETRDPAPP pomocí tlačítka Uložit a přejít k nasazení v pravém dolním rohu.](./media/header-akamai-tutorial/configure-24.png)

1. Prostředí End-User

    ![Snímek obrazovky okna myapps.microsoft.com s obrázkem na pozadí a dialogem pro přihlášení](./media/header-akamai-tutorial/end-user-3.png)

    ![Snímek obrazovky s oknem aplikací myapps.microsoft.com s ikonami pro doplněk, HRWEB, Akamai-CorpApps, výdaje, skupiny a kontroly přístupu](./media/header-akamai-tutorial/end-user-2.png)

1. Podmíněný přístup

    ![Snímek obrazovky s podmíněným přístupem: schválit žádost o přihlášení Poslali jsme vám oznámení na vaše mobilní zařízení. Abyste mohli pokračovat, Odpovězte prosím.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Snímek obrazovky s obrazovkami aplikací, které zobrazují ikony pro MyHeaderApp a SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Snímek obrazovky s Windows Serverem 2012 RS zobrazující ikony obecných uživatelů Ikony pro správce, user0 a user1 ukazují, že jsou přihlášeni.](./media/header-akamai-tutorial/conditional-access-6.png)

1. Případně můžete také přímo zadat adresu URL aplikace RDP.

#### <a name="ssh"></a>SSH

1. Přejít na Přidat aplikace, zvolit **SSH**.

    ![Snímek obrazovky s konzolou pro přidání aplikací v konzole Akamai EAA se zobrazeným protokolem SSH v části aplikace v aplikaci Access Apps.](./media/header-akamai-tutorial/configure-25.png)

1. Zadejte **název** a **Popis** aplikace.

    ![Snímek obrazovky dialogového okna aplikace SSH zobrazující nastavení názvu a popisu aplikace](./media/header-akamai-tutorial/configure-26.png)

1. Nakonfigurujte identitu aplikace.

    ![Snímek obrazovky s kartou Akamai EAA Console General zobrazující nastavení identity aplikace pro SSH-SECURE.](./media/header-akamai-tutorial/configure-27.png)

    a. Zadejte název/popis.

    b. Zadejte IP adresu/plně kvalifikovaný název domény aplikačního serveru a port pro SSH.

    c. Zadejte uživatelské jméno/přístupové heslo SSH * ověřte Akamai EAA.

    d. Zadejte název externího hostitele.

    e. Zadejte umístění konektoru a zvolte konektor.

#### <a name="authentication"></a>Authentication

Klikněte na **Uložit a přejděte na služby**.

![Snímek obrazovky karty ověřování konzoly Akamai EAA pro SSH – SECURE, na které se zobrazuje tlačítko Uložit a přejít na služby, se nachází v pravém dolním rohu.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Služby

Klikněte na **Uložit a přejděte na Upřesnit nastavení**.

![Snímek obrazovky s kartou Akamai EAA Console Services pro SSH – SECURE zobrazující tlačítko Uložit a přejít na AdvancedSettings v pravém dolním rohu.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

Klikněte na Uložit a přejděte na nasazení.

![Snímek obrazovky karty Rozšířená nastavení konzoly Akamai EAA pro SSH – SECURE, která zobrazuje nastavení pro ověřování a konfiguraci kontroly stavu.](./media/header-akamai-tutorial/configure-30.png)

![Snímek obrazovky s vlastním nastavením hlaviček protokolu HTTP v konzole Akamai EAA pro SSH – SECURE pomocí tlačítka Uložit a přejít k nasazení v pravém dolním rohu.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Nasazení

1. Klikněte na **nasadit aplikaci**.

    ![Snímek obrazovky s kartou nasazení konzoly Akamai EAA pro SSH – SECURE, který zobrazuje tlačítko nasadit aplikaci.](./media/header-akamai-tutorial/configure-32.png)

1. Prostředí End-User

    ![Snímek obrazovky s dialogovým oknem přihlášení k oknu myapps.microsoft.com](./media/header-akamai-tutorial/end-user-3.png)

    ![Snímek obrazovky okna aplikace pro myapps.microsoft.com znázorňující ikony pro doplňky, HRWEB, Akamai-CorpApps, výdaje, skupiny a kontroly přístupu](./media/header-akamai-tutorial/end-user-4.png)

1. Podmíněný přístup

    ![Snímek obrazovky zobrazující zprávu: schválit žádost o přihlášení Poslali jsme vám oznámení na vaše mobilní zařízení. Abyste mohli pokračovat, Odpovězte prosím.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Snímek obrazovky aplikací znázorňující ikony pro MyHeaderApp, zabezpečení SSH a SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Snímek obrazovky s příkazovým oknem pro ssh-secure-go.akamai-access.com, na kterém se zobrazuje výzva k zadání hesla](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Snímek obrazovky s příkazovým oknem pro ssh-secure-go.akamai-access.com zobrazující informace o aplikaci a zobrazení výzvy pro příkazy](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Ověřování protokolu Kerberos

V níže uvedeném příkladu budeme publikovat interní webový server <code>http://frp-app1.superdemo.live</code> a povolit jednotné přihlašování pomocí KCD.

#### <a name="general-tab"></a>Karta Obecné

![Snímek obrazovky s kartou Akamai EAA Console General pro MYKERBOROSAPP](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Karta ověřování

Přiřaďte poskytovatele identity.

![Snímek obrazovky karty ověřování konzoly Akamai EAA pro MYKERBOROSAPP ukazující poskytovatele identity nastaveného na jednotné přihlašování Azure AD](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Karta služby

![Snímek obrazovky s kartou Akamai EAA Console Services pro MYKERBOROSAPP.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

![Snímek obrazovky karty Rozšířená nastavení konzoly Akamai EAA pro MYKERBOROSAPP zobrazující nastavení pro související aplikace a ověřování.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> Hlavní název služby (SPN) webového serveru má SPN@Domain Formát ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` pro tuto ukázku. Nechte zbývající nastavení výchozí.

#### <a name="deployment-tab"></a>Karta nasazení

![Snímek obrazovky s kartou nasazení konzoly Akamai EAA pro MYKERBOROSAPP zobrazující tlačítko nasadit aplikaci](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Přidávání adresáře

1. V rozevíracím seznamu vyberte **AD** .

    ![Snímek obrazovky okna adresáře konzoly Akamai EAA, ve kterém se v rozevíracím seznamu pro typ adresáře vybere dialogové okno vytvořit nový adresář se službou AD.](./media/header-akamai-tutorial/configure-33.png)

1. Zadejte potřebná data.

    ![Snímek obrazovky s oknem SUPERDEMOLIVE konzoly Akamai EAA s nastaveními pro adresář, adresářovou službu, konektor a mapování atributů](./media/header-akamai-tutorial/configure-34.png)

1. Ověřte vytvoření adresáře.

    ![Snímek obrazovky okna adresáře konzoly Akamai EAA s informacemi o tom, že byl přidán adresář superdemo.live](./media/header-akamai-tutorial/directory-domain.png)

1. Přidejte skupiny/organizační jednotky, které by vyžadovaly přístup.

    ![Snímek obrazovky s nastavením adresáře superdemo.live. Ikona, kterou vyberete pro přidání skupin nebo organizačních jednotek, je zvýrazněna.](./media/header-akamai-tutorial/add-group.png)

1. V níže uvedené skupině se říká EAAGroup a má 1 člen.

    ![Snímek obrazovky skupin konzoly Akamai EAA v okně adresáře SUPERDEMOLIVE EAAGroup s 1 uživatelem je uvedeno v části skupiny.](./media/header-akamai-tutorial/eaagroup.png)

1. Přidejte adresář k poskytovateli identity kliknutím na   >  **zprostředkovatele identity** identity, klikněte na kartu **adresáře** a klikněte na **přiřadit adresář**.

    ![Snímek obrazovky karty adresáře konzoly Akamai EAA pro jednotné přihlašování Azure AD zobrazující superdemo.live v seznamu aktuálně přiřazených adresářů.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Postup konfigurace delegování KCD pro EAA

#### <a name="step-1-create-an-account"></a>Krok 1: vytvoření účtu 

1. V tomto příkladu použijeme účet s názvem **EAADelegation**. To můžete provést pomocí Snappin **Uživatelé a počítače služby Active Directory** .

    ![Snímek obrazovky karty adresáře konzoly Akamai EAA pro jednotné přihlašování Azure AD Adresář superdemo.live je uveden v části aktuálně přiřazené adresáře.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > Uživatelské jméno musí být v určitém formátu založeném na **názvu zachycení identity**. Z obrázku 1 vidíte, že se jedná o **corpapps.Login.go.Akamai-Access.com** .

1. Přihlašovací jméno uživatele bude:`HTTP/corpapps.login.go.akamai-access.com`

    ![Snímek obrazovky zobrazující vlastnosti EAADelegation s křestním názvem nastavenou na "EAADelegation" a přihlašovací jméno uživatele nastavené na HTTP/corpapps. Login. Přejít. Akamai-Access. com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Krok 2: Konfigurace hlavního názvu služby (SPN) pro tento účet

1. Na základě této ukázky bude hlavní název služby (SPN), jak je uvedeno níže.

2. setspn-s **http/corpapps. Login. Přejít. Akamai-Access. com eaadelegation**

    ![Snímek obrazovky s příkazovým řádkem Správce zobrazující výsledky příkazu setspn-s http/corpapps. Login. Přejít. Akamai-Access. com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Krok 3: Konfigurace delegování

1. Pro účet EAADelegation klikněte na kartu delegování.

    ![Snímek obrazovky s příkazovým řádkem Správce zobrazující příkaz pro konfiguraci hlavního názvu služby (SPN).](./media/header-akamai-tutorial/spn.png)

    * Zadejte použít libovolný protokol pro ověřování.
    * Klikněte na Přidat a přidejte účet fondu aplikací pro web Kerberos. V případě správné konfigurace by se měla automaticky přeložit na správný název SPN.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Krok 4: vytvoření souboru keytab pro AKAMAI EAA

1. Zde je Obecná syntaxe.

1. Ktpass/out ActiveDirectorydomain. keytab/princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /Pass + RdnPass/Crypto All/pType KRB5_NT_PRINCIPAL

1. Příklad vysvětlení

    | Fragment kódu | Vysvětlení |
    | - | - |
    | Ktpass/out EAADemo. keytab | Název výstupního souboru keytab |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | Účet delegování EAA |
    | /pass RANDOMPASS | Heslo účtu delegování EAA |
    | /Crypto všechny pType KRB5_NT_PRINCIPAL | Projděte si dokumentaci ke službě Akamai EAA |
    | | |

1. Ktpass/out EAADemo. keytab/princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /Pass RANDOMPASS/Crypto All pType KRB5_NT_PRINCIPAL

    ![Snímek obrazovky s příkazovým řádkem Správce zobrazující výsledky příkazu pro vytvoření souboru keytab pro AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Krok 5: import keytab v konzole EAA AKAMAI

1. Klikněte na kartu **systémové**  >  **karty**.

    ![Snímek obrazovky s konzolou Akamai EAA s vybranými kartami ze systémové nabídky](./media/header-akamai-tutorial/keytabs.png)

1. V keytab typ vyberte **delegování protokolu Kerberos**.

    ![Snímek obrazovky EAAKEYTAB konzoly Akamai EAA zobrazující nastavení keytab Typ keytab je nastaven na delegování protokolu Kerberos.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Ujistěte se, že se keytab zobrazí jako nasazené a ověřené.

    ![Snímek obrazovky s kartami kláves v konzole Akamai EAA s EAA keytab jako "nasazené a ověřené" keytab ".](./media/header-akamai-tutorial/keytabs-2.png)

1. Zkušenosti uživatele

    ![Snímek obrazovky s dialogovým oknem přihlášení na adrese myapps.microsoft.com. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Snímek obrazovky okna aplikace, ve kterém se myapps.microsoft.com zobrazují ikony aplikací](./media/header-akamai-tutorial/end-user-4.png)

1. Podmíněný přístup

    ![Snímek obrazovky se zprávou schválení žádosti o přihlášení zpráva.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Snímek obrazovky aplikací znázorňující ikony pro MyHeaderApp, zabezpečení SSH, SecretRDPApp a myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Snímek obrazovky s úvodní obrazovkou pro myKerberosApp Přes obrázek pozadí se zobrazí zpráva "uvítací superdemo\user1".](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Vytvořit testovacího uživatele Akamai

V této části vytvoříte uživatele s názvem B. Simon v Akamai. Pokud chcete přidat uživatele na platformě Akamai, pracujte s [týmem podpory klienta Akamai](https://www.akamai.com/us/en/contact-us/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k Akamai, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Akamai v okně moje aplikace, měli byste se automaticky přihlásit k Akamai, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Akamai můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).