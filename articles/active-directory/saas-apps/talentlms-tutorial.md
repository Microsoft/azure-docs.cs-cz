---
title: 'Kurz: Integrace Azure Active Directory s TalentLMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b5c4779605922bf6d523a9bc6e65e4d9a27f32a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163712"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Kurz: Integrace Azure Active Directory s TalentLMS

V tomto kurzu se dozvíte, jak integrovat TalentLMS s Azure Active Directory (Azure AD).

TalentLMS integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TalentLMS
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TalentLMS (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TalentLMS, potřebujete následující položky:

- Předplatné Azure AD
- TalentLMS jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TalentLMS z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-talentlms-from-the-gallery"></a>Přidání TalentLMS z Galerie
Konfigurace integrace TalentLMS do služby Azure AD, budete muset přidat TalentLMS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TalentLMS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **TalentLMS**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. Na panelu výsledků vyberte **TalentLMS**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s TalentLMS podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v TalentLMS je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TalentLMS potřeba navázat.

V TalentLMS, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TalentLMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele TalentLMS](#creating-a-talentlms-test-user)**  – Pokud chcete mít protějšek Britta Simon TalentLMS, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci TalentLMS.

**Ke konfiguraci Azure AD jednotné přihlašování s TalentLMS, proveďte následující kroky:**

1. Na webu Azure Portal na **TalentLMS** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. Na **TalentLMS domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.TalentLMSapp.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory TalentLMS klienta](https://www.talentlms.com/contact) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnotu z certifikátu.

    ![Konfigurace jednotného přihlašování](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/talentlms-tutorial/tutorial_general_400.png)

1. Na **TalentLMS konfigurace** klikněte na tlačítko **nakonfigurovat TalentLMS** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti TalentLMS jako správce.

1. V **účet a nastavení** klikněte na tlačítko **uživatelé** kartu.
   
    ![Účet a nastavení](./media/talentlms-tutorial/IC777296.png "účet a nastavení")

1. Klikněte na tlačítko **jednotné přihlašování (SSO)**,

1. V části jednotného přihlašování proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/talentlms-tutorial/IC777297.png "jednotného přihlašování")   

    a. Z **typ integrace jednotného přihlašování** seznamu vyberte **SAML 2.0**.

    b. V **zprostředkovatele Identity (IDP)** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.
 
    c. Vložit **kryptografický otisk** hodnotu z webu Azure portal do **otisku certifikátu** textového pole.    

    d.  V **přihlašovací adresa URL vzdáleného úložiště** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
 
    e. V **Vzdálená adresa URL pro odhlášení** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    f. Vyplňte následující údaje: 

    * V **TargetedID** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * V **křestní jméno** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * V **příjmení** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
1. Klikněte na **Uložit**.
 
> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/talentlms-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/talentlms-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/talentlms-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-talentlms-test-user"></a>Vytvoření zkušebního uživatele TalentLMS

Přihlaste se k TalentLMS Azure AD uživatelům umožnit, musí být poskytnuty do TalentLMS. V případě TalentLMS zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **TalentLMS** tenanta.

1. Klikněte na tlačítko **uživatelé**a potom klikněte na tlačítko **přidat uživatele**.

1. Na **přidat uživatele** dialogového okna stránky, proveďte následující kroky:
   
    ![Přidání uživatele](./media/talentlms-tutorial/IC777299.png "přidat uživatele")  

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.
 
    c. V **e-mailová adresa** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    d. Klikněte na tlačítko **přidat uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné TalentLMS uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných TalentLMS uživatelským účtům, zřídit AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k TalentLMS použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit TalentLMS Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **TalentLMS**.

    ![Konfigurace jednotného přihlašování](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici TalentLMS na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci TalentLMS

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png

