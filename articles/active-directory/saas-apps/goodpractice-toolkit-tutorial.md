---
title: 'Kurz: Azure Active Directory integrace s nástroji pro sady nástrojů pro zamyšlence | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory nástrojů a nástroji pro zobrazení názorů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 57598ea22874cfba772f6cf0e6b2ba779ef7c482
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92448363"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Kurz: Azure Active Directory integrace s nástroji pro sady nástrojů

V tomto kurzu se naučíte integrovat sadu nástrojů s nástrojem Azure Active Directory (Azure AD).

Pomocí této integrace můžete:

* Řízení ve službě Azure AD, která má přístup k nástrojům Toolkit Tools
* Umožněte uživatelům, aby se automaticky přihlásili k nástrojům Toolkit (jednotné přihlašování) s účty Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí nástrojů pro nástroje pro vydanou sadu nástrojů potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné nástrojů pro sady nástrojů pro myšlenky s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sada nástrojů pro vyžádané čtení podporuje jednotné přihlašování iniciované SP.
* Sada nástrojů pro vyhlašování uživatelů podporuje zřizování uživatelů za běhu.
* Po nakonfigurování sady nástrojů pro správu myšlenek můžete vyhovět řízení relace. Tento ovládací prvek chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Přidání nástrojů k nástrojům pro přidávání názorů z Galerie

Pokud chcete nakonfigurovat integraci nástrojů pro sadu nástrojů do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat sadu nástrojů pro přehledy z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte text sady **nástrojů** .
1. Ve výsledcích hledání vyberte možnost **Nástroje pro Tools Toolkit** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí nástrojů pro instrumentaci nástrojů pomocí nástroje Test User s názvem **B. Simon**. Aby se jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v sadě nástrojů pro sadu nástrojů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástrojů pro instrumentaci nástrojů, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Nakonfigurujte nástroj](#configure-mind-tools-toolkit-sso)** pro nastavení nástrojů pro jednotné přihlašování na straně aplikace.
    1. **[Vytvořte si testovacího uživatele sady nástrojů pro instrumentaci](#create-a-mind-tools-toolkit-test-user)** , aby měl protějšek B. Simon v sadě nástrojů sady nástrojů pro myšlenky. Tato strana je propojená se zastoupením uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části nakonfigurujete jednotné přihlašování služby Azure AD pomocí sady nástrojů pro nástroje pro vymyšlenost pomocí následujících kroků:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací **nástrojů pro nástroje pro nástroj** pro čtení vyberte **jednotné přihlašování**.

    ![Oddíl Manage s vybraným jednotným přihlašováním](common/select-sso.png)

1. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte **SAML/WS-** dopředné povolení jednotného přihlašování.

    ![Dialogové okno vybrat metodu jednotného přihlašování s zvýrazněním SAML](common/select-saml-option.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

    ![Stránka nastavit jeden Sign-On se stránkou SAML s ikonou tužky pro základní konfiguraci SAML zvýrazněnou](common/edit-urls.png)

1. V části **základní konfigurace SAML** v poli **přihlašovací adresa URL** zadejte adresu URL, která má vzor `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` .

    > [!NOTE]
    > Hodnota **přihlašovací adresy URL** není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat hodnotu, obraťte se na [tým podpory nástrojů](mailto:support@goodpractice.com) pro přístup k nástrojům.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML přejdete do části **podpisový certifikát SAML** . Napravo od **XML federačních metadat**vyberte **Stáhnout** a stáhněte text XML a uložte ho do svého počítače. Obsah XML závisí na možnostech, které vyberete.

    ![Část podpisový certifikát SAML se zvýrazněnou možností stáhnout vedle metadat federačního kódu XML](common/metadataxml.png)

1. V části **nastavení nástrojů pro nástroje pro Rozmyšlenost** zkopírujte podle potřeby následující adresy URL.

    * **Přihlašovací adresa URL**

    * **Identifikátor Azure AD**

    * **Odhlašovací adresa URL**

    ![Oddíl nastavení nástrojů pro sady nástrojů se zvýrazněnými adresami URL konfigurace zvýrazněné](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon v Azure Portal:

1. Na levé straně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte **B.Simon@**_doménaspolečnosti_**.** _přípona_. Například, B.Simon@contoso.com.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k nástrojům sady nástrojů pro myšlenky.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **nástroje sada nástrojů**.
1. Na stránce Přehled aplikace otevřete část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Oddíl Manage se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

   ![Okno Uživatelé a skupiny se zvýrazněnou možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-mind-tools-toolkit-sso"></a>Konfigurace nástroje pro nastavení nástrojů pro jednotné přihlašování

Chcete-li konfigurovat jednotné přihlašování na straně sady **nástrojů** pro dodávání dat, odešlete stažený text **XML federačních metadat** a dříve zkopírované adresy URL do sady nástrojů pro [podporu nástrojů pro nástroje](mailto:support@goodpractice.com)pro dodávání. Nakonfigurují toto nastavení tak, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Vytvoření sady nástrojů pro testování názorů

V této části vytvoříte uživatele s názvem B. Simon v nástroji sada nástrojů pro sady nástrojů.

Nástroj pro vyhlašování do sady nástrojů podporuje zřizování za běhu, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel ještě v sadě nástrojů pro přístup k sadě nástrojů neexistuje, vytvoří se nový, když se pokusíte o přístup k sadě nástrojů Tools Toolkit.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když na portálu moje aplikace vyberete dlaždici sady nástrojů pro dodávání, budete automaticky přihlášeni k sadě nástrojů pro panel nástrojů, pro kterou jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu [Úvod do portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si sadu nástrojů pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Postup ochrany nástrojů pro sadu nástrojů s pokročilou viditelností a ovládacími prvky](/cloud-app-security/proxy-intro-aad)