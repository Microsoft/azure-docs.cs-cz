---
title: 'Kurz: Azure Active Directory integrace s RunMyProcess | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RunMyProcess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518445"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Kurz: integrace RunMyProcess s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat RunMyProcess s Azure Active Directory (Azure AD). Když integrujete RunMyProcess s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k RunMyProcess.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k RunMyProcess svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* RunMyProcess odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RunMyProcess podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-runmyprocess-from-the-gallery"></a>Přidání RunMyProcess z Galerie

Pokud chcete nakonfigurovat integraci RunMyProcess do služby Azure AD, musíte přidat RunMyProcess z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **RunMyProcess** .
1. Na panelu výsledků vyberte **RunMyProcess** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí RunMyProcess pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v RunMyProcess.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí RunMyProcess, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte RUNMYPROCESS SSO](#configure-runmyprocess-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte RunMyProcess Test User](#create-runmyprocess-test-user)** -to, abyste měli protějšek B. Simon v RunMyProcess, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **RunMyProcess** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta RunMyProcess](mailto:support@runmyprocess.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení RunMyProcess** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Konfigurace jednotného přihlašování RunMyProcess

1. V jiném okně webového prohlížeče se přihlaste k tenantovi RunMyProcess jako správce.

1. V levém navigačním panelu klikněte na **účet** a vyberte **Konfigurace**.

    ![Snímek obrazovky zobrazuje konfiguraci vybranou z účtu.](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. V části **metoda ověřování** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na kartě metoda ověřování, kde můžete zadat hodnoty, které jsou popsány.](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Jako **metodu**vyberte **jednotné přihlašování s Samlv2**.

    b. Do textového pole pro **přesměrování jednotného přihlašování** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole pro **přesměrování odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **formát ID názvu** zadejte hodnotu **Formát identifikátoru názvu** jako **urn: Oasis: names: TC: SAML: 1.1: NameId-Format: EmailAddress**.

    e. Otevřete stažený soubor certifikátu z Azure Portal v programu Poznámkový blok, zkopírujte obsah souboru certifikátu a vložte ho do textového pole **certifikátu** .

    f. Klikněte na **Uložit** ikonu.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k RunMyProcess.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **RunMyProcess**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-runmyprocess-test-user"></a>Vytvořit testovacího uživatele RunMyProcess

Aby se uživatelé Azure AD mohli přihlašovat k RunMyProcess, musí se zřídit v RunMyProcess. V případě RunMyProcess je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu RunMyProcess společnosti jako správce.

1. Klikněte na **účet** a vyberte **Uživatelé** v levém navigačním panelu a pak klikněte na **Nový uživatel**.

    ![Nový uživatel](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nový uživatel")

1. V části **nastavení uživatele** proveďte následující kroky:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Do příslušných textových polí zadejte **název** a **E-mail** platného účtu Azure AD, který chcete zřídit.

    b. Vyberte **jazyk IDE**, **jazyk**a **profil**.

    c. Vyberte **Odeslat e-mail pro vytvoření účtu**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů RunMyProcess nebo rozhraní API poskytovaná RunMyProcess.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici RunMyProcess, měli byste se automaticky přihlásit k RunMyProcess, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)