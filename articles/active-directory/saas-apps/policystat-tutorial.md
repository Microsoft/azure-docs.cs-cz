---
title: 'Kurz: Integrace služby Azure Active Directory s PolicyStat | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 327e470d60235e6bf400293e80e3aec5f6144ff4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943448"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Kurz: Integrace služby Azure Active Directory s PolicyStat

V tomto kurzu se dozvíte, jak integrovat PolicyStat s Azure Active Directory (Azure AD).
Integrace PolicyStat s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k PolicyStat.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k PolicyStat (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s PolicyStat, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením PolicyStat

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PolicyStat podporuje **sp** inicioval asos

* PolicyStat podporuje zřizování uživatelů **just in time**

## <a name="adding-policystat-from-the-gallery"></a>Přidání PolicyStat z galerie

Chcete-li nakonfigurovat integraci PolicyStat do Azure AD, musíte přidat PolicyStat z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat PolicyStat z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PolicyStat**, z panelu výsledků vyberte **PolicyStat** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![PolicyStat v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí PolicyStat na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v PolicyStat.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí PolicyStat, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování PolicyStat](#configure-policystat-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit PolicyStat testovací ho uživatele](#create-policystat-test-user)** – mít protějšek Britta Simon v PolicyStat, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad s PolicyStat, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **PolicyStat** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![PolicyStat Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.policystat.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta PolicyStat](http://www.policystat.com/support/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

5. Vaše aplikace PolicyStat očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. Kromě výše, PolicyStat aplikace očekává několik dalších atributů, které mají být předány zpět v odpovědi SAML. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** přidejte atribut tokenu SAML následujícím postupem, jak je znázorněno v následující tabulce:

    | Name (Název) | Atribut zdroje |
    |------------------- | -------------------- |
    | Uid | ExtractMailPrefix([mail]) |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**
    
    ![image](common/new-save-attribute.png)

    ![image](./media/policystat-tutorial/attribute01.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **transformaci**.

    e. Ze seznamu **Transformace** zadejte hodnotu atributu zobrazenou pro daný řádek.
    
    f. Ze seznamu **Parametr 1** zadejte hodnotu atributu zobrazenou pro daný řádek.

    g. Klikněte na **Uložit**.

7. V části **Nastavit PolicyStat** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-policystat-single-sign-on"></a>Konfigurace jednotného přihlašování policystatu

1. V jiném okně webového prohlížeče se přihlaste na web společnosti PolicyStat jako správce.

2. Klikněte na kartu **Správce** a v levém **navigačním podokně klikněte na Konfigurace jednotného přihlášení.**
   
    ![Nabídka Správce](./media/policystat-tutorial/ic808633.png "Nabídka Správce")

3. V části **Nastavení** vyberte **Povolit integraci jednotného přihlašování**.
   
    ![Konfigurace jednotného přihlášení](./media/policystat-tutorial/ic808634.png "Konfigurace jednotného přihlášení")

4. Klepněte na **tlačítko Konfigurovat atributy**a potom v části **Konfigurovat atributy** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlášení](./media/policystat-tutorial/ic808635.png "Konfigurace jednotného přihlášení")
   
    a. Do textového pole **Atribut uživatelského jména** zadejte **uid**.

    b. Do textového pole **Atribut křestního jména** zadejte **jméno** uživatele **Britta**.

    c. Do textového pole **Atribut příjmení** zadejte **příjmení** uživatele **Simon**.

    d. Do textového pole **Atribut e-mailu** zadejte **e-mailovou adresu** uživatele `BrittaSimon@contoso.com`.

    e. Klikněte na **Save Changes** (Uložit změny).

5. Klikněte na **metadata IDP**a potom v části **Metadata idp** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlášení](./media/policystat-tutorial/ic808636.png "Konfigurace jednotného přihlášení")
   
    a. Otevřete stažený soubor metadat, zkopírujte obsah a vložte ho do textového pole **Metadata poskytovatele identity.**

    b. Klikněte na **Save Changes** (Uložit změny).

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k PolicyStat.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **PolicyStat**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **PolicyStat**.

    ![Odkaz PolicyStat v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-policystat-test-user"></a>Vytvořit testovacího uživatele PolicyStat

V této části je vytvořen uživatel s názvem Britta Simon v PolicyStat. PolicyStat podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v PolicyStat, nový je vytvořen po ověření.

>[!NOTE]
>Můžete použít jakékoli jiné PolicyStat nástroje pro vytváření uživatelských účtů nebo rozhraní API poskytované PolicyStat zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici PolicyStat na přístupovém panelu, můžete by měl být automaticky přihlášeni k PolicyStat, pro které nastavíte přiřazování k šaškům. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

