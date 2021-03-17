---
title: 'Kurz: Azure Active Directory integrace s JIRA SAML SSO Microsoft (V 5.2) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a JIRA SSO SSO Microsoft (V 5.2).
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
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736881"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Kurz: Azure Active Directory Integration JIRA SSO Microsoftu (V 5.2)

V tomto kurzu se dozvíte, jak integrovat JIRA SAML SSO Microsoft (V 5.2) pomocí služby Azure Active Directory (Azure AD). Když integrujete JIRA SAML SSO Microsoftu (V 5.2) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k JIRA SSO SSO Microsoftu (V 5.2)
* Umožněte, aby se vaši uživatelé automaticky přihlásili k JIRA SSO SSO Microsoftu (V 5.2) pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="description"></a>Popis

K povolení jednotného přihlašování použijte účet Microsoft Azure Active Directory se serverem Atlassian JIRA. Díky tomu můžou všichni uživatelé vaší organizace používat přihlašovací údaje Azure AD k přihlášení do aplikace JIRA. Tento modul plug-in používá pro federaci protokol SAML 2,0.

## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD pomocí jednotného přihlašování JIRA SAML od Microsoftu (V 5.2) potřebujete následující položky:

- Předplatné Azure AD
- JIRA Core a software 5,2 by měly být nainstalované a nakonfigurované ve Windows 64 bitové verzi
- Server JIRA je povolený pomocí HTTPS.
- Poznámka: podporované verze pro modul plug-in JIRA jsou uvedené v následující části.
- JIRA Server je dosažitelný na internetu, zejména na přihlašovací stránce Azure AD pro ověřování a měl by být schopný získat token z Azure AD.
- Přihlašovací údaje správce se nastavují v JIRA.
- WebSudo je v JIRA zakázané.
- Testovací uživatel vytvořený v serverové aplikaci JIRA

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí JIRA. Nejprve otestujte integraci ve vývojovém nebo přípravném prostředí aplikace a pak použijte provozní prostředí.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat měsíční zkušební verzi: [zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Podporované verze JIRA

* JIRA jádro a software: 5,2
* JIRA také podporuje 6,0 až 7,12. Další podrobnosti získáte, když kliknete na [JIRA SAML SSO Microsoft](jiramicrosoft-tutorial.md) .

> [!NOTE]
> Upozorňujeme, že náš modul plug-in JIRA funguje také na Ubuntu verze 16,04.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* JIRA SAML SSO Microsoft (V 5.2 **) podporuje jednotné** přihlašování (SSO).

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Přidání jednotného přihlašování JIRA SAML od Microsoftu (V 5.2) z Galerie

Pokud chcete nakonfigurovat integraci JIRA SSO SSO Microsoftu (V 5.2) do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat JIRA SSO pro SAML od Microsoftu (V 5.2).

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **JIRA jednotné přihlašování SAML od Microsoftu (v 5.2)** .
1. Z panelu výsledků vyberte **JIRA SSO SSO Microsoft (v 5.2)** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro JIRA SSO pomocí Microsoftu (V 5.2)

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí JIRA SSO pro SAML od Microsoftu (V 5.2) na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v JIRA SAML SSO Microsoftem (V 5.2).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování JIRA SAML od Microsoftu (V 5.2), proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. Nakonfigurujte jednotné přihlašování **[SAML JIRA pomocí služby Microsoft (v 5.2) SSO](#configure-jira-saml-sso-by-microsoft-v52-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte JIRA SSO SSO Microsoft (v 5.2) pro testovacího uživatele](#create-jira-saml-sso-by-microsoft-v52-test-user)** , který má protějšek Britta Simon v JIRA SAML SSO Microsoft (v 5.2), který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

1. V Azure Portal na stránce integrace **JIRA SAML nástrojem Microsoft (v 5.2)** pro integraci aplikací, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Port je nepovinný pro případ, že se jedná o pojmenovanou adresu URL. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in JIRA, který je vysvětlen dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k JIRA SAML SSO Microsoft (V 5.2).

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **JIRA SSO SSO Microsoft (v 5.2)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Konfigurace jednotného přihlašování JIRA SAML Microsoftem (V 5.2) SSO

1. V jiném okně webového prohlížeče se přihlaste ke své instanci JIRA jako správce.

2. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.

    ![Snímek obrazovky zobrazuje doplňky vybrané v nabídce nastavení.](./media/jira52microsoft-tutorial/addon1.png)

3. V části karta doplňky klikněte na **Spravovat doplňky**.

    ![Snímek obrazovky zobrazuje možnosti Spravovat doplňky vybrané na kartě Doplňky.](./media/jira52microsoft-tutorial/addon7.png)

4. Stáhněte modul plug-in z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Ručně nahrajte modul plug-in dodaný Microsoftem pomocí nabídky **nahrát doplňky** . Na stažení modulu plug-in se vztahuje [Smlouva o poskytování služeb společnosti Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Snímek obrazovky ukazuje spravovat doplňky pomocí odkazu nahrát doplněk, který se vyvolal.](./media/jira52microsoft-tutorial/addon12.png)

5. Jakmile je modul plug-in nainstalovaný, zobrazí se v části nainstalované doplňky pro **uživatele** . Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky ukazuje jednotné přihlašování Azure A D SAML pro JIRA s vybranou konfigurací.](./media/jira52microsoft-tutorial/addon13.png)

6. Na stránce konfigurace proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce konfigurace konektoru Microsoft JIRA S S-O.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Zajistěte, aby se na aplikaci namapoval jenom jeden certifikát, aby se při překladu metadat neobjevila žádná chyba. Pokud je k dispozici více certifikátů, při překladu metadat správce získá chybu.

    a. Do textového pole **Adresa URL metadat** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal a klikněte na tlačítko **vyřešit** . Přečte adresu URL metadat IdP a vyplní všechny informace o polích.

    b. Zkopírujte **identifikátor, adresu URL odpovědi a hodnoty adresy URL pro přihlášení** a vložte je do pole **identifikátor, adresa URL odpovědi a text adresy URL pro přihlášení** v části **základní konfigurace SAML** na Azure Portal.

    c. Do pole **název tlačítka pro přihlášení** zadejte název tlačítka, které vaše organizace chce, aby se uživatelé mohli na přihlašovací obrazovce podívat.

    d. V části **umístění ID uživatele SAML** vyberte buď **ID uživatele, který je v elementu NameIdentifier příkazu Subject** , nebo **ID uživatele je v elementu atributu**.  Toto ID musí být ID uživatele JIRA. Pokud se ID uživatele neshoduje, systém uživatelům neumožní přihlásit se.

    > [!Note]
    > Výchozí umístění ID uživatele SAML je identifikátor Name. Můžete ji změnit na možnost atributu a zadat příslušný název atributu.

    e. Pokud vyberete **ID uživatele v možnosti elementu atributu** , pak v textovém poli **název atributu** zadejte název atributu, kde je očekáváno ID uživatele. 

    f. Pokud používáte federované domény (třeba ADFS atd.) se službou Azure AD, klikněte na možnost **Povolit zjišťování domovské sféry** a nakonfigurujte **název domény**.

    například Do pole **název domény** zadejte název domény v případě přihlášení založeného na ADFS.

    h. Zaškrtněte **možnost Povolit jednotné odhlašování** , pokud se chcete odhlásit ze služby Azure AD, když se uživatel odhlásí z JIRA. 

    i. Uložte nastavení kliknutím na tlačítko **Uložit** .

    > [!NOTE]
    > Další informace o instalaci a řešení potíží najdete v [příručce pro správce konektoru MS JIRA SSO](./ms-confluence-jira-plugin-adminguide.md) a také v [části Nejčastější dotazy](./ms-confluence-jira-plugin-adminguide.md) týkající se vaší pomoci.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Vytvořit JIRA SSO SSO Microsoftu (V 5.2) Test User

Aby se uživatelé Azure AD mohli přihlašovat k JIRA místním serveru, musí se zřídit v místním serveru JIRA.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru JIRA jako správce.

2. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Snímek obrazovky se zobrazí Správa uživatelů vybraná v nabídce nastavení.](./media/jira52microsoft-tutorial/user1.png)

3. Budete přesměrováni na stránku pro přístup správce k zadání **hesla** a kliknutím na tlačítko **Potvrdit** .

    ![Snímek obrazovky se zobrazí stránka pro přístup správce, kde můžete zadat svoje přihlašovací údaje.](./media/jira52microsoft-tutorial/user2.png)

4. V části karta **Správa uživatelů** klikněte na možnost **vytvořit uživatele**.

    ![Snímek obrazovky se zobrazí karta Správa uživatelů, kde můžete vytvořit uživatele.](./media/jira52microsoft-tutorial/user3.png) 

5. Na stránce **vytvořit nového uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit nový uživatel, kde můžete zadat informace v tomto kroku.](./media/jira52microsoft-tutorial/user4.png)

    a. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je Britta Simon.

    c. Do textového pole **uživatelské jméno** zadejte e-maily jako uživatel Brittasimon@contoso.com .

    d. Do textového pole **heslo** zadejte heslo uživatele.

    e. Klikněte na **vytvořit uživatele**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL jednotného přihlašování JIRA SAML od Microsoftu (V 5.2), kde můžete spustit tok přihlášení. 

* Přejít na adresu URL jednotného přihlašování JIRA SAML od Microsoftu (V 5.2) a spustit tok přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici JIRA SAML SSO Microsoft (V 5.2) v okně moje aplikace, přesměruje se na JIRA jednotné přihlašování SAML prostřednictvím přihlašovací adresy URL Microsoftu (V 5.2). Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete jednotné přihlašování JIRA SAML Microsoftem (V 5.2), můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).