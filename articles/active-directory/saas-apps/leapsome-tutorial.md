---
title: 'Kurz: Integrace Azure Active Directory s Leapsome | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37549cc76e1490b0758de8e296523b0e70c98dbf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191227"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Kurz: Integrace Azure Active Directory s Leapsome

V tomto kurzu se dozvíte, jak integrovat Leapsome s Azure Active Directory (Azure AD).

Leapsome integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Leapsome.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Leapsome (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Leapsome, potřebujete následující položky:

- Předplatné Azure AD
- Leapsome jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Leapsome z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-leapsome-from-the-gallery"></a>Přidání Leapsome z Galerie
Konfigurace integrace Leapsome do služby Azure AD, budete muset přidat Leapsome z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Leapsome z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Leapsome**vyberte **Leapsome** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Leapsome v seznamu výsledků](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Leapsome podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Leapsome je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Leapsome potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Leapsome, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Leapsome](#create-a-leapsome-test-user)**  – Pokud chcete mít protějšek Britta Simon Leapsome, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Leapsome.

**Ke konfiguraci Azure AD jednotné přihlašování s Leapsome, proveďte následující kroky:**

1. Na webu Azure Portal na **Leapsome** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. Na **Leapsome domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Leapsome domény a adresy URL jednotného přihlašování – informace](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://www.leapsome.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Leapsome domény a adresy URL jednotného přihlašování – informace](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Předchozí hodnota adresy URL adresy URL odpovědi a Sign-on není skutečné hodnoty. Budete aktualizovat těchto prvků s skutečné hodnoty, který je vysvětlen později v tomto kurzu.

1. Leapsome aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad.
    
    ![Konfigurace jednotného přihlašování](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Obor názvů |
    | ---------------| --------------- | --------- |   
    | jméno | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Příjmení | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | název | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Obrázek | Adresa URL obrázku zaměstnance. | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Hodnota atributu obrázek není skutečný. Aktualizujte tuto hodnotu s adresou URL aktuální obrázek. Chcete-li získat tento kontakt hodnotu [tým podpory Leapsome klienta](mailto:support@leapsome.com).
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. V **Namespace** textového pole zadejte identifikátor uri oboru názvů pro tento řádek.
    
    e. Klikněte na tlačítko **Ok**

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. Na **Leapsome konfigurace** klikněte na tlačítko **nakonfigurovat Leapsome** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k Leapsome jako správce zabezpečení.

1. Vpravo nahoře klikněte na logo nastavení a potom klikněte na tlačítko **nastavení správy**. 

    ![Sada Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. V nabídce vlevo klikněte na tlačítko **jednotné přihlašování (SSO)** a dále **založené na SAML jednotného přihlašování (SSO)** stránku, proveďte následující kroky:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Vyberte **založené na SAML povolit jednotné přihlašování**.

    b. Kopírovat **adresu URL pro přihlášení (přejděte sem uživatelé start přihlášení)** hodnotu a vložte ho do **přihlašovací adresa URL** textového pole v **Leapsome domény a adresy URL** části na webu Azure portal.

    c. Kopírovat **adresy URL odpovědi (obdrží odpověď od vašeho zprostředkovatele identity)** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **Leapsome domény a adresy URL** části na webu Azure portal.

    d. V **adresu URL pro přihlášení SSO (získaný od zprostředkovatele identity)** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    e. Zkopírujte certifikát si stáhli z webu Azure portal bez – certifikát BEGIN a END certifikátu – komentáře a vložte ji **certifikát (poskytnutý poskytovatelem identity)** textového pole.

    f. Klikněte na tlačítko **aktualizace nastavení jednotného přihlašování**.
    
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/leapsome-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/leapsome-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/leapsome-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-leapsome-test-user"></a>Vytvoření zkušebního uživatele Leapsome

V této části vytvoříte uživatele v Leapsome jako Britta Simon. Práce s [tým podpory Leapsome klienta](mailto:support@leapsome.com) přidat uživatele nebo domény, který je třeba přidat na seznam povolených Leapsome platformě. Pokud je doména přidána týmem, se získat uživatele automaticky přiřazeni k Leapsome platformy. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Leapsome použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Leapsome Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Leapsome**.

    ![Odkaz Leapsome v seznamu aplikací](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Leapsome na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Leapsome.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

