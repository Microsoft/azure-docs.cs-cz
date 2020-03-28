---
title: 'Kurz: Integrace služby Azure Active Directory se launchdarkly | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3f0671bc-f93f-496e-b465-b9ce8c6633fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f8c3ea300960893163264e99bd6c51138c7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159656"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Kurz: Integrace Služby Azure Active Directory se launchdarkly

V tomto kurzu se dozvíte, jak integrovat LaunchDarkly s Azure Active Directory (Azure AD).
Integrace LaunchDarkly s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LaunchDarkly.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě LaunchDarkly (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s LaunchDarkly, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením LaunchDarkly

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LaunchDarkly podporuje **SP a IDP** inicioval a so
* LaunchDarkly podporuje zřizování uživatelů **Just In Time**

## <a name="adding-launchdarkly-from-the-gallery"></a>Přidání LaunchDarkly z galerie

Chcete-li nakonfigurovat integraci LaunchDarkly do Azure AD, musíte přidat LaunchDarkly z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat LaunchDarkly z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LaunchDarkly**, z panelu výsledků vyberte **LaunchDarkly** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![LaunchDarkly v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s [Název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v [Název aplikace].

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [Název aplikace], musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace launchdarkly jednotného přihlášení](#configure-launchdarkly-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit LaunchDarkly testovací ho uživatele](#create-launchdarkly-test-user)** – mít protějšek Britta Simon v LaunchDarkly, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlášení služby Azure AD pomocí [Název aplikace], proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce Integrace aplikací **LaunchDarkly** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![LaunchDarkly Informace o doméně a adresách URL jednotného přihlášení](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`app.launchdarkly.com`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není skutečná. Hodnotu aktualizujete pomocí skutečné adresy URL odpovědi, která je vysvětlena dále v kurzu. Pokud máte v úmyslu používat aplikaci v režimu **IDP,** musíte ponechat pole **Přihlásit se na URL** prázdné, jinak nebudete moci iniciovat přihlášení z **IDP**. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://app.launchdarkly.com`

    ![LaunchDarkly Informace o doméně a adresách URL jednotného přihlášení](common/metadata-upload-additional-signon.png)

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit launchdarkly** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-launchdarkly-single-sign-on"></a>Konfigurace spouštěnízcela jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste na web společnosti LaunchDarkly jako správce.

2. Na levém navigačním panelu vyberte **Nastavení účtu.**

    ![LaunchDarkly Konfigurace](./media/launchdarkly-tutorial/configure1.png)

3. Klikněte na kartu **Zabezpečení.**

    ![LaunchDarkly Konfigurace](./media/launchdarkly-tutorial/configure2.png)

4. Klepněte na tlačítko **POVOLIT přisostžení a** **upravte konfiguraci saml**.

    ![LaunchDarkly Konfigurace](./media/launchdarkly-tutorial/configure3.png)

5. V části **Úprava konfigurace SAML** proveďte následující kroky:

    ![LaunchDarkly Konfigurace](./media/launchdarkly-tutorial/configure4.png)

    a. Zkopírujte **adresu URL služby příjemce SAML** pro vaši instanci a vložte ji do textového pole Url odpovědi v části **LaunchDarkly Domain and URLs** na webu Azure Portal.

    b. Do textového pole **přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z portálu Azure.

    c. Otevřete stažený certifikát z portálu Azure do poznámkového bloku, zkopírujte obsah a vložte ho do pole **s certifikátem X.509,** nebo můžete certifikát nahrát přímo kliknutím na **nahrát jeden**.

    d. Klikněte na **Uložit.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu launchdarkly.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **LaunchDarkly**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LaunchDarkly**.

    ![Odkaz LaunchDarkly v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-launchdarkly-test-user"></a>Vytvořit launchdarkly testovacího uživatele

Cílem této části je vytvořit uživatele s názvem Britta Simon v LaunchDarkly. LaunchDarkly podporuje zřizování za chvíli, což je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Nový uživatel je vytvořen během pokusu o přístup LaunchDarkly, pokud ještě neexistuje.

> [!Note]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se [na tým podpory klienta LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici LaunchDarkly na přístupovém panelu, měli byste být automaticky přihlášeni k LaunchDarkly, pro které nastavíte přistupující zařízení. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
