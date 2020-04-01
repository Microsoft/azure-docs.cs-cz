---
title: 'Kurz: Integrace služby Azure Active Directory s Infinite Campus | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100356"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Kurz: Integrace služby Azure Active Directory s Infinite Campus

V tomto kurzu se dozvíte, jak integrovat Infinite Campus s Azure Active Directory (Azure AD).
Integrace Infinite Campus s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Infinite Campus.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Infinite Campus (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Infinite Campus, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením infinite campus
* Minimálně musíte být správcem služby Azure Active Directory a k dokončení konfigurace mít roli zabezpečení produktu campusu "Studentský informační systém (SIS)".

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Infinite Campus podporuje **sp** inicioval sso

## <a name="adding-infinite-campus-from-the-gallery"></a>Přidání Infinite Campus z galerie

Chcete-li nakonfigurovat integraci Infinite Campus do Azure AD, musíte přidat Infinite Campus z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Infinite Campus z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Infinite Campus**, z panelu výsledků vyberte **Infinite Campus** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Infinite Campus v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Infinite Campus na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Infinite Campus.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Infinite Campus, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace nekonečného jednotného přihlášení](#configure-infinite-campus-single-sign-on)** do areálu - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Infinite Campus](#create-infinite-campus-test-user)** – chcete-li mít protějšek Britta Simon v Infinite Campus, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí infinite campusu, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Infinite Campus** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části Základní konfigurace SAML proveďte následující kroky (všimněte si, že doména se bude lišit podle hostitelského modelu, ale hodnota **plně kvalifikované domény** musí odpovídat instalaci infinite campusu):

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL pomocí následujícího vzoru:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nekonečné Campus domény a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurace jednotného přihlášení do nekonečného kampusu

1. V jiném okně webového prohlížeče se přihlaste do Infinite Campus jako správce zabezpečení.

2. V levé části nabídky klepněte na **položku Správa systému**.

    ![Správce](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Přejděte na**konfiguraci zprostředkovatele služeb****správu SAML služby** >  **zabezpečení uživatele** > .

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na stránce **Konfigurace poskytovatele služeb s přistaň proveďte** následující kroky:

    ![Sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Vyberte **možnost Povolit jednotné přihlašování SAML**.

    b. Úprava **názvu volitelného atributu** tak, aby obsahoval **název**

    c. V části **Vybrat možnost pro načtení dat serveru Zprostředkovatel identity (IDP)** vyberte **URL metadata**, vložte hodnotu **URL metadat federace aplikace,** kterou jste zkopírovali z portálu Azure do pole, a klikněte na **Synchronizovat**.

    d. Po kliknutí na **tlačítko Synchronizovat** se hodnoty automaticky naplní na stránce **Konfigurace poskytovatele služeb při způsobovaném řízení.** Tyto hodnoty lze ověřit tak, aby odpovídaly hodnotám uvedeným v kroku 4 výše.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například, BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

> [!NOTE]
> Pokud chcete, aby všichni vaši uživatelé Azure měli přístup k infinite campusu a spoléhali na systém interních oprávnění Infinite Campus pro řízení přístupu, můžete nastavit vlastnost **Přiřazení uživatele požadované** aplikace na Ne a přeskočit následující kroky.

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Infinite Campus.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Infinite **Campus**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Infinite Campus**.

    ![Nekonečný odkaz campus v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-infinite-campus-test-user"></a>Vytvořit nekonečné Campus test uživatele

Infinite Campus má demograficky zaměřenou architekturu. Obraťte se na [tým podpory Infinite Campus](mailto:sales@infinitecampus.com) a přidejte uživatele na platformě Infinite Campus.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Infinite Campus na přístupovém panelu, měli byste být automaticky přihlášeni k infinite campus, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
