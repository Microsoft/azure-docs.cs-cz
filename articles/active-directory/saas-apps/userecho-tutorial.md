---
title: 'Kurz: Integrace služby Azure Active Directory s uživatelem UserEcho | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 59d61eda7002fe46cf99fac63822b2333b2d64b5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087769"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Kurz: Integrace služby Azure Active Directory s Uživatelem Echo

V tomto kurzu se dozvíte, jak integrovat UserEcho s Azure Active Directory (Azure AD).
Integrace UserEcho s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k UserEcho.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k UserEcho (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s UserEcho, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením UserEcho

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* UserEcho podporuje **sp** inicioval sso

## <a name="adding-userecho-from-the-gallery"></a>Přidání UserEcho z galerie

Chcete-li nakonfigurovat integraci UserEcho do Služby Azure AD, musíte přidat UserEcho z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat UserEcho z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **UserEcho**, z panelu výsledků vyberte **UserEcho** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![UserEcho v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí userecho na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v UserEcho.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí UserEcho, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace userecho jednotného přihlášení](#configure-userecho-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit UserEcho test uživatele](#create-userecho-test-user)** – mít protějšek Britta Simon v UserEcho, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure a disace pomocí Technologie UserEcho, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikace **UserEcho** **možnost Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![UserEcho Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.userecho.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta UserEcho.](https://feedback.userecho.com/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit UserEcho** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-userecho-single-sign-on"></a>Konfigurace jednotného přihlašování UserEcho

1. V jiném okně prohlížeče se přihlaste k webu společnosti UserEcho jako správce.

2. Na panelu nástrojů nahoře rozbalte nabídku klepnutím na uživatelské jméno a potom klepněte na **tlačítko Nastavit .**
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klepněte na **položku Integrace**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klepněte na **položku Web**a potom klepněte **na položku Jednotné přihlašování (SAML2).**
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na stránce **Jednotné přihlašování (SAML)** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Jako **možnost SAML**vyberte **možnost Ano**.
    
    b. Vložte **přihlašovací adresu URL**, kterou jste zkopírovali z portálu Azure do textového pole URL při **správě při přihlašovacíslužby SAML.**
    
    c. Vložte **adresu URL odhlášení**, kterou jste zkopírovali z portálu Azure do textového pole **Adresa URL vzdáleného odhlášení.**
    
    d. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte ho do textového pole **Certifikát X.509.**
    
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
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu UserEcho.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **UserEcho**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **UserEcho**.

    ![Odkaz UserEcho v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-userecho-test-user"></a>Vytvořit uživatele testu UserEcho

Cílem této části je vytvořit uživatele s názvem Britta Simon v UserEcho.

**Chcete-li vytvořit uživatele s názvem Britta Simon v UserEcho, proveďte následující kroky:**

1. Přihlaste se k webu společnosti UserEcho jako správce.

2. Na panelu nástrojů nahoře rozbalte nabídku klepnutím na uživatelské jméno a potom klepněte na **tlačítko Nastavit .**
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klepnutím na tlačítko **Uživatelé**rozbalte oddíl **Uživatelé.**
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klepněte na **položku Uživatelé**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klikněte **na Pozvat nového uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_12.png)

6. V **dialogovém okně Pozvat nového uživatele** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Do textového pole **Název** zadejte název uživatele, jako je Britta Simon.
    
    b.  Do textového pole **E-mail** zadejte Brittasimon@contoso.come-mailovou adresu uživatele, který se líbí .
    
    c. Klepněte na **tlačítko Pozvat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici UserEcho na přístupovém panelu, by měl být automaticky přihlášeni k UserEcho, pro které nastavíte přivytváření přistupujete. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

