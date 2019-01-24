---
title: 'Kurz: Integrace Azure Active Directory s Panopto | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Panopto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 34764a0558aaf5a63c353eb1d1d7180c21ca49c8
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818859"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Kurz: Integrace Azure Active Directory s Panopto

V tomto kurzu se dozvíte, jak integrovat Panopto s Azure Active Directory (Azure AD).

Panopto integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Panopto
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Panopto (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Panopto, potřebujete následující položky:

- Předplatné Azure AD
- Panopto jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Panopto z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-panopto-from-the-gallery"></a>Přidání Panopto z Galerie
Konfigurace integrace Panopto do služby Azure AD, budete muset přidat Panopto z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Panopto z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Panopto**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/panopto-tutorial/tutorial_panopto_search.png)

1. Na panelu výsledků vyberte **Panopto**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/panopto-tutorial/tutorial_panopto_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Panopto podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Panopto je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Panopto potřeba navázat.

V Panopto, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Panopto, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Panopto](#creating-a-panopto-test-user)**  – Pokud chcete mít protějšek Britta Simon Panopto, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Panopto.

**Ke konfiguraci Azure AD jednotné přihlašování s Panopto, proveďte následující kroky:**

1. Na webu Azure Portal na **Panopto** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/panopto-tutorial/tutorial_panopto_samlbase.png)

1. Na **Panopto domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/panopto-tutorial/tutorial_panopto_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.panopto.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Panopto klienta](mailto:support@panopto.com‎) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/panopto-tutorial/tutorial_panopto_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/panopto-tutorial/tutorial_general_400.png)

1. Na **Panopto konfigurace** klikněte na tlačítko **nakonfigurovat Panopto** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/panopto-tutorial/tutorial_panopto_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Panopto jako správce.

1. Na panelu nástrojů na levé straně klikněte na tlačítko **systému**a potom klikněte na tlačítko **zprostředkovatelé Identity**.
   
   ![Systém](./media/panopto-tutorial/ic777670.png "systému")
1. Klikněte na tlačítko **přidat poskytovatele**.
   
   ![Zprostředkovatelé identity](./media/panopto-tutorial/ic777671.png "zprostředkovatelů Identity")
   
1. V části SAML zprostředkovatele proveďte následující kroky:
   
    ![Konfigurace SaaS](./media/panopto-tutorial/ic777672.png "konfigurace SaaS")
    
    a. Z **typ zprostředkovatele** seznamu vyberte **SAML20**.    
    
    b. V **název Instance** textového pole zadejte název instance.

    c. V **popisný** textového pole zadejte popisný název.
    
    d. V **odraz adresa Url stránky** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    e. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    f. Otevřete certifikát kódováním base-64, který jste si stáhli z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **veřejný klíč** textového pole.

1. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/panopto-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/panopto-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/panopto-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/panopto-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-panopto-test-user"></a>Vytvoření zkušebního uživatele Panopto

Neexistuje žádná položka akce konfigurace zřizování uživatelů pro Panopto.  
Přiřazený uživatel se pokusí přihlásit k Panopto pomocí přístupového panelu, Panopto kontroluje, zda uživatel existuje.  

Pokud není žádný uživatelský účet k dispozici dosud, vytvoří se automaticky podle Panopto.

>[!NOTE]
>Můžete použít jakékoli jiné Panopto uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Panopto zřízení uživatelských účtů služby Azure AD.
>
>

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Panopto použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Panopto Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Panopto**.

    ![Konfigurace jednotného přihlašování](./media/panopto-tutorial/tutorial_panopto_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Panopto na přístupovém panelu, měli byste obdržet automaticky přihlašovací stránku Panopto aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/panopto-tutorial/tutorial_general_01.png
[2]: ./media/panopto-tutorial/tutorial_general_02.png
[3]: ./media/panopto-tutorial/tutorial_general_03.png
[4]: ./media/panopto-tutorial/tutorial_general_04.png

[100]: ./media/panopto-tutorial/tutorial_general_100.png

[200]: ./media/panopto-tutorial/tutorial_general_200.png
[201]: ./media/panopto-tutorial/tutorial_general_201.png
[202]: ./media/panopto-tutorial/tutorial_general_202.png
[203]: ./media/panopto-tutorial/tutorial_general_203.png

