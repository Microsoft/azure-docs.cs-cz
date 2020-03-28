---
title: 'Kurz: Integrace služby Azure Active Directory s Mozy Enterprise | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233498"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Kurz: Integrace Azure Active Directory s Mozy Enterprise

V tomto kurzu se dozvíte, jak integrovat Mozy Enterprise s Azure Active Directory (Azure AD).
Integrace Mozy Enterprise s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Mozy Enterprise.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke Mozy Enterprise (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Mozy Enterprise, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Mozy Enterprise

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mozy Enterprise podporuje **sp** inicioval SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Přidání Mozy Enterprise z galerie

Pokud chcete nakonfigurovat integraci Mozy Enterprise do Azure AD, musíte přidat Mozy Enterprise z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Mozy Enterprise z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mozy Enterprise**, z panelu výsledků vyberte **Mozy Enterprise** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Mozy Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Mozy Enterprise na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Mozy Enterprise.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Mozy Enterprise, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace aplikace Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Mozy Enterprise](#create-mozy-enterprise-test-user)** – chcete-li mít protějšek Britta Simon v Mozy Enterprise, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Mozy Enterprise, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Mozy Enterprise** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Mozy Enterprise Domain a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory mozy enterprise klienta](https://support.mozy.com/) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Mozy Enterprise** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurace jednorázového přihlašování Mozy Enterprise

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Mozy Enterprise jako správce.

2. V části **Konfigurace** klepněte na **položku Zásady ověřování**.
   
    ![Zásady ověřování](./media/mozy-enterprise-tutorial/ic777314.png "Zásady ověřování")

3. V části **Zásady ověřování** proveďte následující kroky:
   
    ![Zásady ověřování](./media/mozy-enterprise-tutorial/ic777315.png "Zásady ověřování")
   
    a. Jako **zprostředkovatele**vyberte **adresářové služby** .
   
    b. Vyberte **použít nabízenou položku LDAP**.
   
    c. Klikněte na kartu **Ověřování SAML.**
   
    d. Vložte **přihlašovací adresu URL**, kterou jste zkopírovali z portálu Azure do textového pole Url **ověřování.**
   
    e. Vložte **identifikátor Azure AD**, který jste zkopírovali z portálu Azure do textového pole **koncový bod SAML.**
   
    f. Otevřete stažený certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte celý certifikát do textového pole **Certifikát SAML.**
   
    g. Vyberte **Povolit správu pro správce, aby se přihlásili pomocí svých síťových pověření**.
   
    h. Klikněte na **Save Changes** (Uložit změny).

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu mozy enterprise.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Mozy Enterprise**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Mozy Enterprise**.

    ![Odkaz Mozy Enterprise v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-mozy-enterprise-test-user"></a>Vytvořit testovacího uživatele Mozy Enterprise

Aby bylo možné uživatelům Azure AD přihlásit se k Mozy Enterprise, musí být zřízena do Mozy Enterprise. V případě Mozy Enterprise zřizování je ruční úlohy.

>[!NOTE]
>Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Mozy Enterprise nebo rozhraní API poskytované Mozy Enterprise zřídit uživatelské účty Azure AD.

**Chcete-li zřídit uživatelské účty, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Mozy Enterprise.**

2. Klepněte na tlačítko **Uživatelé**a potom klepněte na tlačítko **Přidat nového uživatele**.
   
    ![Uživatelé](./media/mozy-enterprise-tutorial/ic777317.png "Uživatelé")
   
    >[!NOTE]
    >Možnost **Přidat nového uživatele** se zobrazí pouze v případě, že je **mozy** vybrán jako zprostředkovatel v části **Zásady ověřování**. Pokud je nakonfigurováno ověřování SAML, uživatelé jsou automaticky přidáni při prvním přihlášení prostřednictvím jednotného přihlášení.
    
3. V novém uživatelském dialogovém okně proveďte následující kroky:
   
    ![Přidat uživatele](./media/mozy-enterprise-tutorial/ic777318.png "Přidat uživatele")
   
    a. V seznamu **Zvolit skupinu** vyberte skupinu.
   
    b. V seznamu **Jaký typ uživatele** vyberte typ.
   
    c. Do textového pole **Uživatelské jméno** zadejte název uživatele Azure AD.
   
    d. Do textového pole **E-mail** zadejte e-mailovou adresu uživatele Azure AD.
   
    e. Vyberte **Možnost Odeslat e-mail s pokyny uživatele**.
   
    f. Klikněte na **Přidat uživatele .**

     >[!NOTE]
     > Po vytvoření uživatele se uživateli Azure AD odešle e-mail, který obsahuje odkaz pro potvrzení účtu předtím, než se stane aktivním.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Mozy Enterprise na přístupovém panelu, můžete by měl být automaticky přihlášeni k Mozy Enterprise, pro které nastavíte přistupující spo. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

