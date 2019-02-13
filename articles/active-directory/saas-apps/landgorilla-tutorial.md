---
title: 'Kurz: Integrace Azure Active Directory s klientem váhou pozemního | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pozemního váhou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2e1288885476aeeba01f966b615c23c5fe51081
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199506"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Kurz: Integrace Azure Active Directory s pozemního váhou klienta

V tomto kurzu se dozvíte, jak integrovat pozemního váhou klienta služby Azure Active Directory (Azure AD).

Integrace pozemního váhou klienta s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k pozemního váhou klienta
- Uživatele, aby automaticky získat přihlášení k pozemního váhou klienta (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s klientem váhou pozemního, potřebujete následující položky:

- Předplatné Azure AD
- Klienta váhou pozemního jednotného přihlašování povolená předplatného


> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.


Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání klienta váhou pozemního z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Přidání klienta váhou pozemního z Galerie
Konfigurace integrace pozemního váhou klienta do služby Azure AD, budete muset přidat pozemního váhou klienta z Galerie na váš seznam spravovaných aplikací SaaS.

**Postup přidání klienta váhou pozemního z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **pozemního váhou klienta**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. Na panelu výsledků vyberte **pozemního váhou klienta**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí pozemního váhou klienta na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v klientovi pozemního váhou je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v klientovi pozemního váhou.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v klientovi pozemního váhou.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí pozemního váhou klienta, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s omezenou skupinou.
1. **[Vytvoření zkušebního uživatele pozemního váhou](#creating-a-land-gorilla-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v pozemního váhou klientské aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí pozemního váhou klienta, proveďte následující kroky:**

1. V portálu pro správu Azure na **pozemního váhou klienta** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. Na **pozemního váhou klienta domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. V **identifikátor** textového pole zadejte hodnotu pomocí jedné z následujícímu vzoru: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí jedné z následujícímu vzoru:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Tady doporučujeme používat jedinečnou hodnotu řetězce v identifikátoru. Kontakt [pozemního váhou klienta team](https://www.landgorilla.com/support/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Získat kompletní Konfigurace jednotného přihlašování pro vaši aplikaci na konci pozemního váhou, obraťte se na [tým podpory pozemního váhou klienta](https://www.landgorilla.com/support/) a umožnit jim na stažený **"soubor XML s metadaty** souboru.


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-land-gorilla-test-user"></a>Vytvoření zkušebního uživatele pozemního váhou

Spojte se prosím s [pozemního váhou tým podpory](https://www.landgorilla.com/support/) přidat uživatele na platformě pozemního váhou.
    
### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do klienta pozemního váhou.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit pozemního váhou klienta, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **pozemního váhou klienta**.

    ![Konfigurace jednotného přihlašování](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    


### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici pozemního váhou klienta na přístupovém panelu, které by měl získat automaticky přihlášení k pozemního váhou klientské aplikace.


## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
