---
title: 'Kurz: Integrace služby Azure Active Directory s Kontiki | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098490"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Kurz: Integrace Služby Azure Active Directory s Kontiki

V tomto kurzu se dozvíte, jak integrovat Kontiki s Azure Active Directory (Azure AD).

Integrace Kontiki s Azure AD vám poskytuje následující výhody:

* Azure AD můžete použít k řízení, kdo má přístup k Kontiki.
* Uživatelé mohou být automaticky přihlášeni k Kontiki pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, na webu Azure Portal.

Další informace o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Kontiki, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure AD, vytvořte [si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné Kontiki s povoleným jedním přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete Kontiki s Azure AD.

Kontiki podporuje následující funkce:

* **Jednotné přihlašování iniciované sp**
* **Zřizování uživatelů just-in-time**

## <a name="add-kontiki-in-the-azure-portal"></a>Přidání Kontiki na portál Azure

Pokud chcete kontiki integrovat do Azure AD, musíte kontiki přidat do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levé nabídce vyberte **Azure Active Directory**.

    ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **možnost Nová aplikace**.

    ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Kontiki**. Ve výsledcích hledání vyberte **Kontiki**a pak vyberte **Přidat**.

    ![Kontiki v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Kontiki na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem Služby Azure AD a souvisejícím uživatelem v Kontiki.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí kontiki, musíte dokončit následující stavební bloky:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlášení Kontiki](#configure-kontiki-single-sign-on)** | Konfiguruje nastavení jednotného přihlášení v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotné přihlašování. |
| **[Vytvoření testovacího uživatele Kontiki](#create-a-kontiki-test-user)** | Vytvoří protějšek Britta Simon v Kontiki, který je propojen s reprezentací Azure AD uživatele. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověří, zda konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí Kontiki na webu Azure Portal.

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte v podokně integrace aplikací **Kontiki** **možnost Jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML** nebo **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), chcete-li otevřít podokno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **Základní konfigurace SAML** zadejte do textového pole **Přihlásit se na adresu URL** adresu URL, která má následující vzorec:`https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki Doména a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    > [!NOTE]
    > Obraťte se na [tým podpory klienta Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) a získejte správnou hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **položku Stáhnout** vedle xml metadat **federace**. Vyberte možnost stahování na základě vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu XML metadat federace](common/metadataxml.png)

1. V části **Nastavit Kontiki** zkopírujte na základě vašich požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor azure reklamy
    * Adresa URL odhlášení

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurace jednotného přihlášení Kontiki

Chcete-li nakonfigurovat jednotné přihlašování na straně Kontiki, odešlete stažený soubor XML metadat federace a příslušné adresy URL, které jste zkopírovali z portálu Azure, [týmu podpory Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Tým podpory Kontiki používá informace, které jim odešlete, aby zajistil, že jednotné přihlašovací připojení SAML je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovací ho uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte**Možnost Uživatelé Služby** >  **Azure Active Directory** > **Všichni uživatelé**.

    ![Možnosti Uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **Uživatel** proveďte následující kroky:

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **\@\<brittasimon>\< domény vaší společnosti. prodloužení>**. Například **brittasimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo.** Poznamenejte si hodnotu, která je zobrazena v poli **Heslo.**

    1. Vyberte **Vytvořit**.

    ![Podokno Uživatel](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k Kontiki tak, aby mohla používat Azure jednotné přihlašování.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **Kontiki**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Kontiki**.

    ![Kontiki v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. Potom v podokně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů **Brittu Simonovou.** Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **role Select** příslušnou roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-kontiki-test-user"></a>Vytvoření testovacího uživatele Kontiki

Neexistuje žádná položka akce pro konfiguraci zřizování uživatelů v Kontiki. Když se přiřazený uživatel pokusí přihlásit k Kontiki pomocí portálu Moje aplikace, Kontiki zkontroluje, zda uživatel existuje. Pokud není nalezen žádný uživatelský účet, Kontiki automaticky vytvoří uživatelský účet.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když po nastavení jednotného přihlášení vyberete **kontiki** na portálu Moje aplikace, budete automaticky přihlášeni k Kontiki. Další informace o portálu Moje aplikace najdete [v tématu Přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v těchto článcích:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
