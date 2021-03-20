---
title: 'Kurz: integrace s Azure Active Directoryem pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Veritas Enterprise Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5e777a27e793b1f4ef5489248c170824d69d615c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517680"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: integrace s Azure Active Directoryem pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud

V tomto kurzu se dozvíte, jak integrovat službu Veritas Enterprise Vault.cloud SSO pomocí služby Azure Active Directory (Azure AD).
Integrování programu Veritas Enterprise Vault.cloud SSO s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k webu Veritas Enterprise Vault.cloud SSO.
* Uživatelům můžete povolit, aby se automaticky přihlásili k programu Veritas Enterprise Vault.cloud SSO (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

K nakonfigurování integrace služby Azure AD pomocí jednotného přihlašování společnosti Veritas Enterprise Vault.cloud potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné Veritas Enterprise Vault.cloud SSO s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Veritas Enterprise Vault.cloud SSO podporuje jednotné přihlašování v rámci **SP**

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidání jednotného přihlašování k programu Veritas Enterprise Vault.cloud z Galerie

Pokud chcete nakonfigurovat integraci programu Veritas Enterprise Vault.cloud SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat program Veritas Enterprise Vault.cloud SSO z galerie.

**K přidání programu Veritas Enterprise Vault.cloud SSO z Galerie proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Veritas enterprise Vault.cloud SSO**, vyberte **Veritas Enterprise Vault.cloud SSO** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Veritas Enterprise Vault.cloud SSO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování pomocí programu Veritas Enterprise Vault.cloud SSO na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Veritas Enterprise Vault.cloud SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování pro Veritas Enterprise Vault.cloud, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování pro Veritas Enterprise Vault.cloud SSO](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte uživatele Veritas enterprise Vault.cloud SSO Test User](#create-veritas-enterprise-vaultcloud-sso-test-user)** – abyste měli protějšek Britta Simon v Veritas Enterprise Vault.cloud SSO, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování pro Veritas Enterprise Vault.cloud, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací pro **Veritas Enterprise Vault.cloud SSO** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování pro doménu Veritas Enterprise Vault.cloud SSO a adresy URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. V poli **identifikátor** použijte adresu URL dle datového centra:

    | Datové centrum| URL |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. V textovém poli **Adresa URL odpovědi** použijte adresu URL v rámci datového centra:

    | Datové centrum| URL |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Tuto hodnotu získáte od [společnosti Veritas Enterprise Vault.cloud SSO Client Support Team](https://www.veritas.com/support/.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení pro Veritas Enterprise Vault.cloud SSO** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Konfigurace jednotného Sign-On jednotného přihlašování pro Veritas Enterprise Vault.cloud

Ke konfiguraci jednotného přihlašování na straně **jednotného přihlašování společnosti Veritas enterprise Vault.Cloud** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory pro Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k webu Veritas Enterprise Vault.cloud SSO.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Odkaz na jednotné přihlašování k webu Veritas Enterprise Vault.cloud v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvoření testovacího uživatele jednotného přihlašování pro Veritas Enterprise Vault.cloud

V této části vytvoříte uživatele s názvem Britta Simon v Veritas Enterprise Vault.cloud SSO. Spolupracujte se svým [týmem podpory pro Veritas enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) a přidejte uživatele na platformě veritas Enterprise Vault.cloud SSO. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Vault.cloud SSO Enterprise SSO na přístupovém panelu byste se měli automaticky přihlášeni k přihlašování k webu Veritas Enterprise Vault.cloud SSO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)