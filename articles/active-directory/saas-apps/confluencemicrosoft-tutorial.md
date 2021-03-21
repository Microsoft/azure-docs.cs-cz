---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí jednotného přihlašování (SSO) Confluence SAML od Microsoftu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Confluence jednotné přihlašování SAML od Microsoftu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736995"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Confluence SAML SSO Microsoftu

V tomto kurzu se dozvíte, jak integrovat Confluence SAML SSO Microsoftu pomocí služby Azure Active Directory (Azure AD). Když integrujete Confluence SAML SSO Microsoftu v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Confluence jednotné přihlašování SAML od Microsoftu
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Confluence SSO SSO Microsoftu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="description"></a>Popis:

K povolení jednotného přihlašování použijte účet Microsoft Azure Active Directory se serverem Atlassian Confluence. Díky tomu můžou všichni uživatelé vaší organizace používat přihlašovací údaje Azure AD k přihlášení do aplikace Confluence. Tento modul plug-in používá pro federaci protokol SAML 2,0.

## <a name="prerequisites"></a>Předpoklady

K nakonfigurování integrace služby Azure AD pomocí jednotného přihlašování Confluence SAML od Microsoftu budete potřebovat následující položky:

- Předplatné Azure AD
- Confluence serverová aplikace nainstalovaná na Windows 64-bitový Server (místně nebo v infrastruktuře Cloud IaaS)
- Server Confluence je povolený pomocí HTTPS.
- Poznámka: podporované verze pro modul plug-in Confluence jsou uvedené v následující části.
- Confluence Server je dosažitelný na internetu, zejména na přihlašovací stránce Azure AD pro ověřování a měl by být schopný získat token z Azure AD.
- Přihlašovací údaje správce se nastavují v Confluence.
- WebSudo je v Confluence zakázané.
- Testovací uživatel vytvořený v serverové aplikaci Confluence

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí Confluence. Nejprve otestujte integraci ve vývojovém nebo přípravném prostředí aplikace a pak použijte provozní prostředí.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

Chcete-li začít, potřebujete následující položky:

* Nepoužívejte své provozní prostředí, pokud není nutné.
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Confluence jednotné přihlašování SAML pomocí předplatného jednotného přihlašování (SSO) Microsoftu.

## <a name="supported-versions-of-confluence"></a>Podporované verze Confluence

Od této chvíle jsou podporovány následující verze Confluence:

- Confluence: 5,0 až 5,10
- Confluence: 6.0.1 na 6.15.9
- Confluence: 7.0.1 na 7.9.3

> [!NOTE]
> Upozorňujeme, že náš modul plug-in Confluence funguje taky na Ubuntu verze 16,04.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Confluence SSO SSO od **Microsoftu podporuje jednotné** přihlašování s PROTOKOLem SAML

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Přidání jednotného přihlašování Confluence SAML od Microsoftu z Galerie

Pokud chcete nakonfigurovat integraci Confluence SSO SSO Microsoftu do Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat Confluence SSO SAML od galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Confluence jednotné přihlašování SAML společnosti Microsoft** .
1. Z panelu výsledků vyberte **Confluence jednotné přihlašování SAML od Microsoftu** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Confluence SSO pomocí protokolu SAML od Microsoftu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Confluence SSO SSO Microsoftu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Confluence SAML SSO Microsoftem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Confluence SSO SSO Microsoftu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurovat jednotné přihlašování **[SAML CONFLUENCE SSO pomocí služby Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte Confluence SSO SSO Microsoft Test User](#create-confluence-saml-sso-by-microsoft-test-user)** , abyste měli protějšek B. Simon v Confluence SSO SSO Microsoftu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro **jednotné přihlašování SAML pomocí služby Microsoft** Application Integration Confluence najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Port je nepovinný pro případ, že se jedná o pojmenovanou adresu URL. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in Confluence, který je vysvětlen dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že Microsoftu udělíte přístup k Confluence SAML SSO.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Confluence SSO SSO od Microsoftu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Konfigurovat jednotné přihlašování SAML Confluence pomocí jednotného přihlašování Microsoftu

1. V jiném okně webového prohlížeče se přihlaste ke své instanci Confluence jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.

    ![Snímek obrazovky, na kterém je vybraná ikona ozubeného kola a v rozevírací nabídce se zvýrazní Doplňky](./media/confluencemicrosoft-tutorial/addon1.png)

1. Stáhněte modul plug-in z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Ručně nahrajte modul plug-in dodaný Microsoftem pomocí nabídky **nahrát doplňky** . Na stažení modulu plug-in se vztahuje [Smlouva o poskytování služeb společnosti Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Snímek obrazovky zobrazující stránku Spravovat doplňky se zvolenou akcí nahrát doplněk](./media/confluencemicrosoft-tutorial/addon12.png)

1. Pokud chcete spustit scénář reverzního proxy serveru Confluence nebo scénář nástroje pro vyrovnávání zatížení, proveďte následující kroky:

    > [!NOTE]
    > Nejdříve byste měli nakonfigurovat server pomocí níže uvedených pokynů a pak nainstalovat modul plug-in.

    a. V **server.xml** souboru aplikace JIRA Server přidejte pod portem **konektoru** atribut.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Snímek obrazovky, který zobrazuje soubor server.xml s atributem přidaným do portu "konektor".](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Změnit **základní adresu URL** v **nastavení systému** podle proxy/nástroje pro vyrovnávání zatížení.

    ![Snímek obrazovky zobrazující stránku Správa nastavení se zvýrazněnou základní adresou URL](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Po nainstalování modulu plug-in se zobrazí v části **nainstalované** doplňky v části **Správa doplňku** . Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky, který zobrazuje oddíl "nainstalovaný uživatel" se zvýrazněným tlačítkem konfigurovat.](./media/confluencemicrosoft-tutorial/addon15.png)

1. Na stránce konfigurace proveďte následující kroky:

    ![Snímek obrazovky zobrazující stránku konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Zajistěte, aby se na aplikaci namapoval jenom jeden certifikát, aby se při překladu metadat neobjevila žádná chyba. Pokud je k dispozici více certifikátů, správce při řešení metadat získá chybu.

    1. Do textového pole **Adresa URL metadat** vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal a klikněte na tlačítko **vyřešit** . Přečte adresu URL metadat IdP a vyplní všechny informace o polích.

    1. Zkopírujte **identifikátor, adresu URL odpovědi a hodnoty adresy URL pro přihlášení** a vložte je do pole **identifikátor, adresa URL odpovědi a text adresy URL pro přihlášení** v části **základní konfigurace SAML** na Azure Portal.

    1. Do pole **název tlačítka pro přihlášení** zadejte název tlačítka, které vaše organizace chce, aby se uživatelé mohli na přihlašovací obrazovce podívat.

    1. V **popisu tlačítka pro přihlášení** zadejte popis tlačítka, které vaše organizace chce, aby se uživatelé mohli podívat na přihlašovací obrazovce.

    1. V části **umístění ID uživatele SAML** vyberte buď **ID uživatele, který je v elementu NameIdentifier příkazu Subject** , nebo **ID uživatele je v elementu atributu**.  Toto ID musí být ID uživatele Confluence. Pokud se ID uživatele neshoduje, systém uživatelům neumožní přihlásit se. 

       > [!Note]
       > Výchozí umístění ID uživatele SAML je identifikátor Name. Můžete ji změnit na možnost atributu a zadat příslušný název atributu.

    1. Pokud vyberete **ID uživatele v možnosti elementu atributu** , pak v textovém poli **název atributu** zadejte název atributu, kde je očekáváno ID uživatele. 

    1. Pokud používáte federované domény (třeba ADFS atd.) se službou Azure AD, klikněte na možnost **Povolit zjišťování domovské sféry** a nakonfigurujte **název domény**.

    1. Do pole **název domény** zadejte název domény v případě přihlášení založeného na ADFS.

    1. Zaškrtněte **možnost Povolit jednotné odhlašování** , pokud se chcete odhlásit ze služby Azure AD, když se uživatel odhlásí z Confluence. 

    1. Pokud se chcete přihlásit jenom prostřednictvím přihlašovacích údajů služby Azure AD, povolte možnost **Vynutit přihlášení do Azure** .

       > [!Note]
       > Pokud chcete povolit výchozí přihlašovací formulář pro přihlášení správce na přihlašovací stránce, když je povolené přihlášení k Azure, přidejte parametr dotazu do adresy URL prohlížeče.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Uložte nastavení kliknutím na tlačítko **Uložit** .

       > [!NOTE]
       > Další informace o instalaci a řešení potíží najdete v [příručce pro správce konektoru pro CONFLUENCE SSO společnosti Microsoft](./ms-confluence-jira-plugin-adminguide.md). K dispozici jsou také [Nejčastější dotazy](./ms-confluence-jira-plugin-adminguide.md) týkající se vaší pomoci.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Vytvořit Confluence jednotné přihlašování SAML pomocí Microsoft Test User

Aby se uživatelé Azure AD mohli přihlašovat k Confluence místním serveru, musí se zřídit v Confluence SSO k SAML SSO Microsoft. Pro jednotné přihlašování Confluence SAML od Microsoftu je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru Confluence jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Přidat zaměstnance](./media/confluencemicrosoft-tutorial/user1.png)

1. V části Uživatelé klikněte na kartu **Přidat uživatele** . Na stránce **Přidat uživatelský** dialog proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje "Confluence Administration" se zvolenou kartou "Přidat uživatele" a zadali jste informace "Přidat uživatele".](./media/confluencemicrosoft-tutorial/user2.png)

    a. Do textového pole **uživatelské jméno** zadejte e-mail uživatele jako B. Simon.

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele jako B. Simon.

    c. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele B.Simon@contoso.com .

    d. Do textového pole **heslo** zadejte heslo pro B. Simon.

    e. Klikněte na **Potvrdit heslo** znovu zadejte heslo.

    f. Klikněte na tlačítko **Přidat** .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Confluence jednotné přihlašování SAML přihlašovací adresou URL Microsoftu, kde můžete spustit tok přihlášení. 

* Přejít na Confluence jednotné přihlašování SAML pomocí přihlašovací adresy URL Microsoftu přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Confluence SSO SSO Microsoft v části Moje aplikace, přesměruje se na Confluence jednotné přihlašování SAML prostřednictvím přihlašovací adresy URL Microsoftu. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete jednotné přihlašování Confluence SAML od Microsoftu, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)