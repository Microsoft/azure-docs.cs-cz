---
title: 'Kurz: Integrace Azure Active Directory s oblouk publikování – jednotné přihlašování | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a publikování oblouku – jednotné přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: eafd7998e5bc21a539b6709794fe3cd70d9e3179
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054314"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Kurz: Integrace Azure Active Directory s oblouk publikování – jednotné přihlašování

V tomto kurzu se dozvíte, jak integrovat oblouk publikování – jednotné přihlašování s Azure Active Directory (Azure AD).

Integrace oblouk publikování – jednotné přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k publikování oblouku – jednotné přihlašování.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k publikování oblouku – jednotné přihlašování (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s oblouk publikování – jednotné přihlašování, budete potřebovat následující položky:

- S předplatným služby Azure AD
- Publikování oblouku – jednotné přihlašování jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Publikování oblouku – přidání jednotného přihlašování z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Publikování oblouku – přidání jednotného přihlašování z Galerie
Konfigurace integrace oblouk publikování – jednotné přihlašování do služby Azure AD, budete muset přidat oblouk publikování – jednotné přihlašování z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat oblouk publikování – jednotné přihlašování z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **oblouk publikování – jednotné přihlašování**vyberte **oblouk publikování – jednotné přihlašování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Publikování oblouku – jednotné přihlašování v seznamu výsledků](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk publikování – jednotné přihlašování založené na testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatel oblouk Publishing – jednotné přihlašování je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské oblouk Publishing - jednotného přihlašování je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk publikování – jednotné přihlašování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření publikování oblouk – jednotného přihlašování testovací uživatele](#create-an-arc-publishing---sso-test-user)**  – Pokud chcete mít protějšek Britta Simon oblouk Publishing - jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich oblouk publikování - jednotného přihlašování aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s oblouk publikování – jednotné přihlašování, proveďte následující kroky:**

1. Na webu Azure Portal na **oblouk publikování – jednotné přihlašování** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. Na **oblouk publikování - jednotného přihlašování k doméně a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Publikování oblouk - jednotného přihlašování k doméně a adresy URL jednotné přihlašování – informace](./media/arc-tutorial/tutorial_arc_url.png)

    1. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    1. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Publikování oblouk - jednotného přihlašování k doméně a adresy URL jednotné přihlašování – informace](./media/arc-tutorial/tutorial_arc_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [oblouk publikování - tým podpory jednotného přihlašování klienta](mailto:inf@washpost.com) k získání těchto hodnot. 

1. Publikování oblouku – jednotného přihlašování aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/arc-tutorial/tutorial_arc_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | Jméno | user.givenname |
    | Příjmení | user.surname |
    | e-mail | User.Mail |
    | skupiny | user.assignedroles |

    1. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

     ![Konfigurace jednotného přihlašování](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Konfigurace jednotného přihlašování](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    1. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    1. Nechte **Namespace** prázdné.
    
    1. Klikněte na tlačítko **Ok**

    > [!NOTE]
    > Tady **skupiny** atribut je namapována na žádnou **user.assignedroles**. Jedná se o vlastní role vytvořené ve službě Azure AD k mapování názvů skupin zpět v aplikaci. Můžete najít ještě s něčím poradit [tady](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) o tom, jak vytvořit vlastní role ve službě Azure AD. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/arc-tutorial/tutorial_general_400.png)
    
1. Na **oblouk publikování – Konfigurace jednotného přihlašování** klikněte na tlačítko **konfigurace oblouk publikování – jednotné přihlašování** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Publikování oblouku – Konfigurace jednotného přihlašování](./media/arc-tutorial/tutorial_arc_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **oblouk publikování – jednotné přihlašování** straně, je nutné odeslat na stažený **certifikát (Base64), URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [oblouk Publikování - tým podpory jednotného přihlašování](mailto:inf@washpost.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/arc-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/arc-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/arc-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/arc-tutorial/create_aaduser_04.png)

    1. V **název** zadejte **BrittaSimon**.

    1. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    1. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Vytvoření oblouk publikování – jednotné přihlašování testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon oblouk Publishing - jednotného přihlašování. Publikování oblouku – jednotného přihlašování podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k publikování oblouku – jednotné přihlašování, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [oblouk publikování - tým podpory jednotného přihlašování](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k publikování oblouku – jednotné přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon oblouk publikování – jednotné přihlašování, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **oblouk publikování – jednotné přihlašování**.

    ![Publikování oblouk - jednotného přihlašování k odkazu v seznamu aplikací](./media/arc-tutorial/tutorial_arc_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí oblouk publikování – dlaždice jednotného přihlašování na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší oblouk publikování - jednotného přihlašování aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

