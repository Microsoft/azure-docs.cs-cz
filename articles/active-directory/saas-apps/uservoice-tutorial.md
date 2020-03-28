---
title: 'Kurz: Integrace služby Azure Active Directory s uživatelem UserVoice | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a službou UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232005"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Kurz: Integrace služby Azure Active Directory s uservoice

V tomto kurzu se dozvíte, jak integrovat UserVoice s Azure Active Directory (Azure AD).
Integrace UserVoice s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k UserVoice.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k UserVoice (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí UserVoice, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jednorázovým přihlášením UserVoice

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* UserVoice podporuje **sp** inicioval sso

## <a name="adding-uservoice-from-the-gallery"></a>Přidání uservoice z galerie

Chcete-li nakonfigurovat integraci UserVoice do Azure AD, musíte přidat UserVoice z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat UserVoice z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **UserVoice**, z panelu výsledků vyberte **UserVoice** a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![UserVoice v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí uservoice na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v UserVoice.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí uservoice, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování UserVoice](#configure-uservoice-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu UserVoice](#create-uservoice-test-user)** – chcete-li mít protějšek Britta Simon v UserVoice, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí uservoice, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce Integrace aplikací **UserVoice** **možnost Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![UserVoice Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<tenantname>.UserVoice.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta UserVoice.](https://www.uservoice.com/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit uservoice** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-uservoice-single-sign-on"></a>Konfigurace jednotného přihlášení UserVoice

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti UserVoice jako správce.

2. Na panelu nástrojů nahoře klikněte na **Nastavení**a v nabídce vyberte **Webový portál.**
   
    ![Sekce Nastavení na straně aplikace](./media/uservoice-tutorial/ic777519.png "Nastavení")

3. Na kartě **Webový portál** klikněte v části **Ověřování uživatelů** na tlačítko **Upravit** a otevřete stránku dialogového okna Upravit **ověřování uživatelů.**
   
    ![Karta Webový portál](./media/uservoice-tutorial/ic777520.png "Webový portál")

4. Na stránce Upravit **ověřování uživatelů** proveďte následující kroky:
   
    ![Upravit ověřování uživatelů](./media/uservoice-tutorial/ic777521.png "Upravit ověřování uživatelů")
   
    a. Klikněte **na jednotné přihlašování (SSO).**
 
    b. Vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure do textového pole **vzdáleného přihlášení při přihlašování k objektům SSO.**

    c. Vložte hodnotu **URL odhlášení,** kterou jste zkopírovali z portálu Azure do **textového pole vzdáleného odhlášení služby SSO**.
 
    d. Vložte hodnotu **Kryptografický otisk** , kterou jste zkopírovali z portálu Azure do textového pole **otisku prstu SHA1 aktuálnícertifikát.**
    
    e. Klepněte na **tlačítko Uložit nastavení ověřování**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k UserVoice.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **UserVoice**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **UserVoice**.

    ![Odkaz UserVoice v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-uservoice-test-user"></a>Vytvořit uživatele testu UserVoice

Aby se uživatelé Azure AD mohli přihlásit k UserVoice, musí být zřízeni do UserVoice. V případě UserVoice zřizování je ruční úlohy.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se ke svému tenantovi **UserVoice.**

2. Přejděte do **nastavení**.
   
    ![Nastavení](./media/uservoice-tutorial/ic777811.png "Nastavení")

3. Klepněte na tlačítko **Obecné**.

4. Klepněte na **položku Agenti a oprávnění**.
   
    ![Agenti a oprávnění](./media/uservoice-tutorial/ic777812.png "Agenti a oprávnění")

5. Klikněte na **Přidat správce**.
   
    ![Přidání správců](./media/uservoice-tutorial/ic777813.png "Přidání správců")

6. V dialogovém okně **Pozvat správce** proveďte následující kroky:
   
    ![Pozvat správce](./media/uservoice-tutorial/ic777814.png "Pozvat správce")
   
    a. Do textového pole E-maily zadejte e-mailovou adresu účtu, který chcete zřídit, a klikněte na **Přidat**.
   
    b. Klepněte na **tlačítko Pozvat**.

> [!NOTE]
> Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů UserVoice nebo rozhraní API poskytovaná uživatelem UserVoice.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici UserVoice na přístupovém panelu, můžete by měl být automaticky přihlášeni k UserVoice, pro které nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

