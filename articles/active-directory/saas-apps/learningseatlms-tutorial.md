---
title: 'Kurz: Integrace Azure Active Directory s Learning Seat LMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Learning Seat LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bb056fcf-4135-478e-85b1-5015d1f07b85
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: jeedes
ms.openlocfilehash: b0190cd84674abc62ee68f9d0a160a6cb0cd45c0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815510"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-seat-lms"></a>Kurz: Integrace Azure Active Directory s Learning Seat LMS

V tomto kurzu se dozvíte, jak integrovat Learning Seat LMS s Azure Active Directory (Azure AD).

Learning Seat LMS integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Learning Seat LMS
- Uživatele, aby automaticky získat přihlášení k učení LMS pracovní stanici (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Learning Seat LMS, potřebujete následující položky:

- Předplatné Azure AD
- Learning Seat LMS jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Learning Seat LMS z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-learning-seat-lms-from-the-gallery"></a>Přidání Learning Seat LMS z Galerie
Konfigurace integrace Learning Seat LMS do služby Azure AD, budete muset přidat Learning Seat LMS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Learning Seat LMS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Learning Seat LMS**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learningseatlms-tutorial/tutorial_learnconnect_search.png)

1. Na panelu výsledků vyberte **Learning Seat LMS**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Learning Seat LMS podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Learning Seat LMS je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Learning Seat LMS je potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Learning Seat LMS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Learning Seat LMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Learning Seat LMS](#creating-a-learnconnect-test-user)**  – Pokud chcete mít protějšek Britta Simon Learning Seat LMS, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Learning Seat LMS.

**Ke konfiguraci Azure AD jednotné přihlašování s Learning Seat LMS, proveďte následující kroky:**

1. Na webu Azure Portal na **Learning Seat LMS** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/learningseatlms-tutorial/tutorial_learnconnect_samlbase.png)

1. Na **Learning Seat LMS domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/learningseatlms-tutorial/tutorial_learnconnect_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.learningseatlms.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.learningseatlms.com/Account/AssertionConsumerService`

1. Zkontrolujte **zobrazit pokročilé nastavení URL**, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/learningseatlms-tutorial/tutorial_learnconnect_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.learningseatlms.com`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné hodnoty. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Learning Seat](http://help.learningseatlms.com/help) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/learningseatlms-tutorial/tutorial_learnconnect_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/learningseatlms-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Learning Seat LMS** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Learning Seat](http://help.learningseatlms.com/help).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learningseatlms-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/learningseatlms-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/learningseatlms-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/learningseatlms-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-learning-seat-lms-test-user"></a>Vytvoření zkušebního uživatele Learning Seat LMS

V této části vytvoříte uživatele v Learning Seat LMS jako Britta Simon. Kontakt [tým podpory Learning Seat](http://help.learningseatlms.com/help) se všechny informace o uživateli Chcete-li přidat uživatele v aplikaci Learning Seat LMS.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Learning Seat LMS.

![Přiřadit uživatele][200] 

**Přiřadit Learning Seat LMS Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Learning Seat LMS**.

    ![Konfigurace jednotného přihlašování](./media/learningseatlms-tutorial/tutorial_learnconnect_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu. 

Kliknutím na dlaždici Learning Seat LMS na přístupovém panelu, je bude možné automaticky přihlášení k aplikaci Learning Seat LMS. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnconnect-tutorial/tutorial_general_01.png
[2]: ./media/learnconnect-tutorial/tutorial_general_02.png
[3]: ./media/learnconnect-tutorial/tutorial_general_03.png
[4]: ./media/learnconnect-tutorial/tutorial_general_04.png

[100]: ./media/learnconnect-tutorial/tutorial_general_100.png

[200]: ./media/learnconnect-tutorial/tutorial_general_200.png
[201]: ./media/learnconnect-tutorial/tutorial_general_201.png
[202]: ./media/learnconnect-tutorial/tutorial_general_202.png
[203]: ./media/learnconnect-tutorial/tutorial_general_203.png

