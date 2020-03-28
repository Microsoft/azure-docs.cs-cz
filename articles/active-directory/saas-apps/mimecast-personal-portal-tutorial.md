---
title: 'Kurz: Integrace Služby Azure Active Directory s osobním portálem Mimecast | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a portálem Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160622"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Kurz: Integrace Azure Active Directory s osobním portálem Mimecast

V tomto kurzu se dozvíte, jak integrovat Mimecast Osobní portál s Azure Active Directory (Azure AD).
Integrace Osobního portálu Mimecast s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Mimecast osobní portál.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Osobnímu portálu Mimecast (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s osobním portálem Mimecast, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Mimecast Osobní portál jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mimecast Osobní portál podporuje **SP** inicioval SSO

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Přidání osobního portálu Mimecast z galerie

Chcete-li nakonfigurovat integraci osobního portálu Mimecast do Azure AD, musíte přidat osobní portál Mimecast z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat osobní portál Mimecast z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Osobní portál Mimecast**, z panelu výsledků vyberte **Mimecast Personal Portal** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Mimecast Osobní portál v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí osobního portálu Mimecast na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v osobním portálu Mimecast.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí osobního portálu Mimecast, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace mimecast u jednoho přihlášení portálu](#configure-mimecast-personal-portal-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte mimecast osobní portál testovací ho uživatele](#create-mimecast-personal-portal-test-user)** – mít protějšek Britta Simon v Mimecast osobní portál, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí osobního portálu Mimecast, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Mimecast Personal Portal** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Mimecast Osobní portál domény a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL: 

    | Region (Oblast)  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Region (Oblast)  |  Hodnota | 
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Spojené státy   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Jižní Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrálie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL: 

    | Region (Oblast)  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Hodnota Identifikátor není skutečná. Aktualizujte hodnotu skutečným identifikátorem. Obraťte se na [tým podpory klienta Mimecast Personal Portal,](https://www.mimecast.com/customer-success/technical-support/) abyste získali hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit mimecast osobní portál** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Konfigurace jednotného přihlašování na osobní portál Mimecast

1. V jiném okně webového prohlížeče se přihlaste k osobnímu portálu Mimecast jako správce.

2. Přejděte na **aplikace služeb \> **.
   
    ![Aplikace](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplikace")

3. Klepněte na **položku Profily ověřování**.
   
    ![Profily ověřování](./media/mimecast-personal-portal-tutorial/ic794999.png "Profily ověřování")

4. Klepněte na **položku Nový profil ověřování**.
   
    ![Nový profil ověřování](./media/mimecast-personal-portal-tutorial/ic795000.png "Nový profil ověřování")

5. V části **Profil ověřování** proveďte následující kroky:
   
    ![Profil ověřování](./media/mimecast-personal-portal-tutorial/ic795001.png "Profil ověřování")
   
    a. Do textového pole **Popis** zadejte název konfigurace.
   
    b. Vyberte **Vynutit ověřování SAML pro osobní portál Mimecast**.
   
    c. Jako **zprostředkovatel**vyberte **službu Azure Active Directory**.
   
    d. V textovém poli **URL vystavitisru** vložte hodnotu **Identifikátor azure ad**, kterou jste zkopírovali z webu Azure Portal.
   
    e. V textovém poli **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.
   
    f. Do textového pole **Adresa URL odhlášení** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    g. Otevřete svůj certifikát kódovaný **base-64** v poznámkovém bloku staženém z webu Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát poskytovatele identity (metadata).**

    h. Vyberte **povolit jednotné přihlašování**.
   
    i. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Mimecast osobní portál.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Mimecast Personal Portal**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **Osobní portál Mimecast**.

    ![Odkaz Osobní portál Mimecast v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-mimecast-personal-portal-test-user"></a>Vytvořit uživatele testu osobního portálu Mimecast

Aby se uživatelům Azure AD umožnilo přihlásit se k osobnímu portálu Mimecast, musí být zřízeni do osobního portálu Mimecast. V případě Mimecast osobní portál zřizování je ruční úlohy.

Před vytvořením uživatelů je třeba zaregistrovat doménu.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k **osobnímu portálu Mimecast** jako správce.

2. Přejděte na **Adresáře \> Interní**.
   
    ![Adresáře](./media/mimecast-personal-portal-tutorial/ic795003.png "Adresáře")

3. Klepněte na **tlačítko Registrovat novou doménu**.
   
    ![Zaregistrovat novou doménu](./media/mimecast-personal-portal-tutorial/ic795004.png "Zaregistrovat novou doménu")

4. Po vytvoření nové domény klepněte na tlačítko **Nová adresa**.
   
    ![Nová adresa](./media/mimecast-personal-portal-tutorial/ic795005.png "Nová adresa")

5. V dialogovém okně nová adresa proveďte následující kroky platného účtu Azure AD, který chcete zřídit:
   
    ![Uložit](./media/mimecast-personal-portal-tutorial/ic795006.png "Uložit")
   
    a. Do textového pole **E-mailová adresa** zadejte **e-mailovou adresu** uživatele jako **BrittaSimon\@contoso.com**.
    
    b. Do textového pole **Globální název** zadejte **uživatelské jméno** jako **BrittaSimon**.

    c. Do textových polí **Heslo**a **Potvrdit heslo** zadejte **heslo** uživatele.
   
    b. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné mimecast osobní portál nástroje pro vytváření uživatelských účtů nebo rozhraní API poskytované Mimecast Osobní portál pro zřízení uživatelských účtů Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Mimecast osobní portál na přístupovém panelu, měli byste být automaticky přihlášeni k Mimecast osobní portál, pro který nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

