---
title: 'Kurz: Azure Active Directory integrace s Zoho | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.openlocfilehash: 00b9df3ad5e4d2104051e5489138785b4ecde8e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546113"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Kurz: Azure Active Directory integrace s Zoho

V tomto kurzu se dozvíte, jak integrovat Zoho s Azure Active Directory (Azure AD).
Integrace Zoho s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Zoho.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Zoho (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Zoho potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Zohom jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zoho podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-zoho-from-the-gallery"></a>Přidání Zoho z Galerie

Pokud chcete nakonfigurovat integraci Zoho do služby Azure AD, musíte přidat Zoho z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Zoho z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zoho**, vyberte **Zoho** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zoho v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zoho na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zoho.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zoho, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Zoho](#configure-zoho-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Zoho Test User](#create-zoho-test-user)** – pro Britta Simon v Zoho, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Zoho, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Zoho** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Zoho a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Zoho](https://www.zoho.com/mail/contact.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Zoho** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-zoho-single-sign-on"></a>Nakonfigurovat Zoho jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Zoho mail jako správce.

2. Otevřete **Ovládací panely**.
   
    ![Ovládací panel](./media/zoho-mail-tutorial/ic789607.png "Ovládací panel")

3. Klikněte na kartu **ověřování SAML** .
   
    ![Ověřování SAML](./media/zoho-mail-tutorial/ic789608.png "Ověřování SAML")

4. V části **Podrobnosti ověřování SAML** proveďte následující kroky:
   
    ![Podrobnosti ověřování SAML](./media/zoho-mail-tutorial/ic789609.png "Podrobnosti ověřování SAML")
   
    a. Do textového pole **Adresa URL pro přihlášení** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.
   
    b. V textovém poli **Adresa URL pro odhlášení** vložte **adresu URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.
   
    c. V poli **změnit adresu URL hesla** vložte adresu **URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.
       
    d. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **PublicKey** .
   
    e. Jako **algoritmus**vyberte **RSA**.
   
    f. Klikněte na **OK**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zoho.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Zoho**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zoho**.

    ![Odkaz Zoho v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-zoho-test-user"></a>Vytvořit testovacího uživatele Zoho

Aby se uživatelé Azure AD mohli přihlásit k e-mailu Zoho, musí se zřídit v e-mailu Zoho. V případě Zoho pošty je zřizování ručním úkolem.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Zoho mail nebo rozhraní API, které poskytuje Zoho mail.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu společnosti **Zoho mail** jako správce.

1. Přejít na **ovládací panel \> Pošta & docs**.

1. Přejít na **Podrobnosti o uživateli \> Přidat uživatele**.
   
    ![Přidat uživatele](./media/zoho-mail-tutorial/ic789611.png "Přidání uživatele")

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
   
    ![Přidat uživatele](./media/zoho-mail-tutorial/ic789612.png "Přidání uživatele")
   
    a. Do textového pole **jméno v prvním** poli zadejte jméno uživatele jako **Britta**.

    b. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    c. Do textového pole **ID e-mailu** zadejte ID e-mailu uživatele, jako je **brittasimon \@ contoso.com**.

    d. Do textového pole **heslo** zadejte heslo uživatele.
   
    e. Klikněte na **OK**.  
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail s odkazem na potvrzení účtu předtím, než se aktivuje.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Zoho, měli byste se automaticky přihlásit k Zoho, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

