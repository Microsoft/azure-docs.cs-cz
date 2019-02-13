---
title: 'Kurz: Integrace Azure Active Directory s systém SD Elements | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a systém SD Elements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09947e6d34c5314e8ed4bc2744f07b199278a6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188099"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Kurz: Integrace Azure Active Directory s systém SD Elements

V tomto kurzu se dozvíte, jak systém SD Elements integrovat s Azure Active Directory (Azure AD).

Systém SD Elements integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k prvkům SD
- Uživatele, aby automaticky získat přihlášení k systém SD Elements (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s systém SD Elements, potřebujete následující položky:

- Předplatné Azure AD
- Systém SD Elements jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání systém SD Elements z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sd-elements-from-the-gallery"></a>Přidání systém SD Elements z Galerie
Konfigurace integrace systém SD Elements do služby Azure AD, budete muset přidat systém SD Elements z Galerie na váš seznam spravovaných aplikací SaaS.

**Systém SD Elements přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **systém SD Elements**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_search.png)

1. Na panelu výsledků vyberte **systém SD Elements**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí systém SD Elements podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v systém SD Elements je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v systém SD Elements je potřeba navázat.

V systém SD Elements přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s systém SD Elements, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele systém SD Elements](#creating-a-sd-elements-test-user)**  – Pokud chcete mít protějšek Britta Simon v systém SD Elements, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci systém SD Elements.

**Ke konfiguraci Azure AD jednotné přihlašování s systém SD Elements, proveďte následující kroky:**

1. Na webu Azure Portal na **systém SD Elements** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sdelements_samlbase.png)

1. Na **SD prvky domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sdelements_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory systém SD Elements](mailto:support@sdelements.com) k získání těchto hodnot.

1. Systém SD Elements aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **"Atribut uživatele"** kartu aplikace. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sdelements_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky: 

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | e-mail |user.mail |
    | jméno |user.givenname |
    | Příjmení |user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_officespace_04.png)

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_officespace_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Klikněte na tlačítko **OK**.
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sdelements_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_general_400.png)

1. Na **SD elementů konfigurace** klikněte na tlačítko **nakonfigurovat systém SD Elements** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sdelements_configure.png)

1. Získat single sign-on povoleno, obraťte se na vaše [tým podpory systém SD Elements](mailto:support@sdelements.com) a poskytnout jim staženého souboru. 

1. V jiném okně prohlížeče přihlašování k vašemu tenantovi systém SD Elements jako správce.

1. V nabídce v horní části klikněte na tlačítko **systému**a potom **Single Sign-on**. 
   
    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sd-elements_09.png) 

1. Na **nastavení jednotného přihlašování** dialogového okna, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Jako **typ jednotného přihlašování**vyberte **SAML**.
   
    b. V **ID Entity zprostředkovatele Identity** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 
   
    c. V **službu poskytovatele identit jednotné přihlašování** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal. 
   
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sd-elements-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sd-elements-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sd-elements-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sd-elements-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sd-elements-test-user"></a>Vytvoření zkušebního uživatele systém SD Elements

Cílem této části je vytvořte uživatele Britta Simon v systém SD Elements. V případě systém SD Elements vytvoříte uživatele služby systém SD Elements je ruční úloha.

**Pokud chcete vytvořit systém SD Elements Britta Simon, proveďte následující kroky:**

1. V okně webového prohlížeče přihlašování k webu společnosti systém SD Elements jako správce.

1. V nabídce v horní části klikněte na tlačítko **Správa uživatelů**a potom **uživatelé**.
   
    ![Vytvoření zkušebního uživatele systém SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klikněte na tlačítko **přidání nového uživatele**.
   
    ![Vytvoření zkušebního uživatele systém SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)
 
1. Na **Add New User** dialogového okna, proveďte následující kroky:
   
    ![Vytvoření zkušebního uživatele systém SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.
   
    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.
   
    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.
   
    d. Jako **Role**vyberte **uživatele**. 
   
    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k systém SD Elements použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Systém SD Elements přiřadit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **systém SD Elements**.

    ![Konfigurace jednotného přihlašování](./media/sd-elements-tutorial/tutorial_sdelements_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.
  
Po kliknutí na dlaždici systém SD Elements na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci systém SD Elements.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/sd-elements-tutorial/tutorial_general_203.png

