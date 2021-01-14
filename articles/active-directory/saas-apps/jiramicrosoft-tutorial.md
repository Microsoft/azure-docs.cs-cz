---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí jednotného přihlašování (SSO) JIRA SAML od Microsoftu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a JIRA jednotné přihlašování SAML od Microsoftu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 4793cad25e34e2e318dd205ba5f474d4529d13d5
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183876"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s JIRA SAML SSO Microsoftu

V tomto kurzu se dozvíte, jak integrovat JIRA SAML SSO Microsoftu pomocí služby Azure Active Directory (Azure AD). Když integrujete JIRA SAML SSO Microsoftu v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k JIRA jednotné přihlašování SAML od Microsoftu
* Umožněte, aby se vaši uživatelé automaticky přihlásili k JIRA SSO SSO Microsoftu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="description"></a>Description

K povolení jednotného přihlašování použijte účet Microsoft Azure Active Directory se serverem Atlassian JIRA. Díky tomu můžou všichni uživatelé vaší organizace používat přihlašovací údaje Azure AD k přihlášení do aplikace JIRA. Tento modul plug-in používá pro federaci protokol SAML 2,0.

## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD pomocí jednotného přihlašování JIRA SAML od Microsoftu budete potřebovat následující položky:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
- JIRA Core a software 6,4 8.14.0 nebo JIRA Service Desk 3,0 na 4.11.1 by se měly nainstalovat a nakonfigurovat v systému Windows 64-bit verze
- Server JIRA je povolený pomocí HTTPS.
- Poznámka: podporované verze pro modul plug-in JIRA jsou uvedené v následující části.
- JIRA Server je dostupný na internetu, zejména na přihlašovací stránce Azure AD pro ověřování a měl by být schopný získat token z Azure AD.
- Přihlašovací údaje správce se nastavují v JIRA.
- WebSudo je v JIRA zakázané.
- Testovací uživatel vytvořený v serverové aplikaci JIRA

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí JIRA. Nejprve otestujte integraci ve vývojovém nebo přípravném prostředí aplikace a pak použijte provozní prostředí.

Chcete-li začít, potřebujete následující položky:

* Nepoužívejte své provozní prostředí, pokud není nutné.
* JIRA jednotné přihlašování SAML pomocí předplatného jednotného přihlašování (SSO) Microsoftu.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="supported-versions-of-jira"></a>Podporované verze JIRA

* JIRA Core a software: 6,4 až 8.14.0
* JIRA oddělení služeb 3.0.0 na 4.11.1
* JIRA také podporuje 5,2. Další podrobnosti získáte, když kliknete na [Microsoft Azure Active Directory jednotné přihlašování pro JIRA 5,2](jira52microsoft-tutorial.md) .

> [!NOTE]
> Upozorňujeme, že náš modul plug-in JIRA funguje také na Ubuntu verze 16,04 a Linux.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* JIRA SSO SSO od **Microsoftu podporuje jednotné** přihlašování s PROTOKOLem SAML

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Přidání jednotného přihlašování JIRA SAML od Microsoftu z Galerie

Pokud chcete nakonfigurovat integraci JIRA SSO SSO Microsoftu do Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat JIRA SSO SAML od galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **JIRA jednotné přihlašování SAML společnosti Microsoft** .
1. Z panelu výsledků vyberte **JIRA jednotné přihlašování SAML od Microsoftu** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro JIRA SSO pomocí protokolu SAML od Microsoftu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s JIRA SSO SSO Microsoftu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v JIRA SAML SSO Microsoftem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s JIRA SSO SSO Microsoftu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurovat jednotné přihlašování **[SAML JIRA SSO pomocí služby Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte JIRA SSO SSO Microsoft Test User](#create-jira-saml-sso-by-microsoft-test-user)** , abyste měli protějšek B. Simon v JIRA SSO SSO Microsoftu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro **jednotné přihlašování SAML pomocí služby Microsoft** Application Integration JIRA najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Port je nepovinný pro případ, že se jedná o pojmenovanou adresu URL. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in JIRA, který je vysvětlen dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že Microsoftu udělíte přístup k JIRA SAML SSO.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **JIRA SSO SSO od Microsoftu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Konfigurovat jednotné přihlašování SAML JIRA pomocí jednotného přihlašování Microsoftu

1. V jiném okně webového prohlížeče se přihlaste ke své instanci JIRA jako správce.

2. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.

    ![Snímek obrazovky zobrazuje doplňky vybrané v nabídce nastavení.](./media/jiramicrosoft-tutorial/addon1.png)

3. Stáhněte modul plug-in z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Ručně nahrajte modul plug-in dodaný Microsoftem pomocí nabídky **nahrát doplňky** . Na stažení modulu plug-in se vztahuje [Smlouva o poskytování služeb společnosti Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Snímek obrazovky ukazuje spravovat doplňky pomocí odkazu nahrát doplněk, který se vyvolal.](./media/jiramicrosoft-tutorial/addon12.png)

4. Pokud chcete spustit scénář reverzního proxy serveru JIRA nebo scénář nástroje pro vyrovnávání zatížení, proveďte následující kroky:

    > [!NOTE]
    > Nejdříve byste měli nakonfigurovat server pomocí níže uvedených pokynů a pak nainstalovat modul plug-in.

    a. V **server.xml** souboru aplikace JIRA Server přidejte pod portem **konektoru** atribut.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Snímek obrazovky ukazuje soubor. x m l v editoru s přidaným novým řádkem.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Změnit **základní adresu URL** v **nastavení systému** podle proxy/nástroje pro vyrovnávání zatížení.

    ![Snímek obrazovky ukazuje nastavení správy, kde můžete změnit základní U R L.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Po nainstalování modulu plug-in se zobrazí v části **nainstalované** doplňky v části **Správa doplňku** . Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky ukazuje jednotné přihlašování Azure A D SAML pro JIRA s vybranou konfigurací.](./media/jiramicrosoft-tutorial/addon14.png)

6. Na stránce konfigurace proveďte následující kroky:

    ![Snímek obrazovky se zobrazí Microsoft Azure Active Directory jednotné přihlašování pro konfigurační stránku JIRA.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Zajistěte, aby se na aplikaci namapoval jenom jeden certifikát, aby se při překladu metadat neobjevila žádná chyba. Pokud je k dispozici více certifikátů, při překladu metadat správce získá chybu.

    a. Do textového pole **Adresa URL metadat** vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal a klikněte na tlačítko **vyřešit** . Přečte adresu URL metadat IdP a vyplní všechny informace o polích.

    b. Zkopírujte **identifikátor, adresu URL odpovědi a hodnoty URL pro podepsání** a vložte je do pole **identifikátor, adresa URL odpovědi a text adresy URL pro přihlášení** v **JIRA SAML pomocí služby Microsoft domény a adresy** URL v oblasti Azure Portal.

    c. Do pole **název tlačítka pro přihlášení** zadejte název tlačítka, které vaše organizace chce, aby se uživatelé mohli na přihlašovací obrazovce podívat.
    
    d. V **popisu tlačítka pro přihlášení** zadejte popis tlačítka, které vaše organizace chce, aby se uživatelé mohli podívat na přihlašovací obrazovce.

    e. V části **umístění ID uživatele SAML** vyberte buď **ID uživatele, který je v elementu NameIdentifier příkazu Subject** , nebo **ID uživatele je v elementu atributu**.  Toto ID musí být ID uživatele JIRA. Pokud se ID uživatele neshoduje, systém uživatelům neumožní přihlásit se.

    > [!Note]
    > Výchozí umístění ID uživatele SAML je identifikátor Name. Můžete ji změnit na možnost atributu a zadat příslušný název atributu.

    f. Pokud vyberete **ID uživatele v možnosti elementu atributu** , pak v textovém poli **název atributu** zadejte název atributu, kde je očekáváno ID uživatele.

    například Pokud používáte federované domény (třeba ADFS atd.) se službou Azure AD, klikněte na možnost **Povolit zjišťování domovské sféry** a nakonfigurujte **název domény**.

    h. Do pole **název domény** zadejte název domény v případě přihlášení založeného na ADFS.

    i. Zaškrtněte **možnost Povolit jednotné odhlašování** , pokud se chcete odhlásit z Azure AD při odhlášení uživatele ze služby JIRA.
    
    j. Pokud se chcete přihlásit jenom prostřednictvím přihlašovacích údajů služby Azure AD, povolte možnost **Vynutit přihlášení do Azure** .
    
    > [!Note]
    > Pokud chcete povolit výchozí přihlašovací formulář pro přihlášení správce na přihlašovací stránce, když je povolené přihlášení k Azure, přidejte parametr dotazu do adresy URL prohlížeče.
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. Uložte nastavení kliknutím na tlačítko **Uložit** .

    > [!NOTE]
    > Další informace o instalaci a řešení potíží najdete v [příručce pro správce konektoru pro JIRA SSO společnosti Microsoft](./ms-confluence-jira-plugin-adminguide.md). K dispozici jsou také [Nejčastější dotazy](./ms-confluence-jira-plugin-adminguide.md) týkající se vaší pomoci.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Vytvořit JIRA jednotné přihlašování SAML pomocí Microsoft Test User

Aby se uživatelé Azure AD mohli přihlašovat k JIRA místním serveru, musí se zřídit v JIRA SSO k SAML SSO Microsoft. Pro jednotné přihlašování JIRA SAML od Microsoftu je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru JIRA jako správce.

2. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Snímek obrazovky se zobrazí Správa uživatelů vybraná v nabídce nastavení.](./media/jiramicrosoft-tutorial/user1.png)

3. Budete přesměrováni na stránku pro přístup správce k zadání **hesla** a kliknutím na tlačítko **Potvrdit** .

    ![Snímek obrazovky se zobrazí stránka pro přístup správce, kde můžete zadat svoje přihlašovací údaje.](./media/jiramicrosoft-tutorial/user2.png)

4. V části karta **Správa uživatelů** klikněte na možnost **vytvořit uživatele**.

    ![Snímek obrazovky se zobrazí karta Správa uživatelů, kde můžete vytvořit uživatele.](./media/jiramicrosoft-tutorial/user3.png) 

5. Na stránce **vytvořit nového uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit nový uživatel, kde můžete zadat informace v tomto kroku.](./media/jiramicrosoft-tutorial/user4.png) 

    a. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele B.simon@contoso.com .

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je B. Simon.

    c. Do textového pole **uživatelské jméno** zadejte e-maily jako uživatel B.simon@contoso.com .

    d. Do textového pole **heslo** zadejte heslo uživatele.

    e. Klikněte na **vytvořit uživatele**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na JIRA jednotné přihlašování SAML přihlašovací adresou URL Microsoftu, kde můžete spustit tok přihlášení. 

* Přejít na JIRA jednotné přihlašování SAML pomocí přihlašovací adresy URL Microsoftu přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici JIRA SSO SSO Microsoft v části Moje aplikace, přesměruje se na JIRA jednotné přihlašování SAML prostřednictvím přihlašovací adresy URL Microsoftu. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete jednotné přihlašování JIRA SAML od Microsoftu, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
