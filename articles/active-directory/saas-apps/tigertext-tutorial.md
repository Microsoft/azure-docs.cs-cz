---
title: 'Kurz: Integrace služby Azure Active Directory pomocí programu TigerText Secure Messenger | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088660"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Kurz: Integrace Azure Active Directory s TigerText Secure Messenger

V tomto kurzu se dozvíte, jak integrovat TigerText Secure Messenger s Azure Active Directory (Azure AD).

Integrace TigerText Secure Messenger s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TigerText Secure Messenger.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě TigerText Secure Messenger (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Podrobnosti o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí TigerText Secure Messenger, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné TigerText Secure Messenger s povoleným jednorázovým přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete TigerText Secure Messenger s Azure AD.

TigerText Secure Messenger podporuje jednotné přihlašování iniciované sp(SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Přidání zabezpečeného messengeru TigerText z Azure Marketplace

Pokud chcete nakonfigurovat integraci TigerTextu Secure Messenger do Azure AD, je potřeba přidat TigerText Secure Messenger z Azure Marketplace do seznamu spravovaných aplikací SaaS:

1. Přihlaste se k [portálu Azure](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Přejděte na **položku Podnikové aplikace**a vyberte **možnost Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna **možnost + Nová aplikace.**

    ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **TigerText Secure Messenger**. Ve výsledcích hledání vyberte **TigerText Secure Messenger**a pak vyberte **Přidat,** chcete-li přidat aplikaci.

    ![TigerText Secure Messenger v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí tigertextu Zabezpečené messenger na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem Azure AD a souvisejícím uživatelem v TigerText Secure Messenger.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí tigertextu Secure Messenger, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby uživatelé mohli tuto funkci používat.
1. **[Nakonfigurujte tigertext secure messenger jednotné přihlašování](#configure-tigertext-secure-messenger-single-sign-on)** a nakonfigurujte nastavení jednotného přihlášení na straně aplikace.
1. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD s Brittou Simonovou.
1. **[Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user)** aby britta Simon mohla používat jednotné přihlašování Azure AD.
1. **[Vytvořte testovacího uživatele TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** tak, aby v TigerTextu Zabezpečené messengeru byl uživatel jménem Britta Simon, který je propojený s uživatelem Azure AD jménem Britta Simon.
1. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí TigerText Secure Messenger, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **TigerText Secure Messenger** vyberte Jedno **přihlášení**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), abyste otevřeli podokno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **Základní konfigurace SAML** proveďte následující kroky:

    ![TigerText Secure Messenger domény a adresy URL jednotného přihlášení informace](common/sp-identifier.png)

    1. Do pole **Přihlásit se na adresu URL** zadejte adresu URL:

       `https://home.tigertext.com`

    1. Do pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Hodnota **Identifikátor (ID entity)** není skutečná. Aktualizujte tuto hodnotu skutečným identifikátorem. Chcete-li získat hodnotu, obraťte se na [tým podpory TigerText Secure Messenger](mailto:prosupport@tigertext.com). Můžete také odkazovat na vzory zobrazené v **podokně Základní konfigurace SAML** na webu Azure Portal.

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **položku Stáhnout,** chcete-li z daných možností stáhnout **xml metadat federace** a uložit jej do počítače.

    ![Možnost stažení XML metadat federace](common/metadataxml.png)

1. V části **Nastavit TigerText Secure Messenger** zkopírujte url nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor azure reklamy**
   * **Adresa URL odhlášení**

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurace jednotného přihlášení programu TigerText Secure Messenger

Chcete-li nakonfigurovat jednotné přihlašování na straně TigerText Secure Messenger, musíte odeslat stažený xml metadat federace a příslušné zkopírované adresy URL z portálu Azure týmu [podpory TigerText Secure Messenger](mailto:prosupport@tigertext.com). Tým TigerText Secure Messenger zajistí, že připojení spřitom správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovací ho uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal v levém podokně vyberte možnost**Uživatelé služby** >  **Azure Active Directory**   > **Všichni uživatelé**.

    ![Možnosti Uživatelé a Všichni uživatelé](common/users.png)

1. V horní části obrazovky vyberte **+ Nový uživatel**.

    ![Nová možnost uživatele](common/new-user.png)

1. V podokně **Uživatel** postupujte takto:

    ![Podokno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **\@\<BrittaSimon\< vaší firemní domény>. prodloužení>**. Například **BrittaSimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení tím, že jim přístup k TigerText Secure Messenger.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **TigerText Secure Messenger**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **TigerText Secure Messenger**.

    ![TigerText Secure Messenger v seznamu aplikací](common/all-applications.png)

1. V levém podokně vyberte v části **MANAGE** **položku Uživatelé a skupiny**.

    ![Možnost "Uživatelé a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně Přidat **přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte **britta Simon** v seznamu **Uživatelé** a pak v dolní části podokna zvolte **Vybrat.**

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. V dolní části podokna zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Vytvoření testovacího uživatele TigerText Secure Messenger

V této části vytvoříte uživatele s názvem Britta Simon v TigerText Secure Messenger. Spolupracujte s [týmem podpory TigerText Secure Messenger](mailto:prosupport@tigertext.com) a přidejte Brittu Simon jako uživatele v TigerText Secure Messenger. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když na portálu Moje aplikace vyberete **TigerText Secure Messenger,** měli byste být automaticky přihlášeni k předplatnému TigerText Secure Messenger, pro které nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
