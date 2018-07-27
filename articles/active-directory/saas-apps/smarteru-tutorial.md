---
title: 'Kurz: Integrace Azure Active Directory se službou SmarterU | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: e07ed8f131697d6340b899ef76c988aed215556b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283407"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Kurz: Integrace Azure Active Directory se službou SmarterU

V tomto kurzu se dozvíte, jak integrovat SmarterU s Azure Active Directory (Azure AD).

SmarterU integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SmarterU
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SmarterU (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SmarterU, potřebujete následující položky:

- S předplatným služby Azure AD
- SmarterU jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SmarterU z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-smarteru-from-the-gallery"></a>Přidání SmarterU z Galerie
Konfigurace integrace SmarterU do služby Azure AD, budete muset přidat SmarterU z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SmarterU z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **SmarterU**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smarteru-tutorial/tutorial_smarteru_search.png)

5. Na panelu výsledků vyberte **SmarterU**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smarteru-tutorial/tutorial_smarteru_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s SmarterU podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SmarterU je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SmarterU potřeba navázat.

V SmarterU, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SmarterU, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SmarterU](#creating-a-smarteru-test-user)**  – Pokud chcete mít protějšek Britta Simon SmarterU, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SmarterU.

**Ke konfiguraci Azure AD jednotné přihlašování s SmarterU, proveďte následující kroky:**

1. Na webu Azure Portal na **SmarterU** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/smarteru-tutorial/tutorial_smarteru_samlbase.png)

3. Na **SmarterU domény a adresy URL** části, proveďte následující kroky: 

    ![Konfigurace jednotného přihlašování](./media/smarteru-tutorial/tutorial_smarteru_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://www.smarteru.com/`

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/smarteru-tutorial/tutorial_smarteru_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/smarteru-tutorial/tutorial_general_400.png)

6. V okně jiné webové prohlížeče Přihlaste se k webu společnosti SmarterU jako správce.

7. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení účtu**.
   
    ![Nastavení účtu](./media/smarteru-tutorial/accountsettings.png)

8. Na stránce konfigurace účtu postupujte následovně:
   
    ![Externí ověřování](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 
 
      a. Vyberte **povolit externí autorizace**.
  
      b. V **hlavní ovládací prvek Login** vyberte **SmarterU** kartu.
  
      c. V **výchozí přihlašovací** vyberte **SmarterU** kartu.
  
      d. Vyberte **povolit SAML**.
  
      e. Zkopírujte obsah souboru staženého metadat a vložte jej do **metadat zprostředkovatele identity** textového pole.
      
      f. Vyberte **identifikátor atributu/deklarace identity**.
  
      g. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smarteru-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/smarteru-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/smarteru-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/smarteru-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-smarteru-test-user"></a>Vytvoření zkušebního uživatele SmarterU

Přihlaste se k SmarterU Azure AD uživatelům umožnit, musí být poskytnuty do SmarterU.

Při zřizování SmarterU, je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **SmarterU** tenanta.

2. Přejděte na **uživatelé**.

3. V části uživatel proveďte následující kroky:
   
    ![Nový uživatel](./media/smarteru-tutorial/adduser.png)  

    a. Klikněte na tlačítko **+ uživatel**.
    
    b. Zadejte hodnoty vztahujícího se atributu Azure AD uživatelského účtu do následující textová pole: **primární e-mailu**, **ID zaměstnance**, **heslo**, **ověřit Heslo**, **křestní jméno**, **příjmení**.
    
    c. Klikněte na tlačítko **aktivní**. 
    
    d. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné SmarterU uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných SmarterU uživatelským účtům, zřídit AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SmarterU použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit SmarterU Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SmarterU**.

    ![Konfigurace jednotného přihlašování](./media/smarteru-tutorial/tutorial_smarteru_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.
 
Po kliknutí na dlaždici SmarterU na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SmarterU.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smarteru-tutorial/tutorial_general_01.png
[2]: ./media/smarteru-tutorial/tutorial_general_02.png
[3]: ./media/smarteru-tutorial/tutorial_general_03.png
[4]: ./media/smarteru-tutorial/tutorial_general_04.png

[100]: ./media/smarteru-tutorial/tutorial_general_100.png

[200]: ./media/smarteru-tutorial/tutorial_general_200.png
[201]: ./media/smarteru-tutorial/tutorial_general_201.png
[202]: ./media/smarteru-tutorial/tutorial_general_202.png
[203]: ./media/smarteru-tutorial/tutorial_general_203.png

