---
title: 'Kurz: Integrace služby Azure Active Directory s infogix data3Sixty govern | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Infogix Data3Sixty Govern.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a0b8dbb3992808bdcb94291d876192431c72f6c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100348"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Kurz: Integrace Služby Azure Active Directory s řízením Infogix Data3Sixty

V tomto kurzu se dozvíte, jak integrovat Infogix Data3Sixty řídit s Azure Active Directory (Azure AD).
Integrace Infogix Data3Sixty Govern s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Infogix Data3Sixty Govern.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Infogix Data3Sixty Govern (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí infogix data3Sixty govern, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Infogix Data3Sixty Řídí předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Infogix Data3Sixty Govern podporuje **sp a IDP** iniciované zabezpečení
* Infogix Data3Sixty Govern podporuje zřizování uživatelů **just in time**

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Přidání Infogix Data3Sixty Vládnout z galerie

Chcete-li nakonfigurovat integraci Infogix Data3Sixty Govern do Azure AD, musíte přidat Infogix Data3Sixty Govern z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Infogix Data3Sixty Govern z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Příkaz Infogix Data3Sixty Govern**, vyberte **infogix Data3Sixty Govern** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Infogix Data3Sixty Řídit v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí infogixdata3Sixty Govern na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Infogix Data3Sixty Govern.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí infogix data3Sixty govern, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Infogix Data3Sixty Řídit jednotné přihlašování](#configure-infogix-data3sixty-govern-single-sign-on)** - ke konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte Infogix Data3Sixty Řídit testovacího uživatele](#create-infogix-data3sixty-govern-test-user)** – mít protějšek Britta Simon v Infogix Data3Sixty Govern, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí funkce Infogix Data3Sixty Govern, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce **Infogix Data3Sixty Govern application** integration vyberte Single **sign-on**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Infogix Data3Sixty řídí informace o jednotném přihlášení doméně a adres URL](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`https://data3sixty.com/ui`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.data3sixty.com/sso/acs`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Infogix Data3Sixty řídí informace o jednotném přihlášení doméně a adres URL](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.data3sixty.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL pro odpověď a adresou URL pro přihlášení. Kontaktujte [Infogix Data3Sixty řídit tým podpory klienta](mailto:data3sixtysupport@infogix.com) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Infogix Data3Sixty Govern aplikace očekává, že saml kontrolní výrazy v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Hodnoty těchto atributů můžete spravovat v části **Atributy uživatele** na stránce integrace aplikace. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

7. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** upravte deklarace pomocí **ikony Upravit** nebo přidejte deklarace pomocí funkce Přidat **novou deklaraci** pro konfiguraci atributu tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name (Název) | Atribut zdroje|
    | -----------| -------------- |
    | Jméno  | user.givenname |
    | Lastname   | user.příjmení |
    | uživatelské jméno   | user.mail    |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

8. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Nezpracovaný)** z daných možností a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

9. V **části Nastavit Infogix Data3Sixty Govern** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-infogix-data3sixty-govern-single-sign-on"></a>Konfigurace funkce Infogix Data3Sixty Řídí jednotné přihlašování

Chcete-li nakonfigurovat jednotné přihlašování na straně **Infogix Data3Sixty Govern,** musíte odeslat stažený **certifikát (Raw)** a příslušné zkopírované adresy URL z portálu Azure do [týmu podpory Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Infogix Data3Sixty Govern.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Infogix Data3Sixty Govern**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Infogix Data3Sixty Govern**.

    ![Odkaz Infogix Data3Sixty Govern v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-infogix-data3sixty-govern-test-user"></a>Vytvořit infogix Data3Sixty Řídit testovacího uživatele

V této části je vytvořen uživatel s názvem Britta Simon v Infogix Data3Sixty Govern. Infogix Data3Sixty Govern podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v Infogix Data3Sixty Govern, nový je vytvořen po ověření.

> [!Note]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se [na infogix Data3Sixty řídí tým podpory](mailto:data3sixtysupport@infogix.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Infogix Data3Sixty Řídit na přístupovém panelu, měli byste být automaticky přihlášeni k Infogix Data3Sixty řídit, pro které nastavíte přistupující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

