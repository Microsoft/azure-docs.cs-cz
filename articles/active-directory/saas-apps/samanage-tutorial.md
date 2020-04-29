---
title: 'Kurz: Azure Active Directory integrace s Samanage | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Samanage.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160113"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Kurz: Azure Active Directory integrace s Samanage

V tomto kurzu se dozvíte, jak integrovat Samanage s Azure Active Directory (Azure AD).
Integrace Samanage s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Samanage.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Samanage (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Samanage potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Samanagem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Samanage podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z Galerie

Pokud chcete nakonfigurovat integraci Samanage do služby Azure AD, musíte přidat Samanage z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Samanage z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Samanage**, vyberte **Samanage** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Samanage v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Samanage na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Samanage.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Samanage, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Samanage](#configure-samanage-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Samanage Test User](#create-samanage-test-user)** – pro Britta Simon v Samanage, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Samanage, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Samanage** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Samanage a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru, které jsou vysvětleny dále v tomto kurzu. Další podrobnosti získáte od [týmu podpory Samanage klientů](https://www.samanage.com/support). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Samanage** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-samanage-single-sign-on"></a>Konfigurace jednotného přihlašování Samanage

1. V jiném okně webového prohlížeče se přihlaste k webu Samanage společnosti jako správce.

2. Klikněte na **řídicí panel** a v levém navigačním podokně vyberte **Nastavení** .
   
    ![Ovládacího](./media/samanage-tutorial/tutorial_samanage_001.png "Řídicí panel")

3. Klikněte na **jednotné přihlašování**.
   
    ![Jednotné přihlašování](./media/samanage-tutorial/tutorial_samanage_002.png "Jednotné přihlašování")

4. Přejděte k části **přihlášení pomocí SAML** , proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Přihlášení pomocí SAML")
 
    a. Klikněte na **Povolit jednotné přihlašování pomocí SAML**.  
 
    b. Do textového pole **Adresa URL poskytovatele identity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.    
 
    c. Potvrďte **přihlašovací adresu URL** , která se shoduje s **adresou URL** **základního konfiguračního oddílu SAML** v Azure Portal.
 
    d. Do textového pole **Adresa URL pro odhlášení** zadejte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
 
    e. Do textového pole **vystavitele SAML** zadejte identifikátor URI ID aplikace nastavený ve Vašem zprostředkovateli identity.
 
    f. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do pole **Vložit poskytovatele identity x. 509 pod** textovým polem.
 
    g. **Pokud v Samanage neexistují**, klikněte na vytvořit uživatele.
 
    h. Klikněte na **Aktualizovat**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Samanage.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Samanage**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Samanage**.

    ![Odkaz Samanage v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-samanage-test-user"></a>Vytvořit testovacího uživatele Samanage

Aby se uživatelé Azure AD mohli přihlásit k Samanage, musí se zřídit v Samanage.  
V případě Samanage je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Samanage společnosti jako správce.

2. Klikněte na **řídicí panel** a vyberte **Nastavení** v levém navigačním panelu.
   
    ![Instalace a nastavení](./media/samanage-tutorial/tutorial_samanage_001.png "Nastavení")

3. Klikněte na kartu **Uživatelé** .
   
    ![Uživatelé](./media/samanage-tutorial/tutorial_samanage_006.png "Uživatelé")

4. Klikněte na **Nový uživatel**.
   
    ![Nový uživatel](./media/samanage-tutorial/tutorial_samanage_007.png "Nový uživatel")

5. Zadejte **jméno** a **e-mailovou adresu** účtu Azure Active Directory, který chcete zřídit, a klikněte na **vytvořit uživatele**.
   
    ![Vytvořit uživatele](./media/samanage-tutorial/tutorial_samanage_008.png "Vytvořit uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní. K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Samanage nebo rozhraní API poskytovaná Samanage.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Samanage, měli byste se automaticky přihlásit k Samanage, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

