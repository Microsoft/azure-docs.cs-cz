---
title: 'Kurz: Integrace služby Azure Active Directory s BitaBIZ | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f827945cbeccacfdf048865b6e89b6947fe7de9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159382"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Kurz: Integrace služby Azure Active Directory s BitaBIZ

V tomto kurzu se dozvíte, jak integrovat BitaBIZ s Azure Active Directory (Azure AD).
Integrace BitaBIZ s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k BitaBIZ.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k BitaBIZ (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s BitaBIZ, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením BitaBIZ

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* BitaBIZ podporuje **SP a IDP** inicioval SSO

## <a name="adding-bitabiz-from-the-gallery"></a>Přidání BitaBIZ z galerie

Chcete-li nakonfigurovat integraci BitaBIZ do Azure AD, musíte přidat BitaBIZ z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat BitaBIZ z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **BitaBIZ**, z panelu výsledků vyberte **BitaBIZ** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![BitaBIZ v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí BitaBIZ na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v BitaBIZ.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí BitaBIZ, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednoho přihlášení BitaBIZ](#configure-bitabiz-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte bitabiz test uživatele](#create-bitabiz-test-user)** – mít protějšek Britta Simon v BitaBIZ, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí bitabiz, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **BitaBIZ** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující krok:

    ![BitaBIZ Domény a adresy URL jednotné přihlašovací informace](common/idp-identifier.png)

    Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Hodnota ve výše uvedené adrese URL je pouze pro demonstraci. Aktualizujte hodnotu skutečným identifikátorem, který je vysvětlen dále v kurzu.

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://www.bitabiz.com/dashboard`

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit BitaBIZ** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-bitabiz-single-sign-on"></a>Konfigurace jednotného přihlášení BitaBIZ

1. V jiném okně webového prohlížeče se přihlaste ke svému tenantovi BitaBIZ jako správce.

2. Klikněte na **SETUP ADMIN**.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Klikněte na **integrace Microsoftu** v části **Přidat hodnotu.**

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings2.png)

4. Přejděte dolů do části **Microsoft Azure AD (Povolit jednotné přihlašování)** a proveďte následující kroky:

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Zkopírujte hodnotu z textového pole **ID entity ("Identifikátor" v Azure AD)** a vložte ji do textového pole **Identifikátor** v části **Základní konfigurace SAML** na webu Azure Portal. 

    b. V textovém poli **adresy URL služby jednotného přihlašování azure ad** vložte **přihlašovací adresu URL**, kterou jste zkopírovali z webu Azure Portal.

    c. V textovém poli **ID entity Azure AD SAML** vložte **identifikátor Azure Ad**, který jste zkopírovali z webu Azure Portal.

    d. Otevřete soubor stažený **certifikát (Base64)** v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **podpisového certifikátu Azure AD (Base64. kódovaný).**

    e. Přidejte název firemní e-mailové domény, který je mycompany.com v textovém poli **Název domény** a přiřaďte uživatelské přizběcování uživatelům ve vaší společnosti s touto e-mailovou doménou (NENÍ POVINNÉ).

    f. Mark **SSO povolil** účet BitaBIZ.

    g. Kliknutím na **Uložit konfiguraci Azure AD** uložte a aktivujte konfiguraci přizpůsobovaného přiřazu.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k BitaBIZ.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **BitaBIZ**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **BitaBIZ**.

    ![Odkaz BitaBIZ v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-bitabiz-test-user"></a>Vytvořit testovacího uživatele BitaBIZ

Chcete-li povolit uživatelům Azure AD k přihlášení k BitaBIZ, musí být zřízena do BitaBIZ.  
V případě BitaBIZ zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se na své stránky společnosti BitaBIZ jako správce.

2. Klikněte na **SETUP ADMIN**.

    ![BitaBIZ Přidat uživatele](./media/bitabiz-tutorial/settings1.png)

3. Klikněte na **Přidat uživatele** v části **Organizace.**

    ![BitaBIZ Přidat uživatele](./media/bitabiz-tutorial/user1.png)

4. Klikněte na **Přidat nového zaměstnance**.

    ![BitaBIZ Přidat uživatele](./media/bitabiz-tutorial/user2.png)

5. Na stránce Dialogové okno **Přidat nového zaměstnance** proveďte následující kroky:

    ![BitaBIZ Přidat uživatele](./media/bitabiz-tutorial/user3.png)

    a. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele, jako je Britta.

    b. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je Simon.

    c. Do textového pole **E-mail** zadejte Brittasimon@contoso.come-mailovou adresu uživatele, který se líbí .

    d. Vyberte datum v **datu zaměstnání**.

    e. Existují i jiné nepovinné uživatelské atributy, které lze nastavit pro uživatele. Další podrobnosti naleznete v [dokumentu nastavení zaměstnanců.](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)

    f. Klikněte na **Uložit zaměstnance**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a následuje odkaz pro potvrzení svého účtu před tím, než se stane aktivním.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici BitaBIZ na přístupovém panelu, můžete by měl být automaticky přihlášeni k BitaBIZ, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
