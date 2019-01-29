---
title: 'Kurz: Integrace Azure Active Directory s Sugar CRM | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 857dc07239610ff460f8b43a951633062b690ab9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171180"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Kurz: Integrace Azure Active Directory s Sugar CRM

V tomto kurzu se dozvíte, jak integrovat Sugar CRM s Azure Active Directory (Azure AD).

Integrace s Azure AD Sugar CRM poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sugar CRM
- Uživatele, aby automaticky získat přihlášení k Sugar CRM (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sugar CRM, budete potřebovat následující položky:

- Předplatné Azure AD
- Sugar CRM jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sugar CRM z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sugar-crm-from-the-gallery"></a>Přidání Sugar CRM z Galerie
Ke konfiguraci integrace Sugar CRM do služby Azure AD, budete muset přidat Sugar CRM na váš seznam spravovaných aplikací SaaS z galerie.

**Sugar CRM přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Sugar CRM**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. Na panelu výsledků vyberte **Sugar CRM**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Sugar CRM na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek CRM Sugar je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské CRM Sugar je potřeba navázat.

Sugar CRM, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sugar CRM, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Sugar CRM](#creating-a-sugar-crm-test-user)**  – Pokud chcete mít protějšek Britta Simon Sugar CRM, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Sugar CRM.

**Ke konfiguraci Azure AD jednotné přihlašování s Sugar CRM, proveďte následující kroky:**

1. Na webu Azure Portal na **Sugar CRM** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. Na **Sugar CRM domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta CRM Sugar](https://support.sugarcrm.com/) má být získána hodnota. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. Na **Sugar CRM konfigurace** klikněte na tlačítko **konfigurace CRM Sugar** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Sugar CRM jako správce.

1. Přejděte na **správce**.
   
    ![Správce](./media/sugarcrm-tutorial/ic795888.png "správce")

1. V **správu** klikněte na tlačítko **Správa hesel**.
   
    ![Správa](./media/sugarcrm-tutorial/ic795889.png "správy")

1. Vyberte **povolit ověřování SAML**.
   
    ![Správa](./media/sugarcrm-tutorial/ic795890.png "správy")

1. V **ověřování SAML** části, proveďte následující kroky:
   
    ![Ověřování SAML](./media/sugarcrm-tutorial/ic795891.png "ověřování SAML")  
 
    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
  
    b. V **adresa URL cíle na úrovni služby** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.
  
    c. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát X.509** textového pole.
  
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Vytvoření zkušebního uživatele Sugar CRM

Chcete-li povolit Azure AD uživatelům přihlášení do Sugar CRM, musí být zřízená s Sugar CRM.

V případě Sugar CRM je zřizování úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Sugar CRM** společnosti serveru jako správce.

1. Přejděte na **správce**.
   
    ![Správce](./media/sugarcrm-tutorial/ic795888.png "správce")

1. V **správu** klikněte na tlačítko **Správa uživatelů**.
   
    ![Správa](./media/sugarcrm-tutorial/ic795893.png "správy")

1. Přejděte na **uživatelé \> vytvořit nového uživatele**.
   
    ![Vytvoření nového uživatele](./media/sugarcrm-tutorial/ic795894.png "vytvořit nového uživatele")

1. Na **profilu uživatele** kartu, proveďte následující kroky:
   
    ![Nový uživatel](./media/sugarcrm-tutorial/ic795895.png "nového uživatele")

    a. Typ **uživatelské jméno**, **příjmení**, a **e-mailová adresa** platné uživatele Azure Active Directory do související textových polí.
  
1. Jako **stav**vyberte **aktivní**.

1. Na kartě heslo proveďte následující kroky:
   
    ![Nový uživatel](./media/sugarcrm-tutorial/ic795896.png "nového uživatele")

    a. Do souvisejícího textového pole zadejte heslo.

    b. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné Sugar CRM uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Sugar CRM uživatelským účtům, zřídit AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Sugar CRM.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Sugar CRM, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Sugar CRM**.

    ![Konfigurace jednotného přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Sugar CRM na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Sugar CRM.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

