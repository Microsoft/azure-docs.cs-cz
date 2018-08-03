---
title: 'Kurz: Integrace Azure Active Directory se službou PerformanceCentre | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 352f674e18eb62b2f3db81209cd7f3b919d52c48
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428175"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Kurz: Integrace Azure Active Directory se službou PerformanceCentre

V tomto kurzu se dozvíte, jak integrovat PerformanceCentre s Azure Active Directory (Azure AD).

PerformanceCentre integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PerformanceCentre
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PerformanceCentre (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PerformanceCentre, potřebujete následující položky:

- S předplatným služby Azure AD
- PerformanceCentre jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PerformanceCentre z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-performancecentre-from-the-gallery"></a>Přidání PerformanceCentre z Galerie
Konfigurace integrace PerformanceCentre do služby Azure AD, budete muset přidat PerformanceCentre z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat PerformanceCentre z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **PerformanceCentre**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_search.png)

1. Na panelu výsledků vyberte **PerformanceCentre**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí PerformanceCentre podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v PerformanceCentre je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v PerformanceCentre potřeba navázat.

V PerformanceCentre, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PerformanceCentre, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele PerformanceCentre](#creating-a-performancecentre-test-user)**  – Pokud chcete mít protějšek Britta Simon PerformanceCentre, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci PerformanceCentre.

**Ke konfiguraci Azure AD jednotné přihlašování s PerformanceCentre, proveďte následující kroky:**

1. Na webu Azure Portal na **PerformanceCentre** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

1. Na **PerformanceCentre domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `http://companyname.performancecentre.com/saml/SSO`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory PerformanceCentre klienta](https://www.performancecentre.com/contact-us/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/performancecentre-tutorial/tutorial_general_400.png)

1. Na **PerformanceCentre konfigurace** klikněte na tlačítko **nakonfigurovat PerformanceCentre** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_configure.png) 

1. Přihlášení k vaší **PerformanceCentre** společnosti serveru jako správce.

1. Na kartě na levé straně klikněte na tlačítko **konfigurovat**.
   
    ![Azure AD jednotné přihlašování][10]

1. Na kartě na levé straně klikněte na tlačítko **různé**a potom klikněte na tlačítko **Single Sign On**.
   
    ![Azure AD jednotné přihlašování][11]

1. Jako **protokol**vyberte **SAML**.
   
    ![Azure AD jednotné přihlašování][12]

1. V poznámkovém bloku otevřete soubor stažený metadat, zkopírujte obsah, vložte jej do **metadat zprostředkovatele Identity** textového pole a potom klikněte na tlačítko **Uložit**.
   
    ![Azure AD jednotné přihlašování][13]

1. Ověřte, že hodnoty **Entity základní adresu URL** a **Entity ID URL** jsou správné.
    
     ![Azure AD jednotné přihlašování][14]

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/performancecentre-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/performancecentre-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/performancecentre-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/performancecentre-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-performancecentre-test-user"></a>Vytvoření zkušebního uživatele PerformanceCentre

Cílem této části je vytvořte uživatele Britta Simon v PerformanceCentre.

**Vytvořte uživatele v PerformanceCentre jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti PerformanceCentre jako správce.

1. V nabídce na levé straně klikněte na tlačítko **Interrelate**a potom klikněte na tlačítko **vytvořit účastníka**.
   
    ![Vytvořit uživatele][400]

1. Na **vztah mezi – vytvoření účastníka** dialogového okna, proveďte následující kroky:
   
    ![Vytvořit uživatele][401]
    
    a. Do související textových polí zadejte povinné atributy pro Britta Simon.
    
    >[!IMPORTANT]
    >Atribut uživatelského jména Haniny v PerformanceCentre musí být stejné jako uživatelské jméno ve službě Azure AD.
    
    b. Vyberte **správce klienta** jako **zvolte roli,**.
    
    c. Klikněte na **Uložit**. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k PerformanceCentre použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit PerformanceCentre Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **PerformanceCentre**.

    ![Konfigurace jednotného přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.  

Po kliknutí na dlaždici PerformanceCentre na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci PerformanceCentre.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png

