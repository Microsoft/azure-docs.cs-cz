---
title: 'Kurz: Azure Active Directory integrace s TimeOffManager | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 69c5d30632e187efe36655a17a91c9e373062955
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943190"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Kurz: Integrace Azure Active Directory s TimeOffManager

V tomto kurzu se dozvíte, jak integrovat TimeOffManager s Azure Active Directory (Azure AD).
Integrace TimeOffManager s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k TimeOffManager.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k TimeOffManager (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s TimeOffManager potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným TimeOffManagerm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TimeOffManager podporuje jednotné přihlašování **IDP** .

* TimeOffManager podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-timeoffmanager-from-the-gallery"></a>Přidání TimeOffManager z Galerie

Pokud chcete nakonfigurovat integraci TimeOffManager do služby Azure AD, musíte přidat TimeOffManager z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat TimeOffManager z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TimeOffManager**, vyberte **TimeOffManager** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![TimeOffManager v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TimeOffManager na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TimeOffManager.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TimeOffManager, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování TimeOffManager](#configure-timeoffmanager-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření TimeOffManager Test User](#create-timeoffmanager-test-user)** – pro Britta Simon v TimeOffManager, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí TimeOffManager, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **TimeOffManager** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně TimeOffManager a adresách URL](common/idp-reply.png)

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Tuto hodnotu můžete získat na **stránce nastavení jednotného přihlašování** , která je vysvětlena dále v kurzu nebo se obraťte na [tým podpory TimeOffManager](https://www.purelyhr.com/contact-us). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. TimeOffManager aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

6. Kromě výše očekává aplikace TimeOffManager několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce: 

    | Name | Zdrojový atribut|
    | --- | --- |
    | FirstName |User. křestní jméno |
    | Polím |User. příjmení |
    | Email |User.mail |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavení TimeOffManager** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-timeoffmanager-single-sign-on"></a>Konfigurace jednotného přihlašování TimeOffManager

1. V jiném okně webového prohlížeče se přihlaste k webu TimeOffManager společnosti jako správce.

2. Přejít na  **\> možnosti\> účtu účtu nastavení jednotného přihlašování**
   
    ![Nastavení jednotného přihlašování](./media/timeoffmanager-tutorial/ic795917.png "Nastavení jednotného přihlašování")

3. V části **nastavení jednotného přihlašování** proveďte následující kroky:
   
    ![Nastavení jednotného přihlašování](./media/timeoffmanager-tutorial/ic795918.png "Nastavení jednotného přihlašování")
   
    a. V programu Poznámkový blok otevřete svůj certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte celý certifikát do textového pole **certifikátu X. 509** .
   
    b. Do textového pole vystavitele **IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.
   
    c. Do textového pole **Adresa URL koncového bodu IDP** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
   
    d. Jako **Vynutilí SAML**vyberte **ne**.
   
    e. Jako **automaticky vytvořit uživatele**vyberte **Ano**.
   
    f. Do textového pole **Adresa URL** pro odhlášení vložte hodnotu **adresy URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.
   
    g. klikněte na **Uložit změny**.

4. Na stránce **nastavení jednotného přihlašování** Zkopírujte hodnotu **adresy URL služby pro příjemce kontrolního výrazu** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal. 

      ![Nastavení jednotného přihlašování](./media/timeoffmanager-tutorial/ic795915.png "Nastavení jednotného přihlašování")

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k TimeOffManager.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **TimeOffManager**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **TimeOffManager**.

    ![Odkaz TimeOffManager v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-timeoffmanager-test-user"></a>Vytvořit testovacího uživatele TimeOffManager

V této části se v TimeOffManager vytvoří uživatel s názvem Britta Simon. TimeOffManager podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v TimeOffManager neexistuje, vytvoří se po ověření nový.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů TimeOffManager nebo rozhraní API poskytovaná TimeOffManager.
> 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici TimeOffManager, měli byste se automaticky přihlásit k TimeOffManager, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

