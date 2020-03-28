---
title: 'Kurz: Integrace služby Azure Active Directory se společností Thoughtworks Mingle | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a aplikací Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233296"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Kurz: Integrace Služby Azure Active Directory se společností Thoughtworks Mingle

V tomto kurzu se dozvíte, jak integrovat Thoughtworks Mingle s Azure Active Directory (Azure AD).
Integrace aplikace Thoughtworks Mingle s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Thoughtworks Mingle.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Thoughtworks Mingle (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí aplikace Thoughtworks Mingle, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Thoughtworks Mingle

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Thoughtworks Mingle podporuje **sp** inicioval SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Přidání Thoughtworks Mingle z galerie

Chcete-li nakonfigurovat integraci aplikace Thoughtworks Mingle do služby Azure AD, je třeba přidat aplikaci Thoughtworks Mingle z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat promínání Thoughtworks z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Thoughtworks Mingle**, z panelu výsledků vyberte **Thoughtworks Mingle** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Thoughtworks Mingle v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace Thoughtworks Mingle na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v thoughtworks mingle.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace Thoughtworks Mingle, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace aplikace Thoughtworks Mingle Single Sign-On](#configure-thoughtworks-mingle-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte thoughtworks mingle testovacího uživatele](#create-thoughtworks-mingle-test-user)** – chcete-li mít protějšek Britta Simon v Thoughtworks Mingle, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí aplikace Thoughtworks Mingle, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Thoughtworks Mingle** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení k doménám a adresám URL společnosti Thoughtworks](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory pro zákazníka Thoughtworks Mingle,](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) abyste získali hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit aplikaci Thoughtworks Mingle** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Konfigurace jednotného přihlašování pro aplikaci Thoughtworks

1. Přihlaste se k webu společnosti **Thoughtworks Mingle** jako správce.

2. Klikněte na kartu **Správce** a potom klikněte na **položku SSO Config**.
   
    ![Karta Správce](./media/thoughtworks-mingle-tutorial/ic785157.png "Konfigurace přihlašovacího a následného přihlaš")

3. V části **Konfigurace přihlašovacím příkazu** proveďte následující kroky:
   
    ![Konfigurace přihlašovacího a následného přihlaš](./media/thoughtworks-mingle-tutorial/ic785158.png "Konfigurace přihlašovacího a následného přihlaš")
    
    a. Chcete-li soubor metadat nahrát, klepněte na **tlačítko Vybrat soubor**. 

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Thoughtworks Mingle.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Thoughtworks Mingle**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Thoughtworks Mingle**.

    ![Odkaz Thoughtworks Mingle v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-thoughtworks-mingle-test-user"></a>Vytvořit testovacího uživatele Thoughtworks Mingle

Aby se uživatelé Azure AD mohli přihlásit, musí být zřízeni do aplikace Thoughtworks Mingle pomocí svých uživatelských jmen služby Azure Active Directory. V případě Thoughtworks Mingle zřizování je ruční úlohy.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Thoughtworks Mingle jako správce.

2. Klepněte na **položku Profil**.
   
    ![Váš první projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "Váš první projekt")

3. Klikněte na kartu **Správce** a potom klikněte na **Uživatelé**.
   
    ![Uživatelé](./media/thoughtworks-mingle-tutorial/ic785161.png "Uživatelé")

4. Klepněte na **položku Nový uživatel**.
   
    ![Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785162.png "Nový uživatel")

5. Na stránce dialogového okna **Nový uživatel** proveďte následující kroky:
   
    ![Dialogové okno Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785163.png "Nový uživatel")  
 
    a. Zadejte **přihlašovací jméno**, **zobrazované jméno**, **Vyberte heslo**, **Potvrďte heslo** platného účtu Azure AD, který chcete zřídit do souvisejících textových polí. 

    b. Jako **typ Uživatele**vyberte možnost Úplný **uživatel**.

    c. Klepněte na **tlačítko Vytvořit tento profil**.

>[!NOTE]
>Můžete použít všechny ostatní Nástroje pro vytváření uživatelských účtů Thoughtworks Mingle nebo rozhraní API poskytované Thoughtworks Mingle pro zřizování uživatelských účtů Azure AD.
> 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Thoughtworks Mingle na přístupovém panelu, měli byste být automaticky přihlášeni k Thoughtworks Mingle, pro které nastavíte přispojené k zámečku. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

