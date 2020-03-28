---
title: 'Kurz: Integrace služby Azure Active Directory s novou relikvií | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a novou relikvií.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233520"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Kurz: Integrace služby Azure Active Directory s novou relikvií

V tomto kurzu se dozvíte, jak integrovat New Relic s Azure Active Directory (Azure AD).
Integrace nové relikvie s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Nové relikvie.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k nové relikvii (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s novou relikvií, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Nové předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* New Relic podporuje **SP** inicioval SSO

## <a name="adding-new-relic-from-the-gallery"></a>Přidání nové relikvie z galerie

Chcete-li nakonfigurovat integraci nové relikvie do Azure AD, musíte přidat new relic z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat novou relikvii z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Novou relikvii**, z panelu výsledků vyberte **Nová relikvie** a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![Nová relikvie v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace New Relic na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v New Relic.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace New Relic, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace nového jednotného přihlášení relic](#configure-new-relic-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit nový relikvie testovací ho uživatele](#create-new-relic-test-user)** – mít protějšek Britta Simon v new relic, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí aplikace New Relic, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce Integrace **nové** aplikace možnost I **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Nové informace o doméně relikvii a adresách URL s jedním přihlášením](common/sp-identifier.png)

    a. Do textového pole **Přihlásit se na adresu** `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` URL zadejte adresu URL pomocí následujícího vzoru: - Nezapomeňte nahradit své vlastní ID nového relikvičního účtu.

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL:`rpm.newrelic.com`

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit novou relikvii** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-new-relic-single-sign-on"></a>Konfigurace nového jednotného přihlášení relic

1. V jiném okně webového prohlížeče se přihlaste na web **společnosti New Relic** jako správce.

2. V horní nabídce klikněte na **Nastavení účtu**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797036.png "Nastavení účtu")

3. Klikněte na kartu **Zabezpečení a ověřování** a potom klikněte na kartu Jednotné **znaménko.**
   
    ![Jednotné přihlašování](./media/new-relic-tutorial/ic797037.png "Jednotné přihlašování")

4. Na stránce dialogového okna SAML proveďte následující kroky:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Kliknutím **na Vybrat soubor** nahrajete stažený certifikát Azure Active Directory.

    b. V textovém poli Adresa **URL vzdáleného přihlášení** vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z webu Azure Portal.
   
    c. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    d. Klikněte na **Uložit mé změny**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Nové relikvie.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Nová relikvie**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Nový relikvie**.

    ![Odkaz Nová relikvie v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-new-relic-test-user"></a>Vytvořit nového uživatele testu relikvií

Aby se uživatelé služby Azure Active Directory mohli přihlásit do nové relikvie, musí být zřízeni do nové relikvie. V případě New Relic zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet pro novou relikvii, proveďte následující kroky:**

1. Přihlaste se k webu **společnosti New Relic** jako správce.

2. V horní nabídce klikněte na **Nastavení účtu**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797040.png "Nastavení účtu")

3. V podokně **Účet** na levé straně klikněte na **Souhrn**a potom klikněte na **Přidat uživatele**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797041.png "Nastavení účtu")

4. V dialogovém okně **Aktivní uživatelé** proveďte následující kroky:
   
    ![Aktivní uživatelé](./media/new-relic-tutorial/ic797042.png "Aktivní uživatelé")
   
    a. Do textového pole **E-mail** zadejte e-mailovou adresu platného uživatele služby Azure Active Directory, kterého chcete zřídit.

    b. Jako **role** vyberte **uživatele**.

    c. Klepněte na tlačítko **Přidat tohoto uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů New Relic nebo rozhraní API poskytované New Relic zřídit uživatelské účty Azure AD.
> 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Nová relikvie na přístupovém panelu, můžete by měl být automaticky přihlášeni k nové relikvie, pro které nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

