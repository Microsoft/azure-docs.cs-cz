---
title: 'Kurz: Azure Active Directory integrace se zálohováním koncového bodu automatického úkolu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a úlohou automatického zálohování koncového bodu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/19/2019
ms.author: jeedes
ms.openlocfilehash: 349a77d2f2cd5e1c492de63919d84efb436a51a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673677"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-endpoint-backup"></a>Kurz: Azure Active Directory integrace se zálohováním koncového bodu automatického úkolu

V tomto kurzu se naučíte, jak integrovat zálohování koncového bodu AutoTask pomocí Azure Active Directory (Azure AD).
Integrace zálohování koncového bodu AutoTask pomocí Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k zálohování koncového bodu automatického úkolu.
* Můžete uživatelům povolit, aby se automaticky přihlásili k automatickému přihlášení ke službě Endpoint Endpoint Backup (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí zálohování koncového bodu automatického úkolu potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné automatického úkolu zálohování koncového bodu s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zálohování koncového bodu automatického úkolu podporuje **IDP** , které bylo inicializováno.

## <a name="adding-autotask-endpoint-backup-from-the-gallery"></a>Přidává se záloha koncového bodu automatického úkolu z galerie.

Pokud chcete nakonfigurovat integraci automatického zálohování úloh do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zálohu koncových bodů automatického úkolu z galerie.

**Pokud chcete do galerie přidat zálohování koncového bodu automatického úkolu, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz pro **zálohování koncového bodu služby AutoTask**, vyberte možnost **Autoúloha zálohování koncového bodu** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zálohování koncového bodu automatického úkolu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí zálohování koncového bodu automatického úkolu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci zálohování koncového bodu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zálohování koncového bodu automatického úkolu, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování v rámci automatického úkolu zálohování koncového bodu](#configure-autotask-endpoint-backup-single-sign-on)** – ke konfiguraci jednoho Sign-On nastavení na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit autoúlohu záložní koncový bod koncového uživatele](#create-autotask-endpoint-backup-test-user)** – Chcete-li mít protějšek Britta Simon v zálohování koncového bodu automatického úkolu, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí zálohování koncového bodu automatického úkolu, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **koncového bodu automatického úkolu** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování pro doménu a adresy URL pro zálohování koncových bodů úlohy](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.backup.autotask.net/singlesignon/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.backup.autotask.net/singlesignon/saml/SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta podpory pro úlohy automatického úkolu](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení automatického zálohování koncového bodu úlohy** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-autotask-endpoint-backup-single-sign-on"></a>Konfigurace jednoho Sign-On zálohování koncového bodu automatického úkolu

Chcete-li nakonfigurovat jednotné přihlašování na straně **zálohování koncového bodu automatického úkolu** , je třeba odeslat stažená **metadata federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory zálohování koncového bodu](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k zálohování koncového bodu automatického úkolu.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **zálohování koncového bodu automatického úkolu**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **zálohování koncového bodu automatického úkolu**.

    ![Odkaz na zálohování koncového bodu automatického úkolu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-autotask-endpoint-backup-test-user"></a>Vytvořit autoúlohu – koncový uživatel záložního koncového bodu

V této části vytvoříte uživatele s názvem Britta Simon v zálohování koncového bodu automatického úkolu. Pokud chcete přidat uživatele na platformě zálohování koncového bodu automatického úkolu, pracujte s nástrojem pro [zálohování koncového bodu služby automatického úkolu](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici zálohování koncového bodu automatického úkolu, měli byste být automaticky přihlášeni k zálohování koncového bodu automatického úkolu, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)