---
title: 'Kurz: Azure Active Directory integrace se systémem jednotného přihlašování (GTNexus) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directorym a GTNexusm systémem jednotného přihlašování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: jeedes
ms.openlocfilehash: 11439a8c93c0f1ef682e7104e989a3a2cbbdc28e
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92446731"
---
# <a name="tutorial-azure-active-directory-integration-with-gtnexus-sso-system"></a>Kurz: Azure Active Directory integrace se systémem jednotného přihlašování GTNexus

V tomto kurzu se naučíte integrovat systém GTNexus SSO pomocí služby Azure Active Directory (Azure AD).
Integrace systému GTNexus SSO do Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k GTNexus systému jednotného přihlašování.
* Uživatelům můžete povolit, aby se automaticky přihlásili k systému GTNexus SSO (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se systémem GTNexus SSO budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné GTNexus jednotného přihlašování k systému jednotné přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Systém jednotného přihlašování (GTNexus SSO) podporuje **IDP** INICIované SSO

## <a name="adding-gtnexus-sso-system-from-the-gallery"></a>Přidání systému jednotného přihlašování GTNexus z Galerie

Pokud chcete nakonfigurovat integraci systému jednotného přihlašování GTNexus do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat GTNexus systém jednotného přihlašování z galerie.

**Pokud chcete do galerie přidat systém jednotného přihlašování GTNexus, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **GTNEXUS SSO System**, vyberte systém jednotného **přihlašování GTNexus** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![GTNexus systém jednotného přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí systému GTNexus SSO založeného na testovacím uživateli s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v systému GTNexus SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí systému jednotného přihlašování (GTNexus SSO), musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování systému GTNEXUS SSO](#configure-gtnexus-sso-system-single-sign-on)** – pro konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte uživatele testovacího systému GTNEXUS SSO](#create-gtnexus-sso-system-test-user)** , který bude mít protějšek Britta Simon v systému GTNexus SSO, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí systému jednotného přihlašování (GTNexus SSO), proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **systému GTNexus SSO** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Snímek obrazovky zobrazující stránku konfigurace "základní S A M L" se zvolenou akcí nahrát soubor metadat ".](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![Snímek obrazovky s vybraným polem "vybrat soubor" s logem "složka" a tlačítkem Odeslat.](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi automaticky naplní v textovém poli GTNexus jednotného oddílu systému souborů SSO:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Pokud hodnoty **adresy URL** pro **identifikátor** a odpověď nezískávají automaticky polulated, zadejte je ručně podle vašich požadavků.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-gtnexus-sso-system-single-sign-on"></a>Konfigurace GTNexus jednotného přihlašování k systému Sign-On

Ke konfiguraci jednotného přihlašování na straně serveru **GTNEXUS SSO** je potřeba odeslat **XML federačních metadat** do [týmu podpory systému GTNexus SSO](mailto:support@gtnexus.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k systému GTNexus SSO.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **systém jednotného přihlašování (GTNexus**).

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **GTNexus systém jednotného přihlašování**.

    ![Odkaz na systém jednotného přihlašování GTNexus v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-gtnexus-sso-system-test-user"></a>Vytvořit uživatele testu systému GTNexus SSO

V této části vytvoříte uživatele s názvem Britta Simon v systému GTNexus SSO. Pokud chcete přidat uživatele na systémovou platformu jednotného přihlašování GTNexus, pracujte s [týmem podpory systému GTNEXUS SSO](mailto:support@gtnexus.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici systém GTNexus SSO na přístupovém panelu, měli byste se automaticky přihlásit k systému GTNexus SSO, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)