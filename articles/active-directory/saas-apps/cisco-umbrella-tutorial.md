---
title: 'Kurz: Azure Active Directory integraci s jednotným přihlašováním přes Cisco deštník admin | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco zastřešující správce jednotného přihlašování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592502"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Kurz: Azure Active Directory integrací pomocí jednotného přihlašování Cisco zastřešující admin

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování Cisco zastřešující správce pomocí Azure Active Directory (Azure AD). Když integrujete jednotné přihlašování Cisco zastřešující správce s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování Cisco zastřešující admin.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k jednotnému přihlašování přes Cisco zastřešující správce se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Cisco zastřešující správce jednotného přihlašování (SSO) jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování (SSO) Cisco zastřešující správce podporuje **aktualizace SP a IDP** .

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Přidání jednotného přihlašování k Cisco zastřešující správce z Galerie

Pokud chcete nakonfigurovat integraci Cisco zastřešující admin SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat jednotné přihlašování od správce Cisco deštník z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **jednotné přihlašování Cisco zastřešující admin** .
1. Vyberte **Cisco zastřešující správce SSO** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro jednotné přihlašování Cisco zastřešující admin

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí jednotného přihlašování Cisco zastřešující admin pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v rámci jednotného přihlašování (SSO) správce Cisco zastřešující vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Cisco zastřešující admin, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Konfigurace jednotného přihlašování **[SSO správce Cisco zastřešující](#configure-cisco-umbrella-admin-sso-sso)** – ke konfiguraci jednotného přihlašování na straně aplikace
    1. **[Vytvořte uživatele Cisco zastřešující správce jednotného přihlašování (SSO](#create-cisco-umbrella-admin-sso-test-user)** ), abyste měli protějšek B. Simon v jednotném přihlašování k Azure AD, které je propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **jednotného přihlašování pro Cisco zastřešující správce** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    a. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , proveďte následující kroky:

    b. Klikněte na **nastavit další adresy URL**.

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://login.umbrella.com/sso`

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašeho požadavku a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Cisco zastřešující správce jednotného přihlašování** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k JEDNOTNÉmu přihlašování Cisco zastřešující správce.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cisco zastřešující správce SSO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Konfigurace jednotného přihlašování SSO správce Cisco zastřešující

1. V jiném okně prohlížeče se přihlaste k vašemu firemnímu webu Cisco zastřešující admin SSO jako správce.

2. V levé části nabídky klikněte na **správce** , přejděte na **ověřování** a potom klikněte na **SAML**.

    ![Správce](./media/cisco-umbrella-tutorial/admin.png)

3. Vyberte **jiný** a klikněte na **Další**.

    ![Druhá](./media/cisco-umbrella-tutorial/other.png)

4. Na stránce **metadata jednotného přihlašování Cisco zastřešující admin** klikněte na **Další**.

    ![Metadata](./media/cisco-umbrella-tutorial/metadata.png)

5. V případě, že jste předem nakonfigurovali SAML, vyberte na kartě **metadata pro nahrávání** možnost **kliknutím sem je můžete změnit** a postupovat podle následujících kroků.

    ![Další](./media/cisco-umbrella-tutorial/next.png)

6. V **Možnosti a: nahrání souboru XML** nahrajte soubor **XML s federačními metadaty** , který jste stáhli z Azure Portal a po nahrání metadat, že se automaticky naplní následující hodnoty, a pak klikněte na **Další**.

    ![Choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. V části **ověřit konfiguraci SAML** klikněte na **otestovat konfiguraci SAML**.

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. Klikněte na **Uložit**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Vytvořit uživatele Cisco zastřešující admin SSO Test User

Aby se uživatelé Azure AD mohli přihlásit k jednotnému přihlašování přes Cisco zastřešující admin, musí se zřídit do jednotného přihlašování Cisco zastřešující admin.  
V případě jednotného přihlašování pro Cisco deštník admin je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. V jiném okně prohlížeče se přihlaste k vašemu firemnímu webu Cisco zastřešující admin SSO jako správce.

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

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL jednotného přihlašování Cisco zastřešující správce, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlašování pomocí jednotného přihlašování Cisco zastřešující admin přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k jednotnému přihlašování Cisco zastřešující admin, pro které jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SSO správce Cisco zastřešující v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k JEDNOTNÉmu přihlašování Cisco zastřešující admin, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování jednotného přihlašování Cisco zastřešující správce můžete vyhovět řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).