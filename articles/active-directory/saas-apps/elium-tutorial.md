---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Elium | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Elium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7f0e9d0c97b9325a30de3cb8c6ce10a3ba8489f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Elium

V tomto kurzu se dozvíte, jak integrovat Elium s Azure Active Directory (Azure AD). Když integrujete Elium s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Elium.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Elium svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Elium odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Elium podporuje jednotné přihlašování (SSO) **a IDP** .
* Elium podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-elium-from-the-gallery"></a>Přidání Elium z Galerie

Pokud chcete nakonfigurovat integraci Elium do služby Azure AD, musíte přidat Elium z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Elium** .
1. Na panelu výsledků vyberte **Elium** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Elium

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Elium pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Elium.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Elium, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ELIUM SSO](#configure-elium-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Elium Test User](#create-elium-test-user)** -to, abyste měli protějšek B. Simon v Elium, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Elium** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty se zobrazí ze **souboru metadat SP** ke stažení v `https://<platform-domain>.elium.com/login/saml2/metadata` , což je vysvětleno dále v tomto kurzu.

1. Elium aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Elium několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ---------------| ----------------|
    | e-mail   |uživatel. pošta |
    | first_name| User. křestní jméno |
    | last_name| User. příjmení|
    | job_title| User. jobtitle|
    | company| User. CompanyName|

    > [!NOTE]
    > Toto jsou výchozí deklarace identity. **Vyžaduje se jenom deklarace identity e-mailu**. Pro zřizování JIT je také povinné jenom deklarace identity e-mailu. Další vlastní deklarace identity se můžou lišit od zákaznické platformy k jiné zákaznické platformě.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Elium** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Elium.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Elium**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-elium-sso"></a>Konfigurace jednotného přihlašování Elium

1. Pokud chcete automatizovat konfiguraci v rámci Elium, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit Elium** , které vás přesměruje do aplikace Elium. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Elium. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Elium ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Elium společnosti jako správce a proveďte následující kroky:

1. Klikněte na **Profil uživatele** z pravého horního rohu a pak vyberte **Nastavení**.

    ![Konfigurace jednoho Sign-On Elium 01](./media/elium-tutorial/elium-01.png)

1. V části **Upřesnit** vyberte **zabezpečení** .

    ![Konfigurace jednoho Sign-On Elium 02](./media/elium-tutorial/elium-02.png)

1. Posuňte se dolů k části **jednotné přihlašování (SSO)** a proveďte následující kroky:

    ![Konfigurace jednoho Sign-On Elium 03](./media/elium-tutorial/elium-03.png)

    a. Zkopírujte hodnotu **ověřit, jestli typu Saml2 ověřování funguje pro váš účet** , a vložte ho do textového pole **přihlašovací adresa URL** v základní části **Konfigurace SAML** v Azure Portal.

    > [!NOTE]
    > Po konfiguraci jednotného přihlašování (SSO) máte vždycky přístup k výchozí stránce vzdáleného přihlášení na následující adrese URL: `https://<platform_domain>/login/regular/login` 

    b. Zaškrtněte políčko **Povolit federaci typu Saml2** .

    c. Vyberte zaškrtávací políčko pro **zřizování JIT** .

    d. Kliknutím na tlačítko **Stáhnout** otevřete **metadata SP** .

    e. Vyhledejte **entityID** v souboru **metadat SP** , zkopírujte hodnotu **entityID** a vložte ji do textového pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal. 

    ![Konfigurace jednoho Sign-On Elium 04](./media/elium-tutorial/elium-04.png)

    f. Vyhledejte **AssertionConsumerService** v souboru **metadat SP** , zkopírujte hodnotu **umístění** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    ![Konfigurace jednoho Sign-On Elium 05](./media/elium-tutorial/elium-05.png)

    například Otevřete stažený soubor metadat z Azure Portal do programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **metadat IDP** .

    h. Klikněte na **Uložit**.

### <a name="create-elium-test-user"></a>Vytvořit testovacího uživatele Elium

V této části se v Elium vytvoří uživatel s názvem B. Simon. Elium podporuje **zřizování za běhu**, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v Elium neexistuje, vytvoří se nový, když se pokusíte o přístup k Elium.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 
 
#### <a name="sp-initiated"></a>Zahájena SP:
 
* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Elium, kde můžete spustit tok přihlášení.  
 
* Přejít na adresu URL pro přihlášení k Elium přímo a zahájit tok přihlášení.
 
#### <a name="idp-initiated"></a>Iniciované IDP:
 
* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Elium, pro které jste nastavili jednotné přihlašování. 
 
K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici Elium na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Elium, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Elium můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).