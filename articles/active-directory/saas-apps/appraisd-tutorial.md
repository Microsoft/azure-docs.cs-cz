---
title: 'Kurz: Azure Active Directory integrace s hodnocením | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hodnocení.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 79db0a7bb769d6138bfeeb4e765621a1cffb4d14
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457856"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Kurz: integrace vyhodnocení pomocí Azure Active Directory

V tomto kurzu se naučíte, jak integrovat vyhodnocení pomocí Azure Active Directory (Azure AD). Když integraci vyhodnotí s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k posouzení.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k vyhodnocení jejich účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Vyhodnocen odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Vyhodnocení podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-appraisd-from-the-gallery"></a>Přidání hodnocení z Galerie

Pokud chcete nakonfigurovat integraci hodnocení do Azure AD, musíte přidat vyhodnocenou z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **hodnocení** .
1. Vyberte možnost **vyhodnoceno** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s hodnocením pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v hodnocení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí vyhodnocení, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace se vyhodnotí](#configure-appraisd)** tak, že se na straně aplikace nakonfigurují nastavení jednotného přihlašování.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořte vyhodnoceného testovacího uživatele](#create-appraisd-test-user)** , který má protějšek B. Simon ve vyhodnoceném, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **vyhodnocená** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko Uložit a provedením následujících kroků:

    a. Klikněte na **nastavit další adresy URL**.

    b. Do textového pole **stav přenosu** zadejte adresu URL: `<TENANTCODE>`

    c. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , zadejte do textového pole **přihlašovací adresa** URL adresu URL pomocí následujícího vzoru: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Na stránce pro konfiguraci hodnocení jednotného přihlašování získáte skutečnou adresu URL a stav přenosu, který je vysvětlen dále v tomto kurzu.

1. Vyhodnocená aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Vyhodnocená aplikace očekává, že **NameIdentifier** budou namapovány pomocí **User. mail**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![Snímek obrazovky se zobrazí v podokně atributů uživatele se zvýrazněnou ikonou pro úpravy.](common/edit-attribute.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit hodnocení** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurace hodnocení

1. Chcete-li automatizovat konfiguraci v rámci hodnocení, je nutné nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavení hodnocení** , které vás přesměruje na vyhodnocenou aplikaci. Odtud zadejte přihlašovací údaje správce, které se přihlásí k vyhodnocení. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit hodnocení ručně, otevřete nové okno webového prohlížeče a přihlaste se k vaší vyhodnocené společnosti jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na ikonu **Nastavení** a potom přejděte ke **konfiguraci**.

    ![Snímek obrazovky se zobrazeným odkazem konfigurace.](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. V levé části nabídky klikněte na **jednotné přihlašování SAML**.

    ![Snímek obrazovky zobrazuje možnosti konfigurace s zvýrazněnou možností jednotného přihlašování SAML.](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na stránce **Konfigurace jednoduchého Sign-On SAML 2,0** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce konfigurace pro jednu Sign-On SAML 2,0, kde můžete upravit výchozí stav přenosu a přihlášení iniciované službou U R L.](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Zkopírujte **výchozí hodnotu stavu přenosu** a vložte ji do textového pole **stav přenosu** v **základní konfiguraci SAML** na Azure Portal.

    b. Zkopírujte hodnotu **adresy URL pro přihlášení iniciované službou** a vložte ji do textového pole **přihlašovací adresa URL** v **základní konfiguraci SAML** na Azure Portal.

7. Posuňte se dolů na stejnou stránku pod položkou **identifikovat uživatele**, proveďte následující kroky:

    ![Snímek obrazovky ukazuje identifikaci uživatelů, kde můžete zadat hodnoty z tohoto kroku.](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Do textového pole **URL s jedním Sign-On URL poskytovatele identity** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal a klikněte na **Uložit**.

    b. V poli **Adresa URL vydavatele zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal, a klikněte na **Uložit**.

    c. V programu Poznámkový blok otevřete certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah a vložte ho do pole **certifikát X. 509** a klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B. Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k vyhodnocenému.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Hodnoceno**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-appraisd-test-user"></a>Vytvořit hodnoceného testovacího uživatele

Aby se uživatelé Azure AD mohli přihlásit k posouzení, musí se zřídit pro vyhodnocení. V rámci hodnocení je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k posouzení jako správce zabezpečení.

2. V pravém horním rohu stránky klikněte na ikonu **Nastavení** a potom přejděte do **centra pro správu**.

    ![Snímek obrazovky se zobrazí možnosti nastavení, kde můžete vybrat centrum pro správu.](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na panelu nástrojů v horní části stránky klikněte na tlačítko **lidé**a pak přejděte k části **Přidání nového uživatele**.

    ![Snímek obrazovky s lidmi zobrazuje vyhodnocenou stránku a přidá nového uživatele.](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na stránce **Přidat nového uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka Přidat nového uživatele.](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **e-mailu** zadejte e-maily uživatele jako `B. Simon@contoso.com` .

    d. Klikněte na **Přidat uživatele**.

### <a name="test-sso"></a>Test SSO

Když vyberete vyhodnocenou dlaždici na přístupovém panelu, měli byste se automaticky přihlásit k vyhodnocení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)