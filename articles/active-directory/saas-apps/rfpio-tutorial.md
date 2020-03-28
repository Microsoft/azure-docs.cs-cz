---
title: 'Kurz: Integrace služby Azure Active Directory s RFPIO | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092847"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Kurz: Integrace Azure Active Directory s RFPIO

V tomto kurzu se dozvíte, jak integrovat RFPIO s Azure Active Directory (Azure AD).
Integrace RFPIO s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k RFPIO.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k RFPIO (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s RFPIO, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* RfPIO povolení jednotného přihlášení předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RFPIO podporuje **SP a IDP** inicioval sso

## <a name="adding-rfpio-from-the-gallery"></a>Přidání RFPIO z galerie

Chcete-li nakonfigurovat integraci RFPIO do Azure AD, je potřeba přidat RFPIO z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat rfpio z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **RFPIO**, z panelu výsledků vyberte **RFPIO** a klepnutím na **tlačítko Přidat** přidejte aplikaci.

    ![RFPIO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí rfpio na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v RFPIO.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí RFPIO, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednopřihlášení RFPIO](#configure-rfpio-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte rfpio test uživatele](#create-rfpio-test-user)** – mít protějšek Britta Simon v RFPIO, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí rfpio, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **RFPIO** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující krok:

    ![RfPIO Domény a adresy URL jednotné přihlašovací informace](common/idp-identifier.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://www.rfpio.com`

    b. Klepněte na **tlačítko Nastavit další adresy URL**.

    c. Do textového pole **Stav přenosu** zadejte hodnotu řetězce. Obraťte se na [tým podpory RFPIO](https://www.rfpio.com/contact/) získat tuto hodnotu.

    ![RfPIO Domény a adresy URL jednotné přihlašovací informace](common/idp-preintegrated-relay.png)

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.app.rfpio.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta RFPIO.](https://www.rfpio.com/contact/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit RFPIO** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-rfpio-single-sign-on"></a>Konfigurace jednotného přihlašování RFPIO

1. V jiném okně webového prohlížeče se přihlaste na web **RFPIO** jako správce.

1. Klikněte na rozbalovací soubor v levém dolním rohu.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app1.png)

1. Klikněte na **nastavení organizace**. 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app2.png)

1. Klikněte na **funkce & integrace**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app4.png)

1. V **konfiguraci služby SSO SAML** klepněte na **tlačítko Upravit**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app3.png)

1. V této části proveďte následující akce:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app5.png)
    
    a. Zkopírujte obsah **xml stažených metadat** a vložte jej do **pole konfigurace identity.**

    > [!NOTE]
    >Chcete-li zkopírovat obsah stažených **dat federace XML,** použijte **poznámkový blok++** nebo správný **editor XML**.

    b. Klikněte na **Validate** (Ověřit).

    c. Po klepnutí na **tlačítko Ověřit**, Překlopit **SAML(Povoleno)** na zapnuto.

    d. Klepněte na **tlačítko Odeslat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k RFPIO.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **RFPIO**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **rfpio**.

    ![Odkaz RFPIO v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-rfpio-test-user"></a>Vytvořit uživatele testu RFPIO

1. Přihlaste se k webu společnosti RFPIO jako správce.

1. Klikněte na rozbalovací soubor v levém dolním rohu.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app1.png)

1. Klikněte na **nastavení organizace**. 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app2.png)

1. Klepněte na **položku ČLENOVÉ TÝMU**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app6.png)

1. Klikněte na **PŘIDAT ČLENY**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app7.png)

1. V části **Přidat nové členy.** Proveďte následující akce:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app8.png)

    a. Zadejte **e-mailovou adresu** do pole **Zadat jeden e-mail za řádek.**

    b. Vyberte **roli** podle svých požadavků.

    c. Klepněte na tlačítko **PŘIDAT ČLENY**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a následuje odkaz pro potvrzení svého účtu před tím, než se stane aktivním.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici RFPIO na přístupovém panelu, můžete by měl být automaticky přihlášeni k RFPIO, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

