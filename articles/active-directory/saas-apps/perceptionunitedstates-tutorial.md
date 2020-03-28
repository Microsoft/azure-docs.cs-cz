---
title: 'Kurz: Integrace služby Azure Active Directory s aplikací Perception United States (Non-UltiPro) | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a vnímáním ve Spojených státech (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094839"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Kurz: Integrace služby Azure Active Directory s vnímáním Ve Spojených státech (Non-UltiPro)

V tomto kurzu se dozvíte, jak integrovat vnímání Spojené státy (Non-UltiPro) s Azure Active Directory (Azure AD).
Integrace vnímání Spojených států (Non-UltiPro) s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k vnímání Spojené státy (Non-UltiPro).
* Můžete povolit uživatelům, aby se automaticky přihlásili k vnímání Spojené státy (Non-UltiPro) (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s vnímáním Spojené státy (Non-UltiPro), budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Vnímání Spojené státy (Non-UltiPro) jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vnímání Spojené státy (Non-UltiPro) podporuje **IDP** inicioval Jednotné přihlašované

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Přidání vnímání Spojené státy (Non-UltiPro) z galerie

Chcete-li nakonfigurovat integraci vnímání Spojené státy (Non-UltiPro) do Azure AD, je třeba přidat vnímání Spojené státy (Non-UltiPro) z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat vnímání Spojené státy (Non-UltiPro) z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Vnímání Spojené státy (Non-UltiPro)**, z panelu výsledků vyberte **vnímání Spojených států (Non-UltiPro)** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Vnímání Spojené státy (Non-UltiPro) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace Perception United States (Non-UltiPro) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve vnímání Spojených států (Non-UltiPro).

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace Perception United States (Non-UltiPro), musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace vnímání Usa (Non-UltiPro) Jednotné přihlášení](#configure-perception-united-states-non-ultipro-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit vnímání Spojené státy (Non-UltiPro) testovací uživatel](#create-perception-united-states-non-ultipro-test-user)** – mít protějšek Britta Simon ve vnímání Spojených států (Non-UltiPro), který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí aplikace Perception United States (Non-UltiPro), proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Vnímání Spojených států (Non-UltiPro)** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Vnímání Usa (Non-UltiPro) Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`https://perception.kanjoya.com/sp`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. **Aplikace Perception United States (Non-UltiPro)** vyžaduje hodnotu **identifikátoru Azure AD** jako <entity_id>, kterou získáte z části **Nastavit vnímání Spojených států (Non-UltiPro),** která má být kódována. Chcete-li získat hodnotu kódované identifikátorem **http://www.url-encode-decode.com/** URI, použijte následující odkaz: .

    d. Po získání uri kódované hodnoty kombinovat s **URL odpovědět,** jak je uvedeno níže-

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Vložte výše uvedenou hodnotu do textového pole **Adresa URL odpovědi.**

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit vnímání Spojených států (Non-UltiPro)** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurace vnímání Spojené státy (Non-UltiPro) Jednotné přihlašování

1. V jiném okně prohlížeče se přihlaste na web společnosti Perception United States (Non-UltiPro) jako správce.

2. Na hlavním panelu nástrojů klepněte na **položku Nastavení účtu**.

    ![Vnímání Spojené státy (Non-UltiPro) uživatel](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na stránce **Nastavení účtu** proveďte následující kroky:

    ![Vnímání Spojené státy (Non-UltiPro) uživatel](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Do textového pole **Název společnosti** zadejte název **společnosti**.
    
    b. Do textového pole **Název účtu** zadejte název **účtu**.

    c. Do **výchozího textového pole E-mail s odpovědí** zadejte platný **e-mail**.

    d. Vyberte **zprostředkovatele identity stezu jako** **SAML 2.0**.

4. Na stránce **Konfigurace přizpůsobování vlastního zabezpečení** proveďte následující kroky:

    ![Vnímání Spojené státy (Non-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Vyberte **typ NÁZVU SAML** jako **e-mail**.

    b. Do textového pole **Název konfigurace služby SSO** zadejte název **konfigurace**.
    
    c. V textovém poli **Název zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z webu Azure Portal. 

    d. Do **textového pole Doména SAML**zadejte doménu jako @contoso.com.

    e. Kliknutím na **Znovu nahrát** nahrajete soubor **XML metadat.**

    f. Klikněte na **Aktualizovat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k vnímání Spojené státy (Non-UltiPro).

1. Na webu Azure Portal vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Perception United **States (Non-UltiPro).**

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Vnímání Spojených států (Non-UltiPro).**

    ![Odkaz Vnímání Spojených států (Non-UltiPro) v seznamu aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Vytvořit vnímání Spojené státy (Non-UltiPro) testovací uživatel

V této části vytvoříte uživatele s názvem Britta Simon ve vnímání Spojené státy (Non-UltiPro). Práce s [vnímání Spojené státy (Non-UltiPro) tým podpory](https://www.ultimatesoftware.com/Contact/ContactUs) přidat uživatele v vnímání Spojené státy (Non-UltiPro) platformy.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici vnímání Spojené státy (Non-UltiPro) na přístupovém panelu, měli byste být automaticky přihlášeni k vnímání Spojené státy (Non UltiPro), pro které nastavíte jednotné přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

