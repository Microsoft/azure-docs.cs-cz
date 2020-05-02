---
title: 'Kurz: Azure Active Directory integrace s osobním portálem Mimecast | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a osobním portálem Mimecast.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160622"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Kurz: Azure Active Directory integrace s osobním portálem Mimecast

V tomto kurzu se dozvíte, jak integrovat osobní portál Mimecast s Azure Active Directory (Azure AD).
Integrace osobního portálu Mimecast s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, která má přístup k osobnímu portálu Mimecast.
* Uživatelům můžete povolit, aby se automaticky přihlásili k osobnímu portálu Mimecast (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí osobního portálu Mimecast potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s podporou jednotného přihlašování Mimecast osobního portálu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mimecast Personal Portal podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Přidání osobního portálu Mimecast z Galerie

Pokud chcete nakonfigurovat integraci osobního portálu Mimecast do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat osobní portál Mimecast z galerie.

**K přidání osobního portálu Mimecast z Galerie proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mimecast Personal Portal**, vyberte z panelu výsledků možnost **osobní portál Mimecast** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Mimecast osobní portál v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí osobního portálu Mimecast na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v osobním portálu Mimecast.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí osobního portálu Mimecast, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování na osobním portálu Mimecast](#configure-mimecast-personal-portal-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele osobního portálu Mimecast](#create-mimecast-personal-portal-test-user)** , abyste měli protějšek Britta Simon na osobním portálu Mimecast, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí osobního portálu Mimecast, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **osobního portálu Mimecast** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény a adresy URL osobního portálu Mimecast](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Prací        | `https://jer-api.mimecast.com/login/saml`|

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Oblast  |  Hodnota | 
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Spojené státy   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Jižní Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrálie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Prací        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Prací        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizujte hodnotu skutečným identifikátorem. Pokud chcete získat hodnotu, kontaktujte [tým podpory Mimecast osobního portálu](https://www.mimecast.com/customer-success/technical-support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení osobního portálu Mimecast** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Konfigurace jednotného přihlašování osobního portálu Mimecast

1. V jiném okně webového prohlížeče se přihlaste k osobnímu portálu Mimecast jako správce.

2. Přejít na **aplikace \> služeb**.
   
    ![Aplikace](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplikace")

3. Klikněte na **profily ověřování**.
   
    ![Profily ověřování](./media/mimecast-personal-portal-tutorial/ic794999.png "Profily ověřování")

4. Klikněte na **Nový profil ověřování**.
   
    ![Nový ověřovací profil](./media/mimecast-personal-portal-tutorial/ic795000.png "Nový ověřovací profil")

5. V části **profil ověřování** proveďte následující kroky:
   
    ![Ověřovací profil](./media/mimecast-personal-portal-tutorial/ic795001.png "Ověřovací profil")
   
    a. Do textového pole **Popis** zadejte název konfigurace.
   
    b. Vyberte **vynutil ověřování SAML pro osobní portál Mimecast**.
   
    c. Jako **zprostředkovatel**vyberte **Azure Active Directory**.
   
    d. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.
   
    e. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.
   
    f. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    g. Otevřete v programu Poznámkový blok certifikát s kódováním **Base-64** stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity (metadata)** .

    h. Vyberte možnost **povolení jednotného přihlašování**.
   
    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k osobním portálu Mimecast.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **osobní portál Mimecast**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **osobní portál Mimecast**.

    ![Odkaz na osobního portálu Mimecast v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-mimecast-personal-portal-test-user"></a>Vytvořit testovacího uživatele osobního portálu Mimecast

Aby se uživatelé Azure AD mohli přihlásit k osobnímu portálu Mimecast, musí být zřízené na osobním portálu Mimecast. V případě osobního portálu Mimecast je zřizování ručním úkolem.

Než budete moct vytvořit uživatele, musíte zaregistrovat doménu.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k **osobnímu portálu Mimecast** jako správce.

2. Přejít na **interní \> adresáře**.
   
    ![Adresáře](./media/mimecast-personal-portal-tutorial/ic795003.png "Adresáře")

3. Klikněte na **zaregistrovat novou doménu**.
   
    ![Zaregistrovat novou doménu](./media/mimecast-personal-portal-tutorial/ic795004.png "Zaregistrovat novou doménu")

4. Po vytvoření nové domény klikněte na **Nová adresa**.
   
    ![Nová adresa](./media/mimecast-personal-portal-tutorial/ic795005.png "Nová adresa")

5. V dialogovém okně Nová adresa proveďte následující kroky platného účtu Azure AD, který chcete zřídit:
   
    ![Uložit](./media/mimecast-personal-portal-tutorial/ic795006.png "Uložit")
   
    a. Do textového pole **e-mailová adresa** zadejte **e-mailovou adresu** uživatele jako **BrittaSimon\@contoso.com**.
    
    b. Do textového pole **globální název** zadejte **uživatelské jméno** jako **BrittaSimon**.

    c. Do textového pole **heslo**a **potvrzení hesla** zadejte **heslo** uživatele.
   
    b. Klikněte na **Uložit**.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API osobního portálu Mimecast, které poskytuje osobní portál Mimecast.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici osobního portálu Mimecast na přístupovém panelu byste měli být automaticky přihlášeni k osobnímu portálu Mimecast, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

