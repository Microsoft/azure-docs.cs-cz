---
title: 'Kurz: Azure Active Directory integrace s Coupa | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: a116dff0eba7e5469ab0c352c30bca506ee18238
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549253"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Kurz: Azure Active Directory integrace s Coupa

V tomto kurzu se dozvíte, jak integrovat Coupa s Azure Active Directory (Azure AD).
Integrace Coupa s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Coupa.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Coupa (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Coupa potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Coupam jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Coupa podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-coupa-from-the-gallery"></a>Přidání Coupa z Galerie

Pokud chcete nakonfigurovat integraci Coupa do služby Azure AD, musíte přidat Coupa z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Coupa z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Coupa**, vyberte **Coupa** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Coupa v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Coupa na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Coupa.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Coupa, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Coupa](#configure-coupa-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Coupa Test User](#create-coupa-test-user)** – pro Britta Simon v Coupa, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Coupa, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Coupa** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Coupa a adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta Coupa](https://success.coupa.com/Support/Contact_Us?) .

    b. Do pole **identifikátor** zadejte adresu URL:

    | Prostředí  | URL |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Výroba | `sso-prd1.coupahost.com`|
    | | |

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL:

    | Prostředí | URL |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Výroba | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Coupa** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-coupa-single-sign-on"></a>Nakonfigurovat Coupa jednu Sign-On

1. Přihlaste se k webu Coupa společnosti jako správce.

2. Přejít k ** \> ovládacímu prvku zabezpečení instalace**.

    ![Ovládací prvky zabezpečení](./media/coupa-tutorial/ic791900.png "Zabezpečovací mechanismy")

3. V části **přihlášení pomocí přihlašovacích údajů Coupa** proveďte následující kroky:

    ![Metadata Coupa SP](./media/coupa-tutorial/ic791901.png "Metadata Coupa SP")

    a. Vyberte možnost **Přihlásit se pomocí SAML**.

    b. Kliknutím na tlačítko **Procházet** nahrajte metadata stažená z Azure Portal.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Coupa.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Coupa**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Coupa**.

    ![Odkaz Coupa v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-coupa-test-user"></a>Vytvořit testovacího uživatele Coupa

Aby se uživatelé Azure AD mohli přihlašovat k Coupa, musí se zřídit v Coupa.  

* V případě Coupa je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu společnosti **Coupa** jako správce.

2. V nabídce v horní části klikněte na **Nastavení**a potom klikněte na **Uživatelé**.

    ![Uživatelé](./media/coupa-tutorial/ic791908.png "Uživatelé")

3. Klikněte na **Vytvořit**.

    ![Vytvoření uživatelů](./media/coupa-tutorial/ic791909.png "Vytváření uživatelů")

4. V části **Vytvoření uživatele** proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/coupa-tutorial/ic791910.png "Podrobnosti o uživateli")

    a. Do příslušných textových polí zadejte **přihlašovací**jméno, **jméno**, **příjmení, jméno**a **jedno Sign-On**, atributy **e-mailu** platného Azure Active Directory účtu, který chcete zřídit.

    b. Klikněte na **Vytvořit**.

    >[!NOTE]
    >Držitel účtu Azure Active Directory získá e-mail s odkazem na potvrzení účtu před jeho aktivním použitím.
    >

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Coupa nebo rozhraní API poskytovaná Coupa.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Coupa, měli byste se automaticky přihlásit k Coupa, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

