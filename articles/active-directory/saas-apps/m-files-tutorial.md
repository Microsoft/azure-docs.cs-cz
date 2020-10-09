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
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 8cf4df682a5c141e39a3860547a5b6130e2a3002
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856910"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Kurz: Azure Active Directory integrace s M-Files

V tomto kurzu se naučíte integrovat soubory M-Files pomocí Azure Active Directory (Azure AD).
Integrace M-Files s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k souborům M.
* Uživatelům můžete povolit, aby se automaticky přihlásili do souborů M-Files (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci Integrace Azure AD s M-Files budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* M-soubory s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* M-soubory podporují jednotné přihlašování **SP** iniciované

## <a name="adding-m-files-from-the-gallery"></a>Přidání M-Files z Galerie

Pokud chcete nakonfigurovat integraci souborů M-Files do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat M-Files z galerie.

**Chcete-li do galerie přidat soubory M, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **m-Files**, vyberte **m-Files** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![M-Files v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí souborů M-Files na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v M-Files.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí souborů M-Files, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace M-Files jednotného přihlašování](#configure-m-files-single-sign-on)** – pro konfiguraci nastavení jednoho Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření testovacího uživatele M-Files](#create-m-files-test-user)** – Pokud chcete mít protějšek Britta Simon v M-Files, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí souborů M-Files, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací **M-Files** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![M – soubory s jednotným přihlašováním pro doménu a adresy URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Kontaktujte [M-soubory tým podpory klienta](mailto:support@m-files.com) , aby získali tyto hodnoty. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit soubory M** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-m-files-single-sign-on"></a>Konfigurovat M-soubory Single Sign-On

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování (SSO), kontaktujte [tým podpory souborů M-Files](mailto:support@m-files.com) a poskytněte jim stažená metadata.
   
    >[!NOTE]
    >Pokud chcete nakonfigurovat jednotné přihlašování pro desktopovou aplikaci M-File, postupujte podle následujících kroků. Pokud chcete nakonfigurovat jednotné přihlašování jenom pro web M-Files, nejsou potřeba žádné další kroky.  

1. Postupujte podle dalších kroků a nakonfigurujte desktopovou aplikaci M-File pro povolení jednotného přihlašování s Azure AD. Chcete-li stáhnout soubory M-Files, přejít na stránku pro [Stažení souborů m-Files](https://www.m-files.com/en/download-latest-version) .

1. Otevřete okno **nastavení počítače M-Files** . Pak klikněte na tlačítko **Přidat**.
   
    ![Snímek obrazovky zobrazuje M-Files nastavení plochy, kde můžete vybrat přidat.](./media/m-files-tutorial/tutorial_m_files_10.png)

1. V okně **Vlastnosti připojení trezoru dokumentů** proveďte následující kroky:
   
    ![Snímek obrazovky zobrazuje vlastnosti připojení trezoru dokumentů, kde můžete zadat hodnoty, které jsou popsány.](./media/m-files-tutorial/tutorial_m_files_11.png)  

    V části typ oddílu Server hodnoty zadejte následující hodnoty:  

    a. Jako **název**zadejte `<tenant-name>.cloudvault.m-files.com` . 
 
    b. Jako **číslo portu**zadejte **4466**. 

    c. V případě **protokolu**vyberte **https**. 

    d. V poli **ověřování** vyberte **konkrétní uživatel systému Windows**. Pak se zobrazí výzva s podpisovou stránkou. Vložte přihlašovací údaje služby Azure AD. 

    e. Pro **trezor na serveru**vyberte odpovídající trezor na serveru.
 
    f. Klikněte na **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k souborům M.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **M-Files**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **M-Files**.

    ![Odkaz M-Files v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-m-files-test-user"></a>Vytvořit testovacího uživatele M-Files

Cílem této části je vytvořit uživatele s názvem Britta Simon v M-Files. Pokud chcete přidat uživatele do souborů M-Files, pracujte s  [týmem podpory souborů m-Files](mailto:support@m-files.com) .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici soubory M-Files na přístupovém panelu, měli byste se automaticky přihlásit k souborům M, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

