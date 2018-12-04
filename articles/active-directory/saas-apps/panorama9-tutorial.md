---
title: 'Kurz: Integrace Azure Active Directory se službou Panorama9 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: ad2a9dba54df3ae7a3e2604437c8a65c88dcca94
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850886"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Kurz: Integrace Azure Active Directory se službou Panorama9

V tomto kurzu se dozvíte, jak integrovat Panorama9 s Azure Active Directory (Azure AD).

Panorama9 integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Panorama9
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Panorama9 (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Panorama9, potřebujete následující položky:

- Předplatné Azure AD
- Panorama9 jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Panorama9 z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-panorama9-from-the-gallery"></a>Přidání Panorama9 z Galerie
Konfigurace integrace Panorama9 do služby Azure AD, budete muset přidat Panorama9 z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Panorama9 z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Panorama9**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/panorama9-tutorial/tutorial_panorama9_search.png)

1. Na panelu výsledků vyberte **Panorama9**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Panorama9 podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Panorama9 je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Panorama9 potřeba navázat.

V Panorama9, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Panorama9, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Panorama9](#creating-a-panorama9-test-user)**  – Pokud chcete mít protějšek Britta Simon Panorama9, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Panorama9.

**Ke konfiguraci Azure AD jednotné přihlašování s Panorama9, proveďte následující kroky:**

1. Na webu Azure Portal na **Panorama9** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

1. Na **Panorama9 domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://dashboard.panorama9.com/saml/access/3262`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Panorama9 klienta](https://support.panorama9.com) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurace jednotného přihlašování](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/panorama9-tutorial/tutorial_general_400.png)

1. Na **Panorama9 konfigurace** klikněte na tlačítko **nakonfigurovat Panorama9** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Panorama9.

1. Na panelu nástrojů v horní části klikněte na tlačítko **spravovat**a potom klikněte na tlačítko **rozšíření**.
   
   ![Rozšíření](./media/panorama9-tutorial/ic790023.png "rozšíření")
1. Na **rozšíření** dialogového okna, klikněte na tlačítko **Single Sign-On**.
   
   ![Jednotné přihlašování](./media/panorama9-tutorial/ic790024.png "jednotného přihlašování")
1. V **nastavení** části, proveďte následující kroky:
   
   ![Nastavení](./media/panorama9-tutorial/ic790025.png "nastavení")
   
    a. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
   
    b. V **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnota certifikátu, který jste zkopírovali z portálu Azure portal.    
         
1. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/panorama9-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/panorama9-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/panorama9-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-panorama9-test-user"></a>Vytvoření zkušebního uživatele Panorama9

Chcete-li povolit uživatele Azure AD k přihlášení do Panorama9, musí být poskytnuty do Panorama9.  

V případě Panorama9 zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Panorama9** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **spravovat**a potom klikněte na tlačítko **uživatelé**.
   
  ![Uživatelé](./media/panorama9-tutorial/ic790027.png "uživatelů")

1. V sekci uživatelé klikněte na tlačítko **+** pro přidání nového uživatele.

 ![Uživatelé](./media/panorama9-tutorial/ic790028.png "uživatelů")

1. Přejděte do části data uživatele, zadejte e-mailovou adresu platného uživatele Azure Active Directory, kterým chcete přidělit do **e-mailu** textového pole.

1. Přijďte na sekci uživatelé klikněte na tlačítko **Uložit**.
   
> [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Panorama9 použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Panorama9 Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Panorama9**.

    ![Konfigurace jednotného přihlašování](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Panorama9 na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Panorama9.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

