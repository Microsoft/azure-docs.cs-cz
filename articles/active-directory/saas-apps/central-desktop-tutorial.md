---
title: 'Kurz: Integrace služby Azure Active Directory s central desktopem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a central desktopem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda8e928b530001faeae34c364dfed91d7620f0a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157513"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Kurz: Integrace Azure Active Directory s centrální plochou

V tomto kurzu se dozvíte, jak integrovat central desktop s Azure Active Directory (Azure AD).
Integrace central desktopu s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Central Desktop.
* Uživatelům můžete povolit automatické přihlášení k Centrální ploše (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s central desktopem, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením central desktopu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Central Desktop podporuje **sp** inicioval sonda

## <a name="adding-central-desktop-from-the-gallery"></a>Přidání centrální plochy z galerie

Chcete-li nakonfigurovat integraci central desktopu do Azure AD, musíte přidat Central Desktop z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat centrální plochu z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Central Desktop**, z panelu výsledků vyberte **Central Desktop** a pak kliknutím na tlačítko **Přidat** aplikaci přidejte.

     ![Centrální plocha v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí central desktopu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v centrální ploše.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí central desktopu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování na centrální ploše](#configure-central-desktop-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte centrální desktop test uživatele](#create-central-desktop-test-user)** – mít protějšek Britta Simon v centrální desktop, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí central desktopu, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Central Desktop** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Centrální domény stolního počítače a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.centraldesktop.com`

    b. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta centrální plochy.](https://imeetcentral.com/contact-us) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Nezpracovaný)** z daných možností a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V části **Nastavit centrální plochu** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-central-desktop-single-sign-on"></a>Konfigurace jednotného přihlašování k centrální ploše

1. Přihlaste se ke svému **tenantovi centrální plochy.**

2. Přejděte do **nastavení**. Vyberte **Upřesnit**a pak vyberte **Jednotné přihlášení**.

    ![Nastavení – upřesnit](./media/central-desktop-tutorial/ic769563.png "Nastavení – upřesnit")

3. Na stránce **Nastavení jednotného přihlášení** postupujte takto:

    ![Nastavení jednotného přihlašování](./media/central-desktop-tutorial/ic769564.png "Nastavení jednotného přihlášení")

    a. Vyberte **možnost Povolit jednotné přihlašování SAML v2**.

    b. Do pole **Adresa URL služby Přibránte** v ní vložte hodnotu **identifikátoru Azure Ad,** kterou jste zkopírovali z webu Azure Portal.

    c. Do pole **Adresa URL přihlášení při přihlašovacím** čísle vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z portálu Azure.

    d. Do pole **Adresa URL odhlášení při správě,** vložte hodnotu **url odhlášení,** kterou jste zkopírovali z portálu Azure.

4. V části **Metoda ověření podpisu zprávy** postupujte takto:

    ![Metoda ověření podpisu zprávy](./media/central-desktop-tutorial/ic769565.png "Metoda ověření podpisu zprávy")
    
    a. Vyberte **Certifikát**.

    b. V seznamu **certifikátů spřimhou** šeř vyberte **Možnost RSH SHA256**.

    c. Otevřete stažený certifikát v poznámkovém bloku. Pak zkopírujte obsah certifikátu a vložte jej do pole **Certifikát s vlastní službou.**

    d. Vyberte **Možnost Zobrazit odkaz na přihlašovací stránku SAMLv2**.

    e. Vyberte **Aktualizovat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k central desktop.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Centrální **plocha**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Central Desktop**.

    ![Odkaz Centrální plocha v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-central-desktop-test-user"></a>Vytvořit uživatele testu centrální plochy

Aby se uživatelé Azure AD mohli přihlásit, musí být zřízeni v aplikaci Central Desktop. Tato část popisuje, jak vytvořit uživatelské účty Azure AD v centrální ploše.

> [!NOTE]
> Chcete-li zřídit uživatelské účty Azure AD, můžete použít všechny ostatní nástroje pro vytváření uživatelských účtů central desktop nebo rozhraní API, které jsou poskytovány Central Desktop.

**Zřízení uživatelských účtů na centrální plochu:**

1. Přihlaste se ke svému tenantovi centrální plochy.

2. Vyberte **Lidé** a pak **vyberte Přidat interní členy**.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. Do pole **E-mailová adresa nových členů** zadejte účet Azure AD, který chcete zřídit, a pak vyberte **Další**.

    ![E-mailové adresy nových členů](./media/central-desktop-tutorial/ic781052.png "E-mailové adresy nových členů")

4. Vyberte **Přidat interní členy .**

    ![Přidat interní člen](./media/central-desktop-tutorial/ic781053.png "Přidat interní člen")
  
   > [!NOTE]
   > Uživatelé, které přidáte, obdrží e-mail, který obsahuje potvrzovací odkaz pro aktivaci svých účtů.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici centrální plocha na přístupovém panelu, můžete by měl být automaticky přihlášeni k centrální ploše, pro které nastavíte přistupující připojení. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
