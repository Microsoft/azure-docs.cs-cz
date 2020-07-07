---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Akamai | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17d0c91d31f7746c53d62af87670c40e9902554c
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026810"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Akamai

V tomto kurzu se dozvíte, jak integrovat Akamai s Azure Active Directory (Azure AD). Když integrujete Akamai s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Akamai.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Akamai svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Integrace podnikových aplikací Azure Active Directory a Akamai umožňuje bezproblémový přístup ke starším aplikacím hostovaným v cloudu nebo místním prostředí. Integrované řešení využívá výhod všech moderních možností Azure Active Directory jako je [Azure AD podmíněný přístup](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) a [Azure AD identity governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview) pro starší verze aplikací, které nemají přístup k změnám aplikací nebo instalacím agentů.

Následující obrázek popisuje, kde Akamai EAA zapadá do širšího scénáře hybridního zabezpečeného přístupu.

![Akamai EAA se zahodí do širšího scénáře hybridního zabezpečeného přístupu](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Scénáře ověřování klíčů

Kromě Azure Active Directory podpora nativní integrace pro moderní protokoly ověřování, jako je Open ID Connect, SAML a WS-EAA, rozšiřuje zabezpečený přístup pro aplikace založené na starších verzích pro interní i externí přístup s Azure AD a povoluje pro tyto aplikace moderní scénáře (například přístup bez hesla). Sem patří:

* Ověřovací aplikace založené na hlavičkách
* Vzdálená plocha
* SSH (Secure Shell)
* Aplikace pro ověřování pomocí protokolu Kerberos
* VNC (Virtual Network Computing)
* Anonymní ověřování nebo žádné sestavené ověřovací aplikace
* Aplikace ověřování NTLM (ochrana se dvěma výzvami pro uživatele)
* Aplikace založené na formulářích (ochrana se dvěma výzvami pro uživatele)

### <a name="integration-scenarios"></a>Scénáře integrace

Partnerství s Microsoftem a Akamai EAA umožňuje flexibilitu v souladu s vašimi podnikovými požadavky tím, že podporuje více scénářů integrace podle požadavků vaší firmy. Ty je možné použít k zajištění nulového pokrytí napříč všemi aplikacemi a postupně klasifikovat a konfigurovat vhodné klasifikace zásad.

#### <a name="integration-scenario-1"></a>Scénář integrace 1

Akamai EAA je nakonfigurovaný jako jediná aplikace v Azure AD. Správce může pro aplikaci nakonfigurovat zásady certifikační autority a jakmile se podmínky splní, můžou získat přístup k portálu Akamai EAA.

**Profesionály**:

• Musíte jenom nakonfigurovat IDP jenom jednou.

**Nevýhody**:

• Uživatelé mají na portále dva aplikace

• Jednotné společné pokrytí zásad certifikační autority pro všechny aplikace.

![Scénář integrace 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Scénář integrace 2

Aplikace Akamai EAA se nastavuje individuálně na portálu Azure AD. Správce může pro aplikace nakonfigurovat jednotlivé zásady certifikační autority a po splnění podmínek můžou být uživatelé přesměrováni přímo na konkrétní aplikaci.

**Profesionály**:

• Můžete definovat jednotlivé zásady certifikační autority.

• Všechny aplikace jsou reprezentovány na panelu O365 dlaždice a myApps.microsoft.com.

**Nevýhody**:

• Je potřeba nakonfigurovat víc IDP.

![Scénář integrace 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Akamai odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

- Akamai podporuje jednotné přihlašování IDP.

#### <a name="important"></a>Důležité

Všechna níže uvedená nastavení jsou stejná pro **scénář integrace 1** a **scénář 2**. V případě **scénáře integrace 2** musíte nastavit jednotlivé IDPy ve Akamai EAA a vlastnost URL musí být upravena tak, aby odkazovala na adresu URL aplikace.

![Důležité](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Přidání Akamai z Galerie

Pokud chcete nakonfigurovat integraci Akamai do služby Azure AD, musíte přidat Akamai z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Akamai** .
1. Na panelu výsledků vyberte **Akamai** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Akamai

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Akamai pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Akamai.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Akamai, dokončete následující stavební bloky:

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

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Akamai** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Akamai](https://www.akamai.com/us/en/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Akamai** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Akamai.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Akamai**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-akamai-sso"></a>Konfigurace jednotného přihlašování Akamai

### <a name="setting-up-idp"></a>Nastavení IDP

**AKAMAI EAA konfigurace IDP**

1. Přihlaste se ke konzole **Akamai Enterprise Application Access** .
1. V **konzole Akamai EAA**vyberte **Identity**  >  **Zprostředkovatelé** identity identity a klikněte na **Přidat zprostředkovatele identity**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure01.png)

1. Na stránce **vytvořit nového zprostředkovatele identity** proveďte následující kroky:

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Zadejte **jedinečný název**.

    b. Vyberte protokol **SAML třetí strany** a klikněte na **vytvořit zprostředkovatele identity a nakonfigurovat**.

### <a name="general-settings"></a>Obecná nastavení

1. **Zachycení identity** – zadejte název (základní adresa URL služby SP – bude použit pro konfiguraci Azure AD)

    > [!NOTE]
    > Můžete si vybrat, že budete mít vlastní doménu (budete potřebovat položku DNS a certifikát). V tomto příkladu budeme používat doménu Akamai.

1. **Akamai Cloud Zone** – vyberte příslušnou zónu cloudu.
1. **Ověření certifikátu** – kontrola dokumentace Akamai (volitelné)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Konfigurace ověřování

1. Adresa URL – zadejte adresu URL shodnou s průsečíkem identity (tady se uživatelé přesměrují po ověření).
2. Odhlašovací adresa URL: Aktualizujte adresu URL pro odhlášení.
3. Podepsat požadavek SAML: výchozí není zaškrtnuto.
4. V případě souboru metadat IDP přidejte aplikaci do konzoly služby Azure AD.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Nastavení relace

Ponechte nastavení jako výchozí.

![Konfigurace Akamai](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Adresáře

Přeskočte konfiguraci adresáře.

![Konfigurace Akamai](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Přizpůsobení uživatelského rozhraní

Do IDP můžete přidat vlastní nastavení.

![Konfigurace Akamai](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Upřesnit nastavení

Přeskočit nastavení zálohy/další podrobnosti najdete v dokumentaci k Akamai.

![Konfigurace Akamai](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Nasazení

1. Klikněte na nasadit poskytovatele identity.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/deployment.png)

2. Ověření úspěšného nasazení

### <a name="header-based-authentication"></a>Ověřování na základě hlaviček

Akamai ověřování na základě hlaviček

1. Výběr **vlastního formuláře http** Průvodce přidáním aplikací.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure05.png)

2. Zadejte **název** a **Popis**aplikace.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Authentication

1. Vyberte kartu **ověřování** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure09.png)

2. Přiřazení **zprostředkovatele identity**

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Služby

Klikněte na Uložit a přejděte na ověřování.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

1. V **záhlaví HTTP zákazníka**zadejte atribut **CustomerHeader** a **SAML**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure12.png)

1. Klikněte na **Uložit a přejděte na tlačítko nasazení** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Nasazení aplikace

1. Klikněte na tlačítko **nasadit aplikaci** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure14.png)

1. Ověřte, že aplikace byla úspěšně nasazena.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure15.png)

1. Činnost koncového uživatele.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser01.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Podmíněný přístup.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Vzdálená plocha

1. V Průvodci přidáním aplikací vyberte **RDP** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure16.png)

1. Zadejte **název** a **Popis**aplikace.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure18.png)

1. Zadejte konektor, který bude obsluhovat.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Authentication

Klikněte na **Uložit a přejděte na služby**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Služby

Klikněte na **Uložit a přejděte na Upřesnit nastavení**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

1. Klikněte na **Uložit a přejděte na nasazení**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure22.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure23.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure24.png)

1. Činnost koncového uživatele

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Podmíněný přístup

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Případně můžete také přímo zadat adresu URL aplikace RDP.

#### <a name="ssh"></a>SSH

1. Přejít na Přidat aplikace, zvolit **SSH**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure25.png)

1. Zadejte **název** a **Popis**aplikace.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure26.png)

1. Nakonfigurujte identitu aplikace.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Zadejte název/popis.

    b. Zadejte IP adresu/plně kvalifikovaný název domény aplikačního serveru a port pro SSH.

    c. Zadejte uživatelské jméno/přístupové heslo SSH * ověřte Akamai EAA.

    d. Zadejte název externího hostitele.

    e. Zadejte umístění konektoru a zvolte konektor.

#### <a name="authentication"></a>Authentication

Klikněte na **Uložit a přejděte na služby**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Služby

Klikněte na **Uložit a přejděte na Upřesnit nastavení**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

Kliknutím na Uložit a přejít na nasazení

![Konfigurace Akamai](./media/header-akamai-tutorial/configure30.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Nasazení

1. Klikněte na **nasadit aplikaci**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure32.png)

1. Činnost koncového uživatele

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Podmíněný přístup

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Ověřování protokolu Kerberos

V následujícím příkladu budeme publikovat interní webový server [http://frp-app1.superdemo.live](http://frp-app1.superdemo.live/) a povolit jednotné přihlašování pomocí KCD.

#### <a name="general-tab"></a>Karta Obecné

![Konfigurace Akamai](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Karta ověřování

Přiřazení zprostředkovatele identity

![Konfigurace Akamai](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Karta služby

![Konfigurace Akamai](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Upřesnit nastavení

![Konfigurace Akamai](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> Hlavní název služby (SPN) webového serveru má SPN@Domain Formát ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` pro tuto ukázku. Nechte zbývající nastavení výchozí.

#### <a name="deployment-tab"></a>Karta nasazení

![Konfigurace Akamai](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Přidávání adresáře

1. V rozevíracím seznamu vyberte **AD** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure33.png)

1. Zadejte potřebná data.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure34.png)

1. Ověřte vytvoření adresáře.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/directorydomain.png)

1. Přidejte skupiny/organizační jednotky, které by vyžadovaly přístup.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/addgroup.png)

1. V níže uvedené skupině se říká EAAGroup a má 1 člen.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/eaagroup.png)

1. Přidejte adresář k poskytovateli identity kliknutím na **Identity**  >  **zprostředkovatele identity** identity, klikněte na kartu **adresáře** a klikněte na **přiřadit adresář**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Postup konfigurace delegování KCD pro EAA

#### <a name="step-1-create-an-account"></a>Krok 1: vytvoření účtu 

1. V tomto příkladu použijeme účet s názvem **EAADelegation**. To můžete provést pomocí Snappin **Uživatelé a počítače služby Active Directory** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > Uživatelské jméno musí být v určitém formátu založeném na **názvu zachycení identity**. Z obrázku 1 vidíte, že se jedná o **corpapps.Login.go.Akamai-Access.com** .

1. Přihlašovací jméno uživatele bude:`HTTP/corpapps.login.go.akamai-access.com`

    ![Konfigurace Akamai](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Krok 2: Konfigurace hlavního názvu služby (SPN) pro tento účet

1. Na základě této ukázky bude hlavní název služby (SPN), jak je uvedeno níže.

2. setspn-s **http/corpapps. Login. Přejít. Akamai-Access. com eaadelegation**

    ![Konfigurace Akamai](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Krok 3: Konfigurace delegování

1. Pro účet EAADelegation klikněte na kartu delegování.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/spn.png)

    * Určete použít libovolný protokol pro ověřování.
    * Klikněte na Přidat a přidejte účet fondu aplikací pro web Kerberos. V případě správné konfigurace by se měla automaticky přeložit na správný název SPN.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Krok 4: vytvoření souboru keytab pro AKAMAI EAA

1. Zde je Obecná syntaxe.

1. Ktpass/out ActiveDirectorydomain. keytab/princ `HTTP/yourloginportalurl@ADDomain.com` /mapuser serviceaccount@ADdomain.com /Pass + RdnPass/Crypto All/pType KRB5_NT_PRINCIPAL

1. Příklad vysvětlení

    | Fragment kódu | Vysvětlení |
    | - | - |
    | Ktpass/out EAADemo. keytab | Název výstupního souboru keytab |
    | /princHTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapusereaadelegation@superdemo.live | Účet delegování EAA |
    | /pass RANDOMPASS | Heslo účtu delegování EAA |
    | /Crypto všechny pType KRB5_NT_PRINCIPAL | Projděte si dokumentaci ke službě Akamai EAA |
    | | |

1. Ktpass/out EAADemo. keytab/princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /Pass RANDOMPASS/Crypto All pType KRB5_NT_PRINCIPAL

    ![Konfigurace Akamai](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Krok 5: import keytab v konzole EAA AKAMAI

1. Klikněte na kartu **systémové**  >  **karty**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/keytabs.png)

1. V keytab typ vyberte **delegování protokolu Kerberos**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/keytabdelegation.png)

1. Ujistěte se, že se keytab zobrazí jako nasazené a ověřené.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/keytabs02.png)

1. Zkušenosti uživatele

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Podmíněný přístup

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Vytvořit testovacího uživatele Akamai

V této části vytvoříte uživatele s názvem B. Simon v Akamai. Pokud chcete přidat uživatele na platformě Akamai, pracujte s [týmem podpory klienta Akamai](https://www.akamai.com/us/en/contact-us/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Akamai, měli byste se automaticky přihlásit k Akamai, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Akamai s Azure AD](https://aad.portal.azure.com/)
