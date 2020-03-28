---
title: 'Kurz: Integrace služby Azure Active Directory se samanageem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 741efbae84e18c811beb6c0579c1949c5ddf619c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160113"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Kurz: Integrace služby Azure Active Directory se samanageem

V tomto kurzu se dozvíte, jak integrovat Samanage s Azure Active Directory (Azure AD).
Integrace Samanage s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke samanage.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke Samanage (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Samanage, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Saspráva předplatného s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Samanage podporuje **sp** inicioval sso

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z galerie

Chcete-li nakonfigurovat integraci Samanage do Azure AD, musíte přidat Samanage z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Samanage z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Samanage**, vyberte **Samanage** z panelu výsledků a kliknutím na **tlačítko Přidat** přidejte aplikaci.

     ![Samanage v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí samanageu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Samanageu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Samanage, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace samanage jednotného přihlašování](#configure-samanage-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Samanage](#create-samanage-test-user)** – chcete-li mít protějšek Britta Simon v Samanage, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí samanage, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce Integrace aplikací **Samanage** **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Samanage Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem, která je vysvětlena dále v kurzu. Další podrobnosti získáte od [týmu podpory klienta Samanage](https://www.samanage.com/support). Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Samanage** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-samanage-single-sign-on"></a>Konfigurace jednotného přihlašování Samanage

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Samanage jako správce.

2. Klikněte na **Řídicí panel** a v levém navigačním podokně vyberte **Nastavení.**
   
    ![Řídicího panelu](./media/samanage-tutorial/tutorial_samanage_001.png "Řídicí panel")

3. Klikněte **na Jedno přihlášení**.
   
    ![Jednotné přihlašování](./media/samanage-tutorial/tutorial_samanage_002.png "Jednotné přihlašování")

4. Přejděte na Přihlásit se pomocí oddílu **SAML,** proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Přihlášení pomocí SAML")
 
    a. Klepněte **na tlačítko Povolit jednotné přihlašování pomocí saml**.  
 
    b. V textovém poli **URL zprostředkovatele identity** vložte hodnotu **identifikátoru Azure Ad,** kterou jste zkopírovali z webu Azure Portal.    
 
    c. Potvrďte, že **adresa URL přihlášení** odpovídá adrese URL **přihlašování** v části **Základní konfigurace SAML** na webu Azure Portal.
 
    d. Do textového pole **Adresa URL odhlášení** zadejte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.
 
    e. Do textového pole **vydavatele SAML** zadejte identifikátor URI aplikace nastavený ve vašem zprostředkovateli identity.
 
    f. Otevřete svůj certifikát kódovaný ze základní ho 64 stažený z portálu Azure v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Vložit svého poskytovatele identity x.509.**
 
    g. Klepněte na tlačítko **Vytvořit uživatele, pokud v samanageu neexistují.**
 
    h. Klikněte na **Aktualizovat**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Samanage.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Samanage**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Samanage**.

    ![Odkaz Samanage v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-samanage-test-user"></a>Vytvořit testovacího uživatele Samanage

Chcete-li povolit uživatelům Azure AD k přihlášení ke sasporou, musí být zřízeny do Samanage.  
V případě Samanage zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Samanage jako správce.

2. Klikněte na **Řídicí panel** a v levém navigačním panelu vyberte **Nastavení.**
   
    ![Nastavení](./media/samanage-tutorial/tutorial_samanage_001.png "Nastavení")

3. Klikněte na kartu **Uživatelé.**
   
    ![Uživatelé](./media/samanage-tutorial/tutorial_samanage_006.png "Uživatelé")

4. Klepněte na **položku Nový uživatel**.
   
    ![Nový uživatel](./media/samanage-tutorial/tutorial_samanage_007.png "Nový uživatel")

5. Zadejte **název** a **e-mailovou adresu** účtu Služby Azure Active Directory, který chcete zřídit, a klikněte na Vytvořit **uživatele**.
   
    ![Vytvořit uživatele](./media/samanage-tutorial/tutorial_samanage_008.png "Vytvořit uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory obdrží e-mail a poté potvrdí odkaz, který potvrdí jeho účet, než se stane aktivním. Ke zřizování uživatelských účtů Azure Active Directory můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Samanage nebo rozhraní API poskytovaná samanageem.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Samanage na přístupovém panelu, můžete by měl být automaticky přihlášeni ke samanage, u kterého nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

