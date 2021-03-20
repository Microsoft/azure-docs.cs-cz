---
title: 'Kurz: Azure Active Directory integrace s Saba TalentSpace | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: edb94e8e1b4e814bfac4a1a2a90c5ec71bc48c77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653002"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Saba TalentSpace

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) integrovat Saba TalentSpace. Když integrujete Saba TalentSpace s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Saba TalentSpace.
* Umožněte uživatelům, aby se automaticky přihlásili k Saba TalentSpace pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Saba TalentSpace odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Saba TalentSpace podporuje jednotné přihlašování spouštěné v **SP**

## <a name="add-saba-talentspace-from-the-gallery"></a>Přidat z Galerie TalentSpace Saba

Pokud chcete nakonfigurovat integraci aplikace Saba TalentSpace do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Saba TalentSpace z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole příkaz **Saba TalentSpace** .
1. Vyberte možnost **Saba TalentSpace** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Saba TalentSpace

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Saba TalentSpace pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Saba TalentSpace.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Saba TalentSpace, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace Saba TALENTSPACE SSO](#configure-saba-talentspace-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte Saba TalentSpace Test User](#create-saba-talentspace-test-user)** -to, abyste měli protějšek B. Simon v Saba TalentSpace, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikací **Saba TalentSpace** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://global.hgncloud.com/[companyname]/saml/login`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Do textového pole **Adresa URL odpovědi (adresa URL služby vyhodnocení zákazníka)** zadejte adresu URL pomocí následujícího vzoru: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, kontaktujte prosím [Saba TalentSpace týmu podpory klientů](https://support.saba.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Saba TalentSpace** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Saba TalentSpace.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Saba TalentSpace**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-saba-talentspace-sso"></a>Konfigurace Saba TalentSpace SSO

1. V jiném okně prohlížeče se přihlaste k aplikaci **Saba TalentSpace** jako správce.

2. Klikněte na kartu **Možnosti** .
  
    ![Snímek obrazovky zobrazující domovskou stránku "Saba TalentSpace" se zvolenou kartou možnosti.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. V levém navigačním podokně klikněte na **Konfigurace SAML**.
  
    ![Snímek obrazovky, který zobrazuje levé navigační podokno uživatelského rozhraní s vybranou konfigurací S A M L.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Na stránce **Konfigurace SAML** proveďte následující kroky:

    ![Snímek obrazovky zobrazující stránku konfigurace S M L s zvýrazněnými možnostmi nastavení](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Jako **jedinečný identifikátor** vyberte **NameId**.

    b. V případě, že **se mapuje jedinečný identifikátor**, vyberte **uživatelské jméno**.
  
    c. Pokud chcete nahrát stažený soubor metadat, klikněte na **Procházet** a vyberte soubor a pak **nahrajte soubor**.

    d. Chcete-li otestovat konfiguraci, klikněte na tlačítko **Spustit test**.

    > [!NOTE]
    > Musíte počkat na dokončení zprávy "*test SAML". Zavřete prosím toto okno*. Pak zavřete otevřené okno prohlížeče. Zaškrtávací políčko **Povolit SAML** je povoleno pouze v případě, že byl test dokončen.

    e. Vyberte **Povolit SAML**.

    f. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-saba-talentspace-test-user"></a>Vytvořit Saba TalentSpace Test User

Cílem této části je vytvořit uživatele s názvem Britta Simon v Saba TalentSpace.

**Chcete-li vytvořit uživatele s názvem Britta Simon v Saba TalentSpace, proveďte následující kroky:**

1. Přihlaste se k aplikaci **Saba TalentSpace** jako správce.

2. Klikněte na kartu **centrum uživatelů** a pak klikněte na **vytvořit uživatele**.

    ![Snímek obrazovky zobrazující, že je vybraná karta "centrum uživatelů" a "vytvořit uživatele".](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Na stránce **Nový uživatel** proveďte následující kroky:

    ![Co je služba Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Do textového pole **název** zadejte jméno uživatele jako **B**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **uživatelské jméno** zadejte **B. Simon**, uživatelské jméno jako v Azure Portal.

    d. Do textového pole **heslo** zadejte heslo pro B. Simon.

    e. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Saba TalentSpace adresu URL pro přihlášení, kde můžete spustit tok přihlášení. 

* Přejít na Saba TalentSpace adresu URL pro přímé přihlášení a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Saba TalentSpace v okně moje aplikace byste měli být automaticky přihlášeni k TalentSpace Saba, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

 Jakmile nakonfigurujete Saba TalentSpace, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).