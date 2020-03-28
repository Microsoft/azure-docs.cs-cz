---
title: 'Kurz: Integrace služby Azure Active Directory se společností Coupa | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709f3a5b66db660ade482660cd9b3930ff0b7141
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227652"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Kurz: Integrace Azure Active Directory se společností Coupa

V tomto kurzu se dozvíte, jak integrovat Coupa s Azure Active Directory (Azure AD).
Integrace Coupa s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Coupa.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Coupa (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Coupa, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Coupa

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Coupa podporuje **SP** inicioval SSO

## <a name="adding-coupa-from-the-gallery"></a>Přidání Coupa z galerie

Pokud chcete nakonfigurovat integraci Coupa do Azure AD, musíte přidat Coupa z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Coupa z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Coupa**, z panelu výsledků vyberte **Coupa** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Coupa v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí coupa na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Coupa.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí coupa, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace funkce Coupa Single Sign-On](#configure-coupa-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Coupa](#create-coupa-test-user)** – chcete-li mít protějšek Britta Simon v Coupa, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí coupa, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Coupa** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o doméně a adresách URL coupa](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.coupahost.com`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není skutečná. Aktualizujte tuto hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory klienta Coupa.](https://success.coupa.com/Support/Contact_Us?)

    b. Do pole **Identifikátor** zadejte adresu URL:

    | Prostředí  | zprostředkovatele identity |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Výroba | `sso-prd1.coupahost.com`|
    | | |

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL:

    | Prostředí | zprostředkovatele identity |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Výroba | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit Coupa** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-coupa-single-sign-on"></a>Konfigurace jednotného přihlašování kupé

1. Přihlaste se na svůj web společnosti Coupa jako správce.

2. Přejděte na **instalační program \> řízení zabezpečení**.

    ![Zabezpečovací mechanismy](./media/coupa-tutorial/ic791900.png "Zabezpečovací mechanismy")

3. V části **Přihlásit se pomocí přihlašovacích údajů společnosti Coupa** proveďte následující kroky:

    ![Metadata Coupa SP](./media/coupa-tutorial/ic791901.png "Metadata Coupa SP")

    a. Vyberte **Možnost Přihlásit se pomocí funkce SAML**.

    b. Kliknutím na **Procházet** nahrajte metadata stažená z webu Azure Portal.

    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Coupa.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Coupa**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Kupé**.

    ![Odkaz Coupa v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-coupa-test-user"></a>Vytvořit testovacího uživatele Coupa

Aby bylo možné uživatelům Azure AD přihlásit se do Coupa, musí být zřízena do Coupa.  

* V případě Coupa zřizování je ruční úlohy.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se na web společnosti **Coupa** jako správce.

2. V horní nabídce klepněte na tlačítko **Nastavení**a potom klepněte na tlačítko **Uživatelé**.

    ![Uživatelé](./media/coupa-tutorial/ic791908.png "Uživatelé")

3. Klikněte na **Vytvořit**.

    ![Vytvořit uživatele](./media/coupa-tutorial/ic791909.png "Vytvořit uživatele")

4. V části **Vytvoření uživatele** proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/coupa-tutorial/ic791910.png "Podrobnosti o uživateli")

    a. Zadejte **přihlášení**, **jméno**, **příjmení**, **jednorázové ID přihlášení**, **e-mailové** atributy platného účtu služby Azure Active Directory, který chcete zřídit, do souvisejících textových polí.

    b. Klikněte na **Vytvořit**.

    >[!NOTE]
    >Držitel účtu Azure Active Directory obdrží e-mail s odkazem na potvrzení účtu před tím, než se stane aktivním.
    >

>[!NOTE]
>Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Coupa nebo rozhraní API poskytovaná společností Coupa.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Coupa na přístupovém panelu, měli byste být automaticky přihlášeni k Coupa, pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

