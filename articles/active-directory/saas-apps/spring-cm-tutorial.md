---
title: 'Kurz: Azure Active Directory integrace s SpringCM | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SpringCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.openlocfilehash: d043386f7d0a4713c605f87a9f7bc9f65183029b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88517069"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Kurz: Azure Active Directory integrace s SpringCM

V tomto kurzu se dozvíte, jak integrovat SpringCM s Azure Active Directory (Azure AD).
Integrace SpringCM s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k SpringCM.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k SpringCM (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s SpringCM potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným SpringCMm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SpringCM podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-springcm-from-the-gallery"></a>Přidání SpringCM z Galerie

Pokud chcete nakonfigurovat integraci SpringCM do služby Azure AD, musíte přidat SpringCM z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat SpringCM z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SpringCM**, v panelu výsledek vyberte **SpringCM** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![SpringCM v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SpringCM na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SpringCM.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SpringCM, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování SpringCM](#configure-springcm-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření SpringCM Test User](#create-springcm-test-user)** – pro Britta Simon v SpringCM, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí SpringCM, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SpringCM** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně SpringCM a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta SpringCM](https://knowledge.springcm.com/support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **Nastavení SpringCM** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-springcm-single-sign-on"></a>Nakonfigurovat SpringCM jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu **SpringCM** společnosti jako správce.

1. V nabídce v horní části klikněte na **Přejít na**, klikněte na **Předvolby**a potom v části **Předvolby účtu** klikněte na **jednotné přihlašování SAML**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. V části Konfigurace zprostředkovatele identity proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/spring-cm-tutorial/ic797052.png "Konfigurace zprostředkovatele identity")
    
    a. Pokud chcete nahrát stažený Azure Active Directory certifikát, klikněte na **Vybrat certifikát vystavitele** nebo **změnit certifikát vystavitele**.
    
    b. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.
    
    c. Do textového pole **inicializovaného koncového bodu poskytovatele služeb (SP)** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
            
    d. Vyberte možnost **SAML povolená** jako **Povolit**.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k SpringCM.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **SpringCM**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SpringCM**.

    ![Odkaz SpringCM v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-springcm-test-user"></a>Vytvořit testovacího uživatele SpringCM

Pokud chcete povolit Azure Active Directory uživatelům, aby se přihlásili k SpringCM, musí se zřídit do SpringCM. V případě SpringCM je zřizování ručním úkolem.

> [!NOTE]
> Další informace najdete v tématu [Vytvoření a úprava uživatele SpringCM](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053). 

**Pokud chcete zřídit uživatelský účet SpringCM, proveďte následující kroky:**

1. Přihlaste se k webu **SpringCM** společnosti jako správce.

1. Klikněte na **Přejít**a pak klikněte na **adresář.**
   
    ![Vytvořit uživatele](./media/spring-cm-tutorial/ic797054.png "Vytvořit uživatele")

1. Klikněte na **vytvořit uživatele**.

1. Vyberte **uživatelskou roli**.

1. Vyberte **Odeslat aktivační e-mail**.

1. Do příslušných textových polí zadejte jméno, příjmení a e-mailovou adresu platného Azure Active Directory uživatelského účtu, který chcete zřídit.

1. Přidejte uživatele do **skupiny zabezpečení**.

1. Klikněte na **Uložit**.

   > [!NOTE]
   > K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů SpringCM nebo rozhraní API poskytovaná SpringCM.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SpringCM, měli byste se automaticky přihlásit k SpringCM, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

