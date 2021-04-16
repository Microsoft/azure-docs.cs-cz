---
title: 'Kurz: Azure Active Directory integrace se soubory M-Files | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a M-soubory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482808"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Kurz: Azure Active Directory integrace s M-Files

V tomto kurzu se naučíte integrovat soubory M-Files pomocí Azure Active Directory (Azure AD). Když integrujete M-Files s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k souborům M-Files.
* Umožněte uživatelům, aby se automaticky přihlásili k souborům M-se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* M-soubory odběrů s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* M-Files podporuje jednotné přihlašování (SSO) spouštěné v **SP** .

## <a name="add-m-files-from-the-gallery"></a>Přidání M-Files z Galerie

Pokud chcete nakonfigurovat integraci souborů M-Files do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat M-Files z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte **M-Files** do vyhledávacího pole.
1. Vyberte **M-Files** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro soubory M

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s M-soubory pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v M-Files.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí souborů M-Files, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurovat jednotné přihlašování souborů M](#configure-m-files-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořit testovacího uživatele M-Files](#create-m-files-test-user)** – Chcete-li mít protějšek B. Simon v M-Files, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikací **M-Files** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Kontaktujte [M-soubory tým podpory klienta](mailto:support@m-files.com) , aby získali tyto hodnoty. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit soubory M** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k souborům M.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **M-Files**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-m-files-sso"></a>Konfigurace souborů pro jednotné přihlašování

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování (SSO), kontaktujte [tým podpory souborů M-Files](mailto:support@m-files.com) a poskytněte jim stažená metadata.
   
    >[!NOTE]
    >Pokud chcete nakonfigurovat jednotné přihlašování pro desktopovou aplikaci M-File, postupujte podle následujících kroků. Pokud chcete nakonfigurovat jednotné přihlašování jenom pro web M-Files, nejsou potřeba žádné další kroky.  

1. Postupujte podle dalších kroků a nakonfigurujte desktopovou aplikaci M-File pro povolení jednotného přihlašování s Azure AD. Chcete-li stáhnout soubory M-Files, přejít na stránku pro [Stažení souborů m-Files](https://www.m-files.com/customers/product-downloads/download-update-links/) .

1. Otevřete okno **nastavení počítače M-Files** . Pak klikněte na tlačítko **Přidat**.
   
    ![Snímek obrazovky zobrazuje M-Files nastavení plochy, kde můžete vybrat přidat.](./media/m-files-tutorial/settings.png)

1. V okně **Vlastnosti připojení trezoru dokumentů** proveďte následující kroky:
   
    ![Snímek obrazovky zobrazuje vlastnosti připojení trezoru dokumentů, kde můžete zadat hodnoty, které jsou popsány.](./media/m-files-tutorial/general.png)  

    V části typ oddílu Server hodnoty zadejte následující hodnoty:  

    a. Jako **název** zadejte `<tenant-name>.cloudvault.m-files.com` . 
 
    b. Jako **číslo portu** zadejte **4466**. 

    c. V případě **protokolu** vyberte **https**. 

    d. V poli **ověřování** vyberte **konkrétní uživatel systému Windows**. Pak se zobrazí výzva s podpisovou stránkou. Vložte přihlašovací údaje služby Azure AD. 

    e. Pro **trezor na serveru** vyberte odpovídající trezor na serveru.
 
    f. Klikněte na **OK**.

### <a name="create-m-files-test-user"></a>Vytvořit testovacího uživatele M-Files

Cílem této části je vytvořit uživatele s názvem Britta Simon v M-Files. Pokud chcete přidat uživatele do souborů M-Files, pracujte s  [týmem podpory souborů m-Files](mailto:support@m-files.com) .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k souborům M, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení s M-Files přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici soubory M-Files v části Moje aplikace, přesměruje se na adresu URL pro přihlášení na M-Files. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po konfiguraci M-Files můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
