---
title: 'Kurz: Integrace služby Azure Active Directory se Správcem certifikátů Sectigo | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Správcem certifikátů Sectigo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67588241"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Kurz: Integrace služby Azure Active Directory se Správcem certifikátů Sectigo

V tomto kurzu se dozvíte, jak integrovat Správce certifikátů Sectigo s Azure Active Directory (Azure AD).

Integrace Správce certifikátů Sectigo s Azure AD vám přináší následující výhody:

* Azure AD můžete použít k řízení, kdo má přístup k Sectigo Certificate Manager.
* Uživatelé mohou být automaticky přihlášeni ke Správci certifikátů Sectigo pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, na webu Azure Portal.

Další informace o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Správce certifikátů Sectigo, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure AD, vytvořte [si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné Správce certifikátů Sectigo s povoleným jedním přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete Správce certifikátů Sectigo s Azure AD.

Sectigo Certificate Manager podporuje následující funkce:

* **Jednotné přihlašování iniciované sp**
* **Jednotné přihlašování iniciované idicí IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Přidání Správce certifikátů Sectigo na portál Azure

Chcete-li integrovat Správce certifikátů Sectigo s Azure AD, musíte přidat Správce certifikátů Sectigo do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levé nabídce vyberte **Azure Active Directory**.

    ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **možnost Nová aplikace**.

    ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Správce certifikátů Sectigo**. Ve výsledcích hledání vyberte **Správce certifikátů Sectigo**a pak vyberte **Přidat**.

    ![Správce certifikátů Sectigo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem Služby Azure AD a souvisejícím uživatelem ve Správci certifikátů Sectigo.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo, musíte dokončit následující stavební bloky:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlášení Správce certifikátů Sectigo](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguruje nastavení jednotného přihlášení v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotné přihlašování. |
| **[Vytvoření testovacího uživatele Správce certifikátů Sectigo](#create-a-sectigo-certificate-manager-test-user)** | Vytvoří protějšek Britta Simon v Sectigo Certificate Manager, který je propojen s reprezentací Azure AD uživatele. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověří, zda konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo na webu Azure Portal.

1. Na [portálu Azure](https://portal.azure.com/)vyberte v podokně integrace aplikací **Sectigo Certificate Manager** možnost Jednotné **přihlašování**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML** nebo **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), chcete-li otevřít podokno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Chcete-li nakonfigurovat *režim iniciátoru IDP,* proveďte v **podokně Základní konfigurace SAML** následující kroky:

    1. Do pole **Identifikátor** zadejte jednu z těchto adres URL:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Do pole **Adresa URL pro odpověď** zadejte jednu z těchto adres URL:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Vyberte **Nastavit další adresy URL**.

    1. Do pole **Stav přenosu** zadejte jednu z těchto adres URL:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Doména Správce certifikátů Sectigo a adresy URL jednotné přihlašovací informace](common/idp-relay.png)

1.  Chcete-li aplikaci nakonfigurovat v *režimu iniciovaném službou SP*, proveďte následující kroky:

    * Do pole **Přihlásit se na adresu URL** zadejte jednu z těchto adres URL:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Doména Správce certifikátů Sectigo a adresy URL jednotné přihlašovací informace](common/both-signonurl.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **položku Stáhnout** vedle **certifikátu (Base64).** Vyberte možnost stahování na základě vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu (Base64)](common/certificatebase64.png)

1. V části **Nastavit Správce certifikátů Sectigo** zkopírujte na základě vašich požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor azure reklamy
    * Adresa URL odhlášení

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurace jednotného přihlášení Správce certifikátů Sectigo

Chcete-li nakonfigurovat jednotné přihlašování na straně Správce certifikátů Sectigo, odešlete soubor staženého certifikátu (Base64) a příslušné adresy URL, které jste zkopírovali z portálu Azure, [týmu podpory Správce certifikátů Sectigo](https://sectigo.com/support). Tým podpory Správce certifikátů Sectigo používá informace, které jim odešlete, k zajištění správného nastavení jednotného přihlašovacího připojení SAML na obou stranách.

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

V této části udělíte Britta Simon přístup k Sectigo Certificate Manager, aby mohla používat Azure jednotné přihlášení.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **Sectigo Certificate Manager**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Sectigo Certificate Manager**.

    ![Správce certifikátů Sectigo v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. Potom v podokně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů **Brittu Simonovou.** Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **role Select** příslušnou roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Vytvoření testovacího uživatele Správce certifikátů Sectigo

V této části vytvoříte uživatele s názvem Britta Simon v Sectigo Certificate Manager. Spolupracujte s [týmem podpory Správce certifikátů Sectigo](https://sectigo.com/support) a přidejte uživatele do platformy Sectigo Certificate Manager. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když po nastavení jednotného přihlášení vyberete správce **certifikátů Sectigo** na portálu Moje aplikace, budete automaticky přihlášeni ke Správci certifikátů Sectigo. Další informace o portálu Moje aplikace najdete [v tématu Přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v těchto článcích:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


