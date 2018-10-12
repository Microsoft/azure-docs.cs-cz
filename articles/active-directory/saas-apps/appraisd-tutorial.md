---
title: 'Kurz: Integrace Azure Active Directory se službou Appraisd | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118511"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Kurz: Integrace Azure Active Directory se službou Appraisd

V tomto kurzu se dozvíte, jak integrovat Appraisd s Azure Active Directory (Azure AD).

Appraisd integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Appraisd.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Appraisd (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Appraisd, potřebujete následující položky:

- Předplatné Azure AD
- Appraisd jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Appraisd z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-appraisd-from-the-gallery"></a>Přidání Appraisd z Galerie
Konfigurace integrace Appraisd do služby Azure AD, budete muset přidat Appraisd z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Appraisd z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Appraisd**vyberte **Appraisd** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Appraisd podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Appraisd je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Appraisd potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Appraisd, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Appraisd](#create-an-appraisd-test-user)**  – Pokud chcete mít protějšek Britta Simon Appraisd, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Appraisd.

**Ke konfiguraci Azure AD jednotné přihlašování s Appraisd, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Appraisd** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Klikněte na tlačítko **nastavit další adresy URL**. 

    b. V **stav přenosu** textové pole, zadejte adresu URL: `<TENANTCODE>`

    c. Pokud chcete nakonfigurovat aplikace v **SP** zahájeno v režimu **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Na stránce Konfigurace jednotného přihlašování Appraisd, který je vysvětlen později v tomto kurzu získáte skutečnou hodnotu přihlašovací adresu URL a stav přenosu.
    
5. Appraisd aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    a. Klikněte na tlačítko **upravit** tlačítko Otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Z **zdrojový atribut** vyberte hodnotu atributu.

    c. Klikněte na **Uložit**. 

7. V **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. Na **nastavení Appraisd** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. V okně jiné webové prohlížeče Přihlaste se k Appraisd jako správce zabezpečení.

10. V horní části stránky, klikněte na **nastavení** ikonu, přejděte na **konfigurace**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. V levé nabídce klikněte na **SAML jednotného přihlašování**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. Na **konfigurace SAML 2.0 Single Sign-On** stránce, proveďte následující kroky:
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopírovat **výchozí stav přenosu** hodnotu a vložte ji **stav přenosu** textového pole v **základní konfiguraci SAML** na portálu Azure portal.

    b. Kopírovat **spouštěných službou přihlašovací adresa URL** hodnotu a vložte ji **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** na portálu Azure portal.

13. Přejděte dolů na stejné stránce v části **identifikace uživatelů**, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. V **jednotné přihlašování – adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z webu Azure portal a klikněte na tlačítko **Uložit**.

    b. V **adresa URL Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **Azure Ad identifikátor**, který jste zkopírovali z webu Azure portal a klikněte na tlačítko **Uložit**.

    c. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah a vložte jej do **certifikát X.509** pole a klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-appraisd-test-user"></a>Vytvořit testovacího uživatele Appraisd

Povolení služby Azure AD uživatelé přihlašují k Appraisd, se musí být poskytnuty do Appraisd. Zřizování v Appraisd, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Appraisd jako správce zabezpečení.

2. V horní části stránky, klikněte na **nastavení** ikonu, přejděte na **Centrum správy**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na panelu nástrojů v horní části stránky klikněte na tlačítko **lidé**, přejděte na **přidat nového uživatele**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na **přidat nového uživatele** stránce, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    d. Klikněte na **Přidat uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Appraisd použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogového okna, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Appraisd na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Appraisd.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
