---
title: 'Kurz: Integrace služby Azure Active Directory se stránkovou | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a stránkovnou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68227468"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Kurz: Integrace služby Azure Active Directory se stránkovou

V tomto kurzu se dozvíte, jak integrovat Stránkované názvy s Azure Active Directory (Azure AD).

Integrace stránkované namísto s Azure AD vám poskytuje následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup k PageDNA.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k pagedna (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Podrobnosti o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s stránkovou, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné PageDNA s povoleným jedním přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete stránkovna s Azure AD.

PageDNA podporuje následující funkce:

* Sp-inicioval jednotné přihlašování (SSO).

* Zřizování uživatelů just-in-time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Přidání stránkované na webu Azure Marketplace

Pokud chcete nakonfigurovat integraci PageDNA do Azure AD, musíte přidat Stránkonu z Azure Marketplace do seznamu spravovaných aplikací SaaS:

1. Přihlaste se k [portálu Azure](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Přejděte na **položku Podnikové aplikace**a vyberte **možnost Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna **možnost + Nová aplikace.**

    ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Stránkovatna**. Ve výsledcích hledání vyberte **PageDNA**a pak vyberte **Přidat,** chcete-li přidat aplikaci.

    ![PageDNA v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí stránkované na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v pagedna.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí stránkované natu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby uživatelé mohli tuto funkci používat.
1. **[Nakonfigurujte jednotné přihlašování PageDNA](#configure-pagedna-single-sign-on)** tak, aby bylo nakonfigurováno nastavení jednotného přihlášení na straně aplikace.
1. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD s Brittou Simonovou.
1. **[Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user)** aby britta Simon mohla používat jednotné přihlašování Azure AD.
1. **[Vytvořte uživatele testu PageDNA](#create-a-pagedna-test-user)** tak, aby v stránce PageDNA byl uživatel jménem Britta Simon, který je propojený s uživatelem Azure AD s názvem Britta Simon.
1. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí stránkované natu, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikace **PageDNA** **možnost Jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), abyste otevřeli podokno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **Základní konfigurace SAML** proveďte následující kroky:

    ![PageDNA domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    1. Do pole **Přihlásit se na adresu URL** zadejte adresu URL pomocí jednoho z následujících vzorů:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Do pole **Identifikátor (ID entity)** zadejte adresu URL pomocí jednoho z následujících vzorů:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory PageDNA](mailto:success@pagedna.com). Můžete také odkazovat na vzory zobrazené v **podokně Základní konfigurace SAML** na webu Azure Portal.

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **vyberte Stáhnout,** chcete-li stáhnout **certifikát (Raw)** z daných možností a uložit jej do počítače.

    ![Možnost stažení certifikátu (Nezpracovaný)](common/certificateraw.png)

1. V části **Nastavit stránku DNA** zkopírujte adresu URL nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor azure reklamy**
   * **Adresa URL odhlášení**

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurace jednotného přihlašování PageDNA

Chcete-li nakonfigurovat jednotné přihlašování na straně PageDNA, odešlete stažený certifikát (Raw) a příslušné zkopírované adresy URL z portálu Azure týmu [podpory PageDNA](mailto:success@pagedna.com). Tým PageDNA zajistí, že připojení samolo přihlašování k elektrické síti je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure portal s názvem Britta Simon.

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

V této části povolíte uživateli Britta Simon používat Azure jednotné přihlášení udělením přístupu uživatele PageDNA.

1. Na webu Azure Portal vyberte **Podnikové aplikace** > **Všechny aplikace** > **PageDNA**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Možnost PageDNA**.

    ![PageDNA v seznamu aplikací](common/all-applications.png)

1. V levém podokně vyberte v části **MANAGE** **položku Uživatelé a skupiny**.

    ![Možnost "Uživatelé a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně Přidat **přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte **britta Simon** v seznamu **Uživatelé** a pak v dolní části podokna zvolte **Vybrat.**

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. V dolní části podokna zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-pagedna-test-user"></a>Vytvoření testovacího uživatele PageDNA

Uživatel s názvem Britta Simon je nyní vytvořen v PageDNA. Nemusíte dělat nic k vytvoření tohoto uživatele. PageDNA podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. Pokud uživatel s názvem Britta Simon ještě neexistuje v PageDNA, nový je vytvořen po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když na portálu Moje aplikace vyberete **Stránkovatu,** měli byste být automaticky přihlášeni k předplatnému PageDNA, pro které nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

