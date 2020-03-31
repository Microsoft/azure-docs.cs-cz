---
title: 'Kurz: Integrace služby Azure Active Directory s HubSpotem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944451"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Kurz: Integrace Azure Active Directory s HubSpotem

V tomto kurzu se dozvíte, jak integrovat HubSpot s Azure Active Directory (Azure AD).

Integrace HubSpot u Azure AD vám poskytuje následující výhody:

* Azure AD můžete použít k řízení, kdo má přístup k HubSpot.
* Uživatelé se můžou automaticky přihlásit k HubSpotu pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, na webu Azure Portal.

Další informace o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s HubSpot, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure AD, vytvořte [si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné HubSpot s povoleným jedním přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete HubSpot s Azure AD.

HubSpot podporuje následující funkce:

* **Jednotné přihlašování iniciované sp**
* **Jednotné přihlašování iniciované idicí IDP**

## <a name="add-hubspot-in-the-azure-portal"></a>Přidání HubSpotu na portál Azure

Chcete-li integrovat HubSpot s Azure AD, musíte přidat HubSpot do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levé nabídce vyberte **Azure Active Directory**.

    ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **možnost Nová aplikace**.

    ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **HubSpot**. Ve výsledcích hledání vyberte **HubSpot**a pak vyberte **Přidat**.

    ![HubSpot v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí HubSpot na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem Azure AD a souvisejícím uživatelem v HubSpotu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí HubSpot, musíte dokončit následující stavební bloky:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování HubSpot](#configure-hubspot-single-sign-on)** | Konfiguruje nastavení jednotného přihlášení v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotné přihlašování. |
| **[Vytvoření testovacího uživatele HubSpot](#create-a-hubspot-test-user)** | Vytvoří protějšek Britta Simon v HubSpot, který je propojen s reprezentaci Azure AD uživatele. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověří, zda konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí HubSpot u webu Azure Portal.

1. Na [portálu Azure](https://portal.azure.com/)vyberte v podokně integrace aplikací **HubSpot** **možnost Jedno přihlášení**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML** nebo **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), chcete-li otevřít podokno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Chcete-li nakonfigurovat *režim iniciátoru IDP,* proveďte v **podokně Základní konfigurace SAML** následující kroky:

    1. Do pole **Identifikátor** zadejte adresu URL, která\/má následující\<vzorec:\>https: /api.hubspot.com/login-api/v1/saml/login?portalId= CUSTOMER ID .

    1. Do pole **Adresa URL pro odpověď** zadejte adresu\/URL,\<která má\>následující vzorec: https: /api.hubspot.com/login-api/v1/saml/acs?portalId= CUSTOMER ID .

    ![Centrální doména a adresy URL informace o jednotném přihlášení](common/idp-intiated.png)

    > [!NOTE]
    > Chcete-li formátovat adresy URL, můžete také odkazovat na vzory zobrazené v podokně **Základní konfigurace SAML** na webu Azure Portal.

1. Postup konfigurace aplikace v režimu *iniciovaném službou SP:*

    1. Vyberte **Nastavit další adresy URL**.

    1. Do pole **Přihlásit se na adresu URL** zadejte **https:\//app.hubspot.com/login**.

    ![Možnost Nastavit další adresy URL](common/metadata-upload-additional-signon.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **položku Stáhnout** vedle **certifikátu (Base64).** Vyberte možnost stahování na základě vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu (Base64)](common/certificatebase64.png)

1. V části **Nastavit HubSpot** zkopírujte na základě svých požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor azure reklamy
    * Adresa URL odhlášení

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurace jednotného přihlašování HubSpot

1. Otevřete si v prohlížeči novou kartu a přihlaste se ke svému účtu správce HubSpot.

1. V pravém horním rohu stránky vyberte ikonu **Nastavení.**

    ![Ikona Nastavení v HubSpotu](./media/hubspot-tutorial/config1.png)

1. Vyberte **Výchozí nastavení účtu**.

    ![Možnost Výchozí účet v HubSpotu](./media/hubspot-tutorial/config2.png)

1. Posuňte se dolů do části **Zabezpečení** a pak vyberte **Nastavit**.

    ![Možnost Nastavit v HubSpotu](./media/hubspot-tutorial/config3.png)

1. V části **Nastavit jednotné přihlašování** proveďte následující kroky:

    1. V poli **Audience URl (ID entity poskytovatele služeb)** vyberte **Kopírovat,** chcete-li hodnotu zkopírovat. Na webu Azure Portal vložte hodnotu do pole **Identifikátor** v **podokně Základní konfigurace SAML.**

    1. V poli **Přihlásit se na url, ACS, Příjemce nebo Přesměrování** vyberte **Kopírovat,** chcete-li hodnotu zkopírovat. Na webu Azure Portal vložte hodnotu do pole **Adresa URL odpovědi** v **podokně Základní konfigurace SAML.**

    1. V HubSpot, v poli **Identifikátor zprostředkovatele identity nebo Adresa URL vystavitele,** vložte hodnotu **pro identifikátor Azure AD,** který jste zkopírovali na webu Azure Portal.

    1. V HubSpot, v poli **Adresa URL jednotného přihlášení zprostředkovatele identity,** vložte hodnotu přihlašovací **adresy URL,** kterou jste zkopírovali na webu Azure Portal.

    1. V programu Windows Notepad otevřete soubor certifikátu (Base64), který jste stáhli. Vyberte a zkopírujte obsah souboru. Potom v HubSpot, vložte jej do pole **Certifikát X.509.**

    1. Vyberte možnost **ověření**.

        ![Oddíl Nastavení jednotného přihlašování v HubSpotu](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovací ho uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte**Možnost Uživatelé Služby** >  **Azure Active Directory** > **Všichni uživatelé**.

    ![Možnosti Uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **Uživatel** proveďte následující kroky:

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **\@\<brittasimon>\< domény vaší společnosti. prodloužení\>**. Například **brittasimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo.** Poznamenejte si hodnotu, která je zobrazena v poli **Heslo.**

    1. Vyberte **Vytvořit**.

    ![Podokno Uživatel](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k HubSpot, aby mohla používat azure jednotné přihlašování.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **HubSpot**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **HubSpot**.

    ![HubSpot v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. Potom v podokně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů **Brittu Simonovou.** Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **role Select** příslušnou roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-hubspot-test-user"></a>Vytvoření testovacího uživatele HubSpot

Chcete-li povolit Azure AD uživatele k přihlášení k HubSpot, musí být uživatel zřízen v HubSpot. V HubSpot zřizování je ruční úloha.

Zřízení uživatelského účtu v HubSpotu:

1. Přihlaste se k webu společnosti HubSpot jako správce.

1. V pravém horním rohu stránky vyberte ikonu **Nastavení.**

    ![Ikona Nastavení v HubSpotu](./media/hubspot-tutorial/config1.png)

1. Vyberte **uživatelé & týmy**.

    ![Možnost Uživatelé & týmy v HubSpotu](./media/hubspot-tutorial/user1.png)

1. Vyberte **Vytvořit uživatele**.

    ![Možnost Vytvořit uživatele v HubSpotu](./media/hubspot-tutorial/user2.png)

1. Do pole **Přidat e-mail y addess(es)** zadejte e-mailovou adresu uživatele ve formátu brittasimon\@contoso.com a pak vyberte **Další**.

    ![Pole Přidat e-mailové adresy v části Vytvořit uživatele v HubSpotu](./media/hubspot-tutorial/user3.png)

1. V části **Vytvořit uživatele** vyberte každou kartu. Na každé kartě nastavte příslušné možnosti a oprávnění pro uživatele. Potom vyberte **Další**.

    ![Karty v části Vytvořit uživatele v HubSpotu](./media/hubspot-tutorial/user4.png)

1. Chcete-li odeslat pozvánku uživateli, vyberte **možnost Odeslat**.

    ![Možnost Odeslat v HubSpotu](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Uživatel je aktivován poté, co uživatel přijme pozvání.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když po nastavení jednotného přihlašování vyberete **HubSpot** na portálu Moje aplikace, automaticky se k HubSpotu přihlásíte. Další informace o portálu Moje aplikace najdete [v tématu Přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v těchto článcích:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
