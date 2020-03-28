---
title: 'Kurz: Integrace služby Azure Active Directory se službou Signagelive | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160927"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Kurz: Integrace Služby Azure Active Directory se službou Signagelive

V tomto kurzu se dozvíte, jak integrovat Signagelive s Azure Active Directory (Azure AD).
Integrace Signagelive s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Signagelive.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Signagelive (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Signagelive, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Signagelive s podporou jednotného přihlášení.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Signagelive podporuje sp-iniciované přihlašování.

## <a name="add-signagelive-from-the-gallery"></a>Přidat Signagelive z galerie

Pokud chcete nakonfigurovat integraci Signagelive do Azure AD, nejdřív přidejte Signagelive z galerie do seznamu spravovaných aplikací SaaS.

Chcete-li přidat Signagelive z galerie, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **části Podnikové aplikace**a vyberte možnost Všechny **aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Signagelive**. 

     ![Signagelive v seznamu výsledků](common/search-new-app.png)

5. V podokně výsledků vyberte **Signagelive** a pak vyberte tlačítko **Přidat** a přidejte aplikaci.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Signagelive na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem ve Službě Signagelive.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí signagelive, nejprve vyplňte následující stavební bloky:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby uživatelé mohli tuto funkci používat.
2. [Nakonfigurujte jednotné přihlašování Signagelive](#configure-signagelive-single-sign-on) tak, aby bylo nakonfigurováno nastavení jednotného přihlášení na straně aplikace.
3. [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD s Brittou Simonovou.
4. [Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user) aby britta Simon mohla používat jednotné přihlašování Azure AD.
5. [Vytvořte testovacího uživatele Signagelive,](#create-a-signagelive-test-user) který bude mít protějšek Britty Simonové ve Signagelive, který je propojený s reprezentací Azure AD uživatele.
6. [Otestujte jednotné přihlášení](#test-single-sign-on) a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Signagelive, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Signagelive** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlášení** vyberte možnost **SAML,** chcete-li povolit jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit,** abyste otevřeli dialogové okno **Základní konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** postupujte takto:

    ![Informace o jednotném přihlášení do domény Signagelive a adres URL](common/sp-signonurl.png)

    Do pole **Přihlašovací adresa URL** zadejte adresu URL, která používá následující vzorec:`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat hodnotu, obraťte se na [tým podpory klienta Signagelive](mailto:support@signagelive.com) . Můžete také odkazovat na vzory, které jsou zobrazeny v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** v části **Podpisový certifikát SAML** vyberte **stáhnout a** stáhněte si certifikát **(Nezpracovaný)** z daných možností podle vašeho požadavku. Pak jej uložte do počítače.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V části **Nastavit Signagelive** zkopírujte url, které potřebujete.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-signagelive-single-sign-on"></a>Konfigurace jednotného přihlašování Signagelive

Chcete-li nakonfigurovat jednotné přihlašování na straně Signagelive, odešlete stažený **certifikát (Raw)** a zkopírované adresy URL z portálu Azure týmu [podpory Signagelive](mailto:support@signagelive.com). Zajišťují, že připojení jednotného přistajení SAML je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko Nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** brittasimon@yourcompanydomain.extensionzadejte " ". Například v tomto případě můžeteBrittaSimon@contoso.comzadat " ".

    c. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Signagelive.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Signagelive**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Signagelive**.

    ![Odkaz Signagelive v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Vyberte tlačítko **Přidat uživatele.** Potom v dialogovém okně **Přidat přiřazení** vyberte Možnost **I Uživatelé a skupiny**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** **Brittu Simonovou**. Pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Dále klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **Přiřadit.**

### <a name="create-a-signagelive-test-user"></a>Vytvoření testovacího uživatele Signagelive

V této části vytvoříte uživatele s názvem Britta Simon v Signagelive. Spolupracujte s [týmem podpory Signagelive](mailto:support@signagelive.com) a přidejte uživatele do platformy Signagelive. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu MyApps.

Když na portálu MyApps vyberete dlaždici **Signagelive,** měli byste být automaticky přihlášeni. Další informace o portálu MyApps najdete v [tématu Co je portál MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

