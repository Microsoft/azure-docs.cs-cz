---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Teamphoria | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Teamphoria.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: a8cf58f49cba1bcc77c96a0da2061a037bb62bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92504738"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Teamphoria

V tomto kurzu se dozvíte, jak integrovat Teamphoria s Azure Active Directory (Azure AD). Když integrujete Teamphoria s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Teamphoria.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Teamphoria svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Teamphoria odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Teamphoria podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Přidání Teamphoria z Galerie

Pokud chcete nakonfigurovat integraci Teamphoria do služby Azure AD, musíte přidat Teamphoria z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Teamphoria** .
1. Na panelu výsledků vyberte **Teamphoria** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Teamphoria

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Teamphoria pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Teamphoria.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Teamphoria, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte TEAMPHORIA SSO](#configure-teamphoria-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Teamphoria Test User](#create-teamphoria-test-user)** -to, abyste měli protějšek B. Simon v Teamphoria, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Teamphoria** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Teamphoria](https://www.teamphoria.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Teamphoria** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Teamphoria.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Teamphoria**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-teamphoria-sso"></a>Konfigurace jednotného přihlašování Teamphoria

1. Pokud chcete automatizovat konfiguraci v rámci Teamphoria, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit Teamphoria** , které vás přesměruje do aplikace Teamphoria. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Teamphoria. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Teamphoria ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Teamphoria společnosti jako správce a proveďte následující kroky:

4. Přejděte na možnost **Nastavení správce** na levém panelu nástrojů a na kartě konfigurovat klikněte na **jednotné přihlašování** . otevře se okno Konfigurace jednotného přihlašování.

    ![Snímek obrazovky ukazuje nastavení správce, kde můžete vybrat jednotné přihlašování.](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Kliknutím na **Přidat novou možnost zprostředkovatel identity** v pravém horním rohu otevřete formulář pro přidání nastavení pro jednotné přihlašování.

    ![Snímek obrazovky ukazuje, kde můžete vybrat přidat nového poskytovatele IDENTITY.](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Zadejte podrobnosti do polí, jak je popsáno níže.

    ![Snímek obrazovky se zobrazí stránka, kde můžete zadat hodnoty, které jsou popsány.](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **ZOBRAZOVANÝ název**: Zadejte zobrazovaný název modulu plug-in na stránce správce.

    b. **Název tlačítka**: název karty, která se zobrazí na přihlašovací stránce pro přihlášení prostřednictvím jednotného přihlašování.

    c. **Certifikát**: otevřete dříve stažený certifikát z Azure Portal v programu Poznámkový blok, zkopírujte obsah stejného umístění a vložte ho sem do pole.

    d. **Vstupní bod**: vložte **adresu URL pro přihlášení** zkopírovanou dříve z Azure Portal.

    e. Přepněte možnost na **zapnuto** a klikněte na **Uložit**.

### <a name="create-teamphoria-test-user"></a>Vytvořit testovacího uživatele Teamphoria

Aby se uživatelé Azure AD mohli přihlašovat k Teamphoria, musí se zřídit v Teamphoria. V případě Teamphoria je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Teamphoria společnosti jako správce.

1. Kliknutím na nastavení **správce** na levém panelu nástrojů a na kartě **Spravovat** klikněte na **Uživatelé** a otevřete stránku správce pro uživatele.

    ![Přidat zaměstnance](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klikněte na možnost **Ruční Pozvánka** .

    ![Snímek obrazovky zobrazuje možnost RUČNÍho pozvání.](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Na této stránce proveďte následující akci.

    ![Snímek obrazovky se zobrazí na stránce ruční pozvání uživatele, kde můžete zadat jméno a e-mailovou adresu.](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Do textového pole **e-mailová adresa** zadejte **e-mailovou adresu** uživatele, jako je B. Simon.

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    d. Klikněte na **pozvat 1 uživatele**. Uživatel musí přijmout pozvánku, aby se mohl v systému vytvořit.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Teamphoria, měli byste se automaticky přihlásit k Teamphoria, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Teamphoria s Azure AD](https://aad.portal.azure.com/)