---
title: 'Kurz: Azure Active Directory integrace s Tableau online | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau online.
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
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731965"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Tableau online

V tomto kurzu se dozvíte, jak integrovat Tableau online s Azure Active Directory (Azure AD). Když integrujete Tableau online s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Tableau online
* Umožněte uživatelům, aby se automaticky přihlásili k Tableau online pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Tableau online jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Tableau online podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-tableau-online-from-the-gallery"></a>Přidání Tableau do online režimu z Galerie

Pokud chcete nakonfigurovat integraci Tableau online do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Tableau online z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Tableau online** .
1. Na panelu výsledků vyberte **Tableau online** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Tableau online

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Tableau online na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Tableauu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Tableau online, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Tableau jednotné](#configure-tableau-online-sso)** přihlašování online – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte online testovacího uživatele Tableau](#create-tableau-online-test-user)** , abyste měli protějšek B. Simon v Tableau online, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce online integrace aplikací **Tableau** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > `<entityid>`Tuto hodnotu získáte v tomto kurzu v části **nastavení online Tableau** . Hodnota ID entity bude hodnota **identifikátoru Azure AD** v **nastavení online** oddílu pro Tableau.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavení online Tableau** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Tableau online.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Tableau online**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-tableau-online-sso"></a>Konfigurace online jednotného přihlašování Tableau

1. Pokud chcete automatizovat konfiguraci v rámci služby Tableau online, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit Tableau online** a přesměrujte vás na online aplikaci Tableau. Odtud zadejte přihlašovací údaje správce pro přihlášení k Tableau online. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Tableau online ručně, v jiném okně webového prohlížeče se přihlaste ke svému webu Tableau online společnosti jako správce.

1. Přejít na **Nastavení** a pak na **ověřování**.

    ![Snímek obrazovky s vybraným ověřováním zobrazuje v nabídce nastavení.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Chcete-li povolit SAML, v části **typy ověřování** . Zaškrtněte políčko **Povolit další metodu ověřování** a zaškrtněte políčko **SAML** .

    ![Snímek obrazovky se zobrazí v části typy ověřování, kde můžete vybrat hodnoty.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Posuňte se dolů a **importujte soubor metadat do online oddílu Tableau** .  Klikněte na Procházet a importujte soubor metadat, který jste stáhli ze služby Azure AD. Pak klikněte na **použít**.

   ![Snímek obrazovky se zobrazí oddíl, do kterého můžete importovat soubor metadat.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. V části **kontrolní výrazy shody** vložte odpovídající název kontrolního výrazu zprostředkovatele identity pro **e-mailovou adresu**, **křestní jméno** a **příjmení**. Získání těchto informací z Azure AD: 
  
    a. V Azure Portal navštivte stránku online integrace aplikací **Tableau** .

    b. V části **atributy uživatele & deklarace identity** klikněte na ikonu Upravit.

   ![Snímek obrazovky se zobrazí v části atributy uživatele & deklarací, kde můžete vybrat ikonu Upravit.](./media/tableauonline-tutorial/attributesection.png)

    c. Zkopírujte hodnotu oboru názvů těchto atributů: křestní číslo, e-mail a příjmení pomocí následujících kroků:

   ![Snímek obrazovky ukazuje atributy daného atributu, příjmení a EmailAddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klikněte na tlačítko **uživatel. prodané** hodnoty

    e. Zkopírujte hodnotu z textového pole **obor názvů** .

    ![Snímek obrazovky se zobrazí část Správa deklarací identity uživatelů, kde můžete zadat obor názvů.](./media/tableauonline-tutorial/attributesection2.png)

    f. Pokud chcete zkopírovat hodnoty oboru názvů pro e-mail a příjmení, opakujte výše uvedené kroky.

    například Přepněte do online aplikace Tableau a pak nastavte **atributy uživatele & deklarace identity** následujícím způsobem:

    * E-mail: **mail** nebo **userPrincipalName**

    * Jméno **: křestní** jméno

    * Příjmení: **příjmení**

    ![Snímek obrazovky se zobrazí v sekci atributů shody, kde můžete zadat hodnoty.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Vytvořit online testovacího uživatele Tableau

V této části vytvoříte uživatele s názvem Britta Simon v Tableau online.

1. V **online režimu Tableau** klikněte na **Nastavení** a pak na **ověřovací** oddíl. Přejděte dolů do části **Správa uživatelů** . Klikněte na **Přidat uživatele** a pak na **zadat e-mailové adresy**.
  
    ![Snímek obrazovky se zobrazí oddíl spravovat uživatele, kde můžete vybrat přidat uživatele.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Vyberte možnost **Přidat uživatele pro ověřování SAML**. Do textového pole **Zadejte e-mailové adresy** přidejte Britta. Simon \@ contoso.com
  
    ![Snímek obrazovky se zobrazí stránka Přidat uživatele, kde můžete zadat e-mailovou adresu.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klikněte na **Přidat uživatele**.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Tableau adresu URL pro přihlášení do online režimu, kde můžete spustit tok přihlášení.

* Přejít na adresu URL online přihlašování na Tableau a spustit tok přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Tableau online v nabídce Moje aplikace, přesměruje se na Tableau Online přihlašovací adresu URL. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Tableau online můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)