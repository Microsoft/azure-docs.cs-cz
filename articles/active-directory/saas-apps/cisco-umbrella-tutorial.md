---
title: 'Kurz: Azure Active Directory integrace s Cisco zastřešující | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco zastřešující.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 3b0bb561918d8fb82c6b7bec0a01107ffd9bb5c3
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557315"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Kurz: Azure Active Directory integrace s Cisco deštníkem

V tomto kurzu se dozvíte, jak integrovat Cisco deštník s Azure Active Directory (Azure AD). Když integrujete Cisco deštník s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Cisco zastřešující.
* Umožněte uživatelům, aby se automaticky přihlásili k Cisco deštníku pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Cisco deštník jednotného přihlašování (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cisco deštník podporuje **SP a IDP** iniciované jednotné přihlašování.

## <a name="add-cisco-umbrella-from-the-gallery"></a>Přidat Cisco zastřešující z Galerie

Pokud chcete nakonfigurovat integraci Cisco deštníku do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Cisco zastřešující z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Cisco deštník** .
1. Vyberte **Cisco zastřešující** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Cisco deštník

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Cisco zastřešující pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Cisco zastřešující.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Cisco zastřešující, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Cisco deštník SSO](#configure-cisco-umbrella-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte uživatele Cisco zastřešující test](#create-cisco-umbrella-test-user)** , abyste měli protějšek B. Simon v Cisco zastřešující, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Cisco zastřešující** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    a. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , proveďte následující kroky:

    b. Klikněte na **nastavit další adresy URL**.

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://login.umbrella.com/sso`

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašeho požadavku a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Cisco zastřešující** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Cisco zastřešující.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cisco deštník**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cisco-umbrella-sso"></a>Konfigurace Cisco zastřešující jednotného přihlašování

1. V jiném okně prohlížeče se přihlaste k webu Cisco zastřešující společnosti jako správce.

2. V levé části nabídky klikněte na **správce** , přejděte na **ověřování** a potom klikněte na **SAML**.

    ![Správce](./media/cisco-umbrella-tutorial/admin.png)

3. Vyberte **jiný** a klikněte na **Další**.

    ![Druhá](./media/cisco-umbrella-tutorial/other.png)

4. Na stránce **metadata Cisco zastřešující** klikněte na **Další**.

    ![Metadata](./media/cisco-umbrella-tutorial/metadata.png)

5. V případě, že jste předem nakonfigurovali SAML, vyberte na kartě **metadata pro nahrávání** možnost **kliknutím sem je můžete změnit** a postupovat podle následujících kroků.

    ![Další](./media/cisco-umbrella-tutorial/next.png)

6. V **Možnosti a: nahrání souboru XML** nahrajte soubor **XML s federačními metadaty** , který jste stáhli z Azure Portal a po nahrání metadat, že se automaticky naplní následující hodnoty, a pak klikněte na **Další**.

    ![Choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. V části **ověřit konfiguraci SAML** klikněte na **otestovat konfiguraci SAML**.

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. Klikněte na **Uložit**.

### <a name="create-cisco-umbrella-test-user"></a>Vytvořit uživatele Cisco zastřešující test

Aby se uživatelé Azure AD mohli přihlásit k Cisco deštníku, musí se zřídit v Cisco zastřešující.  
V případě Cisco zastřešující je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. V jiném okně prohlížeče se přihlaste k webu Cisco zastřešující společnosti jako správce.

2. V levé části nabídky klikněte na **správce** a přejděte na **účty**.

    ![Účet](./media/cisco-umbrella-tutorial/account.png)

3. Na stránce **účty** klikněte na **Přidat** v pravém horním rohu stránky a proveďte následující kroky.

    ![Uživatel](./media/cisco-umbrella-tutorial/create-user.png)

    a. Do pole **jméno** zadejte jméno, jako je **Britta**.

    b. Do pole **příjmení** zadejte příjmení, jako je **Simon**.

    c. Z **role zvolte delegovaného správce** vyberte svou roli.

    d. Do pole **e-mailová adresa** zadejte EmailAddress uživatele, jako je **brittasimon \@ contoso.com**.

    e. Do pole **heslo** zadejte své heslo.

    f. V poli **Potvrdit heslo** zadejte znovu heslo.

    například Klikněte na **vytvořit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení Cisco deštník, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení Cisco zastřešující přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Cisco zastřešující, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Cisco zastřešující v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Cisco zastřešující, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Cisco zastřešující můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
