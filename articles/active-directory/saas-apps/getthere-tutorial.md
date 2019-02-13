---
title: 'Kurz: Integrace Azure Active Directory s GetThere | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GetThere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b132da4a763490fa6c7a73c80f8e2e3a11b42e9b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173126"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>Kurz: Integrace Azure Active Directory s GetThere

V tomto kurzu se dozvíte, jak integrovat GetThere s Azure Active Directory (Azure AD).

GetThere integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k GetThere.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k GetThere (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s GetThere, potřebujete následující položky:

- Předplatné Azure AD
- GetThere jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání GetThere z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-getthere-from-the-gallery"></a>Přidání GetThere z Galerie
Konfigurace integrace GetThere do služby Azure AD, budete muset přidat GetThere z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat GetThere z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/getthere-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/getthere-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/getthere-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **GetThere**vyberte **GetThere** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí GetThere podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v GetThere je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v GetThere potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s GetThere, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele GetThere](#create-a-getthere-test-user)**  – Pokud chcete mít protějšek Britta Simon GetThere, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci GetThere.

**Ke konfiguraci Azure AD jednotné přihlašování s GetThere, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **GetThere** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![image](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. V **identifikátor** textové pole, zadejte jeden z následující adresy URL:
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. V **adresy URL odpovědi** text zadejte některou z následující adresy URL:
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. Aplikace GetThere očekává jedinečné **uživatelské jméno** hodnotu v deklaraci identity uživatelského jména. Odběratele můžete namapovat správné hodnoty pro deklarace identity uživatelského jména. V následujícím příkladu jsme změnili **uživatelské jméno** k **user.mail**, ale můžete změnit mapování podle nastavení vaší organizace. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](./media/getthere-tutorial/i4-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název |  Zdrojový atribut |  Obor názvů |
    | ---------------| --------------- | --------------- |
    | Název webu | "Zajistit dobu hodnotu podle vaší organizaci" | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | Uživatelské jméno |  user.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/getthere-tutorial/i2-attribute.png)

    ![image](./media/getthere-tutorial/i3-attribute.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. V **Namespace** textového pole zadejte obor názvů atributu zobrazený pro tento řádek.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače.

    ![image](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. Na **nastavení GetThere** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    Všimněte si, že adresa URL může třeba následující:

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    ![image](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. Ke konfiguraci jednotného přihlašování na **GetThere** straně, je nutné odeslat na stažený **certifikát (Base64)** a zkopírovaný **odhlašovací adresa URL pro přihlášení, Azure Ad identifikátor, adresa URL** k [ Tým podpory GetThere klienta](mailto:dataintegration@sabre.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/getthere-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/getthere-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/getthere-tutorial/d_userproperties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-getthere-test-user"></a>Vytvoření zkušebního uživatele GetThere

V této části vytvoříte uživatele v GetThere jako Britta Simon. Práce s [tým podpory GetThere klienta](mailto:dataintegration@sabre.com) přidat uživatele na platformě GetThere. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k GetThere použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/getthere-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **GetThere**.

    ![image](./media/getthere-tutorial/tutorial_getthere_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/getthere-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/getthere-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici GetThere na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci GetThere.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
