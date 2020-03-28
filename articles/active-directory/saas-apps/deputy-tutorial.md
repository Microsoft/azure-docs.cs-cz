---
title: 'Kurz: Integrace služby Azure Active Directory se zástupcem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a deputyem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd021b9547048e9f5218ce4f2b8b110b15d05963
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048563"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Kurz: Integrace služby Azure Active Directory se zástupcem

V tomto kurzu se dozvíte, jak integrovat zástupce s Azure Active Directory (Azure AD).
Integrace zástupce s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zástupce.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke zástupci (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s deputy, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Zástupce jednotného přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zástupce podporuje **SP** a **IDP** inicioval sso

## <a name="adding-deputy-from-the-gallery"></a>Přidání zástupce z galerie

Chcete-li nakonfigurovat integraci zástupce do Azure AD, musíte přidat zástupce z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat zástupce z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zástupce**, z panelu výsledků vyberte **Zástupce** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zástupce v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí zástupce na základě testovacího uživatele s názvem **Britta Simon**.
Pro jednotné přihlašování do práce, propojení vztah mezi uživatelem Azure AD a související uživatel v Zástupce musí být vytvořena.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zástupce, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace zástupce jednotného přihlášení](#configure-deputy-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit zástupce testovacího uživatele](#create-deputy-test-user)** – mít protějšek Britta Simon v zástupce, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Deputy, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce **Integrace aplikací Zástupce** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Informace o jednotném přihlášení zástupce domény a adres URL](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:
    
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs` |

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Informace o jednotném přihlášení zástupce domény a adres URL](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Přípona náměstka regionu je nepovinná, nebo by měla používat jednu z těchto: au | na | eu |jako |la |af |a |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se na [tým podpory zástupce klienta](https://www.deputy.com/call-centers-customer-support-scheduling-software) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit zástupce** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-deputy-single-sign-on"></a>Konfigurace jednotného přihlašování zástupce

1. Přejděte na následující`https://(your-subdomain).deputy.com/exec/config/system_config`adresu URL: . Přejděte do **nastavení zabezpečení** a klepněte na tlačítko **Upravit**.
   
    ![Konfigurace jednotného přihlašování](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Na této stránce **Nastavení zabezpečení** proveďte následující kroky.

    ![Konfigurace jednotného přihlašování](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Povolit **přihlášení do sociální ch odsek-sítě**.
   
    b. Otevřete kódovaný certifikát Base64 stažený z portálu Azure v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát OpenSSL.**
   
    c. Do textového pole URL při spo.`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. V textovém poli adresy URL `<your subdomain>` při správě při správě SAML nahraďte subdoménou.
   
    e. V textovém poli adresy URL `<saml sso url>` při správě při saml nahraďte **přihlašovací adresu URL,** kterou jste zkopírovali z portálu Azure.
   
    f. Klepněte na **tlačítko Uložit nastavení**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu zástupce.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Zástupce**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu žádostí vyberte **zástupce**.

    ![Odkaz Zástupce v seznamu Žádostí](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-deputy-test-user"></a>Vytvořit zástupce testovacího uživatele

Chcete-li povolit uživatelům Azure AD k přihlášení k Zástupce, musí být zřízena do Zástupce. V případě zástupce zřizování je ruční úlohy.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu společnosti zástupce jako správce.

2. V horním navigačním podokně klikněte na **Lidé**.
   
    ![People](./media/deputy-tutorial/tutorial_deputy_001.png "People")

3. Klikněte na tlačítko **Přidat lidi** a klikněte na **Přidat jednu osobu**.
   
    ![Přidat lidi](./media/deputy-tutorial/tutorial_deputy_002.png "Přidat lidi")

4. Proveďte následující kroky a klepněte na **tlačítko Uložit & pozvat**.
   
    ![Nový uživatel](./media/deputy-tutorial/tutorial_deputy_003.png "Nový uživatel")

    a. Do textového pole **Název** zadejte název uživatele, jako je **BrittaSimon**.
   
    b. Do textového pole **E-mail** zadejte e-mailovou adresu účtu Azure AD, který chcete zřídit.
   
    c. Do textového pole **Práce na** zadejte název firmy.
   
    d. Klikněte na **tlačítko Uložit & pozvat.**

5. Držitel účtu Azure AD obdrží e-mail a následuje odkaz pro potvrzení svého účtu před tím, než se stane aktivním. Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů zástupce nebo rozhraní API poskytované zástupce zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Zástupce na přístupovém panelu, by měl být automaticky přihlášeni k zástupce, pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

