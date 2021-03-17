---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Adobe Creative Cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 553f77cdb42aa0adb230ee3efd7bec7e9fbfa972
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653367"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Adobe Creative Cloud

V tomto kurzu se naučíte, jak integrovat Adobe Creative Cloud s Azure Active Directory (Azure AD). Když integrujete Adobe Creative Cloud s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Adobe Creative Cloud.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Adobe Creative Cloud se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Adobe Creative Cloud odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Creative Cloud podporuje jednotné přihlašování spouštěné přes **SP**

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Přidání Adobe Creative Cloud z Galerie

Pokud chcete nakonfigurovat integraci Adobe Creative Cloud do služby Azure AD, musíte přidat Adobe Creative Cloud z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Adobe Creative Cloud** .
1. Na panelu výsledků vyberte **Adobe Creative Cloud** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Adobe Creative Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Adobe Creative Cloud pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Adobe Creative Cloud.

K nakonfigurování a testování jednotného přihlašování služby Azure AD pomocí Adobe Creative Cloud proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Adobe Creative Cloud SSO](#configure-adobe-creative-cloud-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Adobe Creative Cloud testovacího uživatele](#create-adobe-creative-cloud-test-user)** – můžete mít protějšek B. Simon v Adobe Creative Cloud, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Adobe Creative Cloud** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://adobe.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Postupujte podle pokynů v části Krok 4 **Konfigurace rozhraní Adobe cloud SSO** . V takovém případě můžete otevřít **soubor XML s federačními metadaty** a získat z něj hodnotu ID entity a umístit ji jako hodnotu identifikátoru v konfiguraci služby Azure AD. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Adobe Creative Cloud aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho Adobe Creative Cloud aplikace očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    |----- | --------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | E-mail | uživatel. pošta |

    > [!NOTE]
    > Uživatelé musí mít platnou licenci Microsoft 365 ExO pro hodnotu deklarace e-mailu, která se má vyplnit v odpovědi SAML.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML data federace** a pak vyberte **Stáhnout** a Stáhněte si soubor metadat XML a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Adobe Creative Cloud** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon použití jednotného přihlašování Azure tím, že udělíte přístup k Adobe Creative Cloud.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Adobe Creative Cloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-adobe-creative-cloud-sso"></a>Konfigurace Adobe Creative Cloud jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste ke [konzole správce Adobe](https://adminconsole.adobe.com) jako správce systému.

1. V horním navigačním panelu vyberte **Nastavení** a pak zvolte **Identita**. Otevře se seznam adresářů. Vyberte federovaný adresář, který chcete.

1. Na stránce s **podrobnostmi adresáře** vyberte **Konfigurovat**.

1. Zkopírujte ID entity a adresu URL služby ACS (adresa URL nebo adresa URL příjemce kontrolního výrazu). Zadejte adresy URL do příslušných polí v Azure Portal.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Použijte hodnotu ID entity od společnosti Adobe, kterou jste zadali pro **identifikátor** v dialogovém okně **Konfigurovat nastavení aplikace** .

    b. Pomocí adresy URL služby ACS (adresa URL služby assertion Consumer Service) od společnosti Adobe jste zadali **adresu URL odpovědi** v dialogovém okně **Konfigurovat nastavení aplikace** .

1. V dolní části stránky nahrajte soubor **XML federačních dat** , který jste stáhli z Azure Portal. 

    ![Soubor XML federačních dat](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML metadata IdP")

1. Vyberte **Uložit**.

### <a name="create-adobe-creative-cloud-test-user"></a>Vytvořit Adobe Creative Cloud testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat Adobe Creative Cloud, musí se zřídit do Adobe Creative Cloud. V případě Adobe Creative Cloud je zřizování ručním úkolem.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Při zřizování uživatelských účtů proveďte následující kroky:

1. Přihlaste se k webu [konzoly pro správu Adobe](https://adminconsole.adobe.com) jako správce.

2. Přidejte uživatele do konzoly společnosti Adobe jako federované ID a přiřaďte je k profilu produktu. Podrobné informace o přidávání uživatelů najdete v tématu [Přidání uživatelů v konzole pro správu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. V tomto okamžiku zadejte svou e-mailovou adresu nebo hlavní název uživatele (UPN) do formuláře přihlašování Adobe, stiskněte klávesu TAB a měli byste být federované zpátky do služby Azure AD:
   * Webový přístup: Webová \. adobe.com > přihlášení
   * V rámci nástroje Desktop App > přihlášení
   * V rámci aplikace > Help > přihlášení

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Adobe Creative Cloud adresu URL pro přihlášení, kde můžete spustit tok přihlášení. 

* Přejít na Adobe Creative Cloud adresa URL pro přihlášení přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Adobe Creative Cloud v okně moje aplikace, přesměruje se na Adobe Creative Cloud přihlašovací adresa URL. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Adobe Creative Cloud můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)