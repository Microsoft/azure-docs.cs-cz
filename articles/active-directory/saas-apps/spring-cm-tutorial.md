---
title: 'Kurz: Integrace Azure Active Directory se službou SpringCM | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.openlocfilehash: 777586bc24c0962ec1f0ba3913397bfe26d0d0d5
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841060"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Kurz: Integrace Azure Active Directory se službou SpringCM

V tomto kurzu se dozvíte, jak integrovat SpringCM s Azure Active Directory (Azure AD).

SpringCM integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SpringCM
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SpringCM (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SpringCM, potřebujete následující položky:

- Předplatné Azure AD
- SpringCM jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SpringCM z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-springcm-from-the-gallery"></a>Přidání SpringCM z Galerie
Konfigurace integrace SpringCM do služby Azure AD, budete muset přidat SpringCM z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SpringCM z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **SpringCM**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/spring-cm-tutorial/tutorial_springcm_search.png)

1. Na panelu výsledků vyberte **SpringCM**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s SpringCM podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SpringCM je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SpringCM potřeba navázat.

V SpringCM, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SpringCM, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SpringCM](#creating-a-springcm-test-user)**  – Pokud chcete mít protějšek Britta Simon SpringCM, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SpringCM.

**Ke konfiguraci Azure AD jednotné přihlašování s SpringCM, proveďte následující kroky:**

1. Na webu Azure Portal na **SpringCM** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/spring-cm-tutorial/tutorial_springcm_samlbase.png)

1. Na **SpringCM domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/spring-cm-tutorial/tutorial_springcm_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory SpringCM klienta](https://knowledge.springcm.com/support) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Raw)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/spring-cm-tutorial/tutorial_springcm_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/spring-cm-tutorial/tutorial_general_400.png)

1. Na **SpringCM konfigurace** klikněte na tlačítko **nakonfigurovat SpringCM** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/spring-cm-tutorial/tutorial_springcm_configure.png)     

1. V okně jiné webové prohlížeče, přihlaste se k vaší **SpringCM** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **přejít na**, klikněte na tlačítko **Předvolby**a pak na **předvolby účtu** klikněte na tlačítko **SAML SSO**.
   
    ![JEDNOTNÉ PŘIHLAŠOVÁNÍ SAML](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. V části Konfigurace zprostředkovatele Identity proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/spring-cm-tutorial/ic797052.png "konfigurace zprostředkovatele Identity")
    
    a. Chcete-li nahrát svůj certifikát stažený Azure Active Directory, klikněte na tlačítko **vyberte certifikát, Issuer** nebo **změnu vystavitele certifikátu**.
    
    b. Vložit **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure portal do **vystavitele** textového pole.
    
    c. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal do **koncový bod iniciované poskytovatele služeb (SP)** textového pole.
            
    d. Vyberte **povoleno SAML** jako **povolit**.

    e. Klikněte na **Uložit**.
 
> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/spring-cm-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/spring-cm-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/spring-cm-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/spring-cm-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-springcm-test-user"></a>Vytvoření zkušebního uživatele SpringCM

Povolení uživatelů Azure Active Directory pro přihlášení k SpringCM, musí být poskytnuty do SpringCM. V případě SpringCM zřizování se ruční úlohy.

>[!NOTE]
>Další informace najdete v tématu [vytvářet a upravovat uživatele SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Ke zřízení uživatelského účtu pro SpringCM, proveďte následující kroky:**

1. Přihlaste se k vaší **SpringCM** společnosti serveru jako správce.

1. Klikněte na tlačítko **GOTO**a potom klikněte na tlačítko **adresář**.
   
    ![Vytvoření uživatele](./media/spring-cm-tutorial/ic797054.png "vytvoření uživatele")

1. Klikněte na tlačítko **vytvořit uživatele**.

1. Vyberte **Role uživatele**.

1. Vyberte **poslat aktivační E-mail**.

1. Zadejte jméno, příjmení a e-mailovou adresu platného účtu uživatele Azure Active Directory, které chcete zřídit do související textových polí.

1. Přidejte uživatele do **skupiny zabezpečení**.

1. Klikněte na **Uložit**.

  >[!NOTE]
  >Můžete použít jakékoli jiné SpringCM uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných SpringCM uživatelským účtům, zřídit AAD.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SpringCM použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit SpringCM Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SpringCM**.

    ![Konfigurace jednotného přihlašování](./media/spring-cm-tutorial/tutorial_springcm_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.
 
Po kliknutí na dlaždici SpringCM na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SpringCM.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/spring-cm-tutorial/tutorial_general_203.png

