---
title: 'Kurz: Integrace Azure Active Directory s řízením Workspot | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workspot ovládacího prvku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.openlocfilehash: 520e625ba9689ebf35e985fe95609c62102e2493
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49312658"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Kurz: Integrace Azure Active Directory s řízením Workspot

V tomto kurzu se dozvíte, jak integrovat ovládací prvek Workspot se službou Azure Active Directory (Azure AD).

Ovládací prvek Workspot integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Workspot ovládacího prvku.
- Uživatele, aby automaticky získat přihlášení k řízení Workspot (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ovládacím prvkem Workspot, potřebujete následující položky:

- Předplatné Azure AD
- Ovládacího prvku Workspot jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ovládacího prvku Workspot z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workspot-control-from-the-gallery"></a>Přidání ovládacího prvku Workspot z Galerie
Pokud chcete nakonfigurovat integraci Workspot ovládacího prvku do služby Azure AD, budete muset přidat ovládací prvek Workspot z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání ovládacího prvku Workspot z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Workspot řízení**vyberte **Workspot řízení** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s ovládacím prvkem Workspot podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ovládacím prvku Workspot je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatel v ovládacím prvku Workspot musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ovládacím prvkem Workspot, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ovládací prvek Workspot](#create-a-workspot-control-test-user)**  – Pokud chcete mít protějšek Britta Simon Workspot ovládací prvek, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Workspot ovládacího prvku.

**Ke konfiguraci Azure AD jednotné přihlašování s ovládacím prvkem Workspot, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Workspot řízení** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** intiated režimu:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory klienta řízení Workspot](mailto:support@workspot.com) k získání těchto hodnot. 

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Na **nastavit řízení Workspot** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    Všimněte si, že adresa URL může třeba následující:

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. V jiné okno webového prohlížeče, přihlaste se k řízení Workspot jako správce zabezpečení.

8. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavení**, přejděte na **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Na **konfiguraci zabezpečení kontrolního výrazu značky jazyka** stránce, proveďte následující kroky:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. V **Entity ID** textového pole vložte hodnotu **Azure Ad identifikátor** který jste zkopírovali z portálu Azure portal.   

    b.In **adresa URL služby Sign-on** textového pole vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    c. V **odhlašovací adresa URL služby** textového pole vložte hodnotu **odhlašovací adresa URL** který jste zkopírovali z portálu Azure portal. 

    d. Klikněte na **soubor aktualizace** tlačítko Nahrát base-64 kódování certifikátu, který jste si stáhli z portálu Azure portal do certifikátu X.509.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-workspot-control-test-user"></a>Vytvoření zkušebního uživatele Workspot ovládacího prvku

Přihlaste se k řízení Workspot Azure AD uživatelům umožnit, musí být poskytnuty do Workspot ovládacího prvku. V ovládacím prvku Workspot zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k řízení Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky klikněte na tlačítko **uživatelé**, přejděte na **přidat uživatele**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na **přidat nového uživatele** stránce, proveďte následující kroky:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    d. Vyberte příslušné role uživatele z **Role** rozevíracího seznamu.

    e. Vyberte příslušné skupiny uživatelů z **skupiny** rozevíracího seznamu.

    f. Klikněte na tlačítko **přidat uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k ovládacímu prvku Workspot.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **ovládací prvek Workspot**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Workspot ovládacího prvku na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Workspot ovládacího prvku.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
