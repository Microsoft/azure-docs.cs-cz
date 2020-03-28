---
title: 'Kurz: Integrace Azure Active Directory s Uberflipem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Uberflipem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048477"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Kurz: Integrace Azure Active Directory s Uberflipem

V tomto kurzu se dozvíte, jak integrovat Uberflip s Azure Active Directory (Azure AD).

Integrace Uberflipu s Azure AD vám přináší následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup k Uberflipu.
* Uživatelům můžete povolit automatické přihlášení k Uberflipu (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Podrobnosti o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s Uberflipem, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné Uberflip s povoleným jednorázovým přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

Uberflip podporuje následující funkce:

* Sp-inicioval a IDP inicioval jednotné přihlašování (SSO).
* Zřizování uživatelů just-in-time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Přidání Uberflipu z Azure Marketplace

Pokud chcete nakonfigurovat integraci Uberflipu do Azure AD, musíš uberflip přidat z Azure Marketplace do seznamu spravovaných aplikací SaaS:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém podokně vyberte **Azure Active Directory**.

   ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Přejděte na **položku Podnikové aplikace**a vyberte **možnost Všechny aplikace**.

   ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna **možnost + Nová aplikace.**

   ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Uberflip**. Ve výsledcích hledání vyberte **Uberflip**a pak vyberte **Přidat** a přidejte aplikaci.

   ![Uberflip v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Uberflipu na základě testovacího uživatele s názvem **B Simon**. Aby jednotné přihlašování fungovalo, musíte na Uberflipu vytvořit propojení mezi uživatelem Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Uberflipu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby uživatelé mohli tuto funkci používat.
1. **[Nakonfigurujte uberflip jednotné přihlašování](#configure-uberflip-single-sign-on)** a nakonfigurujte nastavení jednotného přihlášení na straně aplikace.
1. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
1. **[Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user)** aby umožnil B. Simonovi používat jednotné přihlašování Azure AD.
1. **[Vytvořte uživatele testu Uberflip](#create-an-uberflip-test-user)** tak, aby v Uberflipu byl uživatel jménem B. Simon, který je propojený s uživatelem Azure AD jménem B. Simon.
1. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Uberflipu, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Uberflip** vyberte **Jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), abyste otevřeli podokno Základní **konfigurace SAML.**

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **Základní konfigurace SAML** proveďte jeden z následujících kroků v závislosti na režimu automatického připojetí, který chcete konfigurovat:

   * Chcete-li aplikaci nakonfigurovat v režimu automatického přihlašování iniciovaném iniciátorem IDP, zadejte do pole **Adresa URL pro odpověď (Adresa URL služby Assertion Consumer Service)** adresu URL pomocí následujícího vzoru:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domény a adresy URL jednotné přihlašovací informace](common/both-replyurl.png)

     > [!NOTE]
     > Tato hodnota není skutečná. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Chcete-li získat skutečnou hodnotu, obraťte se na [tým podpory Uberflip](mailto:support@uberflip.com). Můžete také odkazovat na vzory zobrazené v **podokně Základní konfigurace SAML** na webu Azure Portal.

   * Chcete-li aplikaci nakonfigurovat v režimu služby SSO iniciovaného sp, vyberte **Nastavit další adresy URL**a do pole Přihlašovací adresa **URL** zadejte tuto adresu URL:

     `https://app.uberflip.com/users/login`

     ![Uberflip domény a adresy URL jednotné přihlašovací informace](common/both-signonurl.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **položku Stáhnout,** chcete-li z daných možností stáhnout **xml metadat federace** a uložit jej do počítače.

   ![Možnost stažení XML metadat federace](common/metadataxml.png)

1. V podokně **Nastavení Uberflipu** zkopírujte adresu URL nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor azure reklamy**
   * **Adresa URL odhlášení**

   ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurace jednotného přihlašování Uberflip

Chcete-li nakonfigurovat jednotné přihlašování na straně Uberflip, musíte odeslat stažený xml metadat federace a příslušné zkopírované adresy URL z portálu Azure [týmu podpory Uberflip](mailto:support@uberflip.com). Tým Uberflip zajistí, aby bylo připojení SAML SSO správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon na webu Azure Portal.

1. Na webu Azure Portal v levém podokně vyberte možnost**Uživatelé služby** >  **Azure Active Directory** > **Všichni uživatelé**.

    ![Možnosti Uživatelé a Všichni uživatelé](common/users.png)

1. V horní části obrazovky vyberte **+ Nový uživatel**.

    ![Nová možnost uživatele](common/new-user.png)

1. V podokně **Uživatel** postupujte takto:

    ![Podokno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **bSimon\@\<\< vašífiremní domény>. prodloužení>**. Například **BSimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat Azure jednotné přihlašování tím, že udělí te přístup k Uberflip.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **Uberflip**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Uberflip**.

    ![Uberflip v seznamu aplikací](common/all-applications.png)

1. V levém podokně vyberte v části **MANAGE** **položku Uživatelé a skupiny**.

    ![Možnost "Uživatelé a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně Přidat **přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** **B Simon** a v dolní části podokna zvolte **Vybrat.**

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. V dolní části podokna zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-an-uberflip-test-user"></a>Vytvoření testovacího uživatele Uberflip

Uživatel jménem B. Simon je nyní vytvořen v Uberflipu. Nemusíte dělat nic k vytvoření tohoto uživatele. Uberflip podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povolené. Pokud uživatel jménem B. Simon v Uberflipu ještě neexistuje, po ověření se vytvoří nový uživatel.

> [!NOTE]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se na [tým podpory Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když na portálu Moje aplikace vybereš **Uberflip,** měl byses automaticky přihlásit k předplatnému Uberflip, pro které jsi nastavil jednotné přihlašování. Další informace o portálu Moje aplikace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
