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
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 331d9df6059c8af54857d05d2d6373e48e127eac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850842"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Kurz: Azure Active Directory Integration JIRA SSO Microsoftu (V 5.2)

V tomto kurzu se naučíte integrovat JIRA SAML SSO Microsoft (V 5.2) pomocí služby Azure Active Directory (Azure AD).
Integrace služby JIRA SAML SSO Microsoftu (V 5.2) pomocí Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k JIRA SSO SSO Microsoft (V 5.2).
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k JIRA SSO SSO Microsoftu (V 5.2) (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="description"></a>Popis

K povolení jednotného přihlašování použijte účet Microsoft Azure Active Directory se serverem Atlassian JIRA. Díky tomu můžou všichni uživatelé vaší organizace používat přihlašovací údaje Azure AD k přihlášení do aplikace JIRA. Tento modul plug-in používá pro federaci protokol SAML 2,0.

## <a name="prerequisites"></a>Předpoklady

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
> Upozorňujeme, že náš modul plug-in JIRA funguje taky na Ubuntu verze 16,04.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* JIRA SAML SSO Microsoft (V 5.2 **) podporuje jednotné** přihlašování (SSO).

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Přidání jednotného přihlašování JIRA SAML od Microsoftu (V 5.2) z Galerie

Pokud chcete nakonfigurovat integraci JIRA SSO SSO Microsoftu (V 5.2) do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat JIRA SSO pro SAML od Microsoftu (V 5.2).

**Pokud chcete přidat JIRA SAML SSO Microsoftu (V 5.2) z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **JIRA SAML SSO Microsoft (v 5.2)**, vyberte **JIRA SSO SSO Microsoft (v 5.2)** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![JIRA jednotné přihlašování SAML od Microsoftu (V 5.2) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí JIRA SSO pro SAML od Microsoftu (V 5.2) na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo možné jednotné přihlašování pracovat, je nutné zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v JIRA SAML SSO společnosti Microsoft (V 5.2).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí JIRA SSO Microsoftu (V 5.2), musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování JIRA SAML pomocí Microsoft (v 5.2) jednotného přihlašování](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte JIRA SSO SSO Microsoft (v 5.2) pro testovacího uživatele](#create-jira-saml-sso-by-microsoft-v52-test-user)** , který má protějšek Britta Simon v JIRA SAML SSO Microsoft (v 5.2), který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování JIRA SAML od Microsoftu (V 5.2), proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **JIRA SAML pomocí služby Microsoft (v 5.2)** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![JIRA jednotné přihlašování SAML od Microsoftu (V 5.2) informace o jednotném přihlašování v doméně a adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Port je nepovinný pro případ, že se jedná o pojmenovanou adresu URL. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in JIRA, který je vysvětlen dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Konfigurace JIRA SAML SSO Microsoft (V 5.2) Single Sign-On

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
    > Další informace o instalaci a řešení potíží najdete na stránce [Průvodce pro správce konektoru MS JIRA SSO](../ms-confluence-jira-plugin-adminguide.md) a také v [části Nejčastější dotazy](../ms-confluence-jira-plugin-faq.md) týkající se vaší pomoci.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon\@yourcompanydomain.extension` . Například, BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k JIRA SAML SSO Microsoftu (V 5.2).

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **JIRA SSO SSO Microsoft (v 5.2)**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **JIRA SSO SSO Microsoft (v 5.2)**.

    ![Odkaz na JIRA SAML SSO Microsoft (V 5.2) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

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

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici JIRA SAML SSO Microsoft (V 5.2) na přístupovém panelu, měli byste se automaticky přihlásit k JIRA SAML SSO Microsoft (V 5.2), pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
