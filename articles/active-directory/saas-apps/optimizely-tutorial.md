---
title: 'Kurz: Integrace služby Azure Active Directory s optimalizátorem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Optimalizátorně.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943956"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Kurz: Integrace Služby Azure Active Directory s optimalizátorem

V tomto kurzu se dozvíte, jak optimalizovat s Azure Active Directory (Azure AD).
Integrace optimalizujte s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Optimizely.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k optimalizujte (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s optimalizujte, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Optimalizmativně jednopřihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Optimizely podporuje **sp** inicioval SSO

## <a name="adding-optimizely-from-the-gallery"></a>Přidání optimalizum z galerie

Chcete-li nakonfigurovat integraci Optimalizujte do Azure AD, musíte přidat Optimizely z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat optimalizu z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Optimalizma**, z panelu výsledků vyberte **Optimalizovat** a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![Optimalizace v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí optimalizu založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v optimalizu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD s optimalizátorem, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace optimalizmálně jednotného přihlašování](#configure-optimizely-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit optimalizujte testování uživatele](#create-optimizely-test-user)** – mít protějšek Britta Simon v Optimizely, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí technologie Optimizely, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce **Optimalizace** integrace aplikací vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Optimalizujte informace o jednotlivých přihlašování k doméně a adresám URL](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://app.optimizely.net/<instance name>`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Hodnotu aktualizujete skutečnou přihlašovací adresou URL a identifikátorem, což je vysvětleno dále v kurzu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Aplikace Optimizely očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. Kromě výše, Optimizely aplikace očekává několik dalších atributů, které mají být předány zpět v odpovědi SAML. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** přidejte atribut tokenu SAML následujícím postupem, jak je znázorněno v následující tabulce:

    | Name (Název) | Atribut zdroje |
    | ---------------| --------------- |
    | e-mail | user.mail |
    
    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit optimalizu** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-optimizely-single-sign-on"></a>Konfigurovat optimalizovaně jednotné přihlašování

1. Chcete-li nakonfigurovat jednotné přihlašování na straně **Optimizely,** obraťte se na správce účtu Optimizely a poskytněte stažený **certifikát (Base64)** a příslušné zkopírované adresy URL.

2. V reakci na váš e-mail, Optimizely poskytuje přihlašovací adresu URL (SP-inicioval SSO) a identifikátor (ID entity poskytovatele služeb) hodnoty.

    a. Zkopírujte **adresu URL služby SSO iniciované sp,** kterou poskytuje Optimizely, a vložte do textového pole **Přihlašovací adresa URL** v části Základní **konfigurace SAML** na webu Azure Portal.

    b. Zkopírujte **ID entity poskytovatele služeb** poskytované optimalizum a vložte do textového pole **Identifikátor** v části Základní **konfigurace SAML** na webu Azure Portal.

3. V jiném okně prohlížeče se přihlaste k aplikaci Optimizely.

4. V pravém horním rohu klikněte na název účtu a potom na **Nastavení účtu**.

    ![Jednotné přihlašování azure ad](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Na kartě Účet zaškrtněte políčko **Povolit jednotné přihlašování** v části Jednotné přihlášení v části **Přehled.**
  
    ![Jednotné přihlašování azure ad](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klikněte na **Uložit.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu Optimizely.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Optimalizovat**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **optimalizovat**.

    ![Odkaz Optimalizovat v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-optimizely-test-user"></a>Vytvořit optimalizovaný test uživatele

V této části vytvoříte uživatele s názvem Britta Simon v Optimizely.

1. Na domovské stránce vyberte **kartu Spolupracovníci.**

2. Chcete-li do projektu přidat nového **spolupracovníka,** klepněte na tlačítko Nový spolupracovník .
   
    ![Vytvoření testovacího uživatele Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Vyplňte e-mailovou adresu a přiřaďte jim roli. Klepněte na **tlačítko Pozvat**.

    ![Vytvoření testovacího uživatele Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Obdrží e-mailovou pozvánku. Pomocí e-mailové adresy se musí přihlásit do optimizely.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Optimizely na přístupovém panelu, měli byste být automaticky přihlášeni k Optimizely, pro které nastavíte přiřazovací ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

