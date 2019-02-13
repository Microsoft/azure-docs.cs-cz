---
title: 'Kurz: Integrace Azure Active Directory s Halogen softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Halogen softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1ef8db71a6a9765eac4ec6ac8cae1d731e296b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205881"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Kurz: Integrace Azure Active Directory s Halogen softwaru

V tomto kurzu se dozvíte, jak integrovat Halogen Software s Azure Active Directory (Azure AD).

Integrace softwaru Halogen s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k softwaru Halogen
- Uživatele, aby automaticky získat přihlášení k softwaru Halogen (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Halogen softwaru, potřebujete následující položky:

- Předplatné Azure AD
- Halogen jednotné přihlašování povoleno předplatné softwaru

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Halogen softwaru z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-halogen-software-from-the-gallery"></a>Přidání Halogen softwaru z Galerie

Konfigurace integrace Halogen softwaru do služby Azure AD, budete muset přidat Halogen Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Halogen softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Halogen softwaru**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

1. Na panelu výsledků vyberte **Halogen softwaru**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Halogen Software založený na uživateli test "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Halogen softwaru je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské Halogen softwaru je potřeba navázat.

V softwaru Halogen přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Halogen softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele softwaru Halogen](#creating-a-halogen-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Halogen Software, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Halogen softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Halogen softwaru, proveďte následující kroky:**

1. Na webu Azure Portal na **Halogen softwaru** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

1. Na **Halogen softwaru domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://global.hgncloud.com/<companyname>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Halogen softwarového klienta](https://support.halogensoftware.com/) k získání těchto hodnot. 
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/halogen-software-tutorial/tutorial_general_400.png)

1. V jiném okně prohlížeče, přihlašování k vaší **Halogen softwaru** aplikaci jako správce.

1. Klikněte na tlačítko **možnosti** kartu. 
   
    ![Co je služba Azure AD Connect][12]

1. V levém navigačním podokně klikněte na tlačítko **konfigurace SAML**. 
   
    ![Co je služba Azure AD Connect][13]

1. Na **konfigurace SAML** stránce, proveďte následující kroky: 

    ![Co je služba Azure AD Connect][14]

     a. Jako **jedinečný identifikátor**vyberte **NameID**.

     b. Jako **mapy jedinečný identifikátor pro**vyberte **uživatelské jméno**.
  
     c. Nahrát soubor stažený metadat, klikněte na tlačítko **Procházet** a vyberte soubor a potom **nahrát soubor**.
 
     d. Chcete-li otestovat konfiguraci, klikněte na tlačítko **spustit Test**. 
    
    >[!NOTE]
    >Budete muset čekat na zprávu "*SAML test je dokončen. Zavřete prosím toto okno*". Zavřete okno otevřené. **Povolit SAML** zaškrtávací políčko je povoleno pouze v případě test byl dokončen. 
     
     e. Vyberte **povolit SAML**.
    
     f. Klikněte na tlačítko **uložit změny**. 

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/halogen-software-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/halogen-software-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/halogen-software-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. V **název** textového pole Název zadejte jako **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-halogen-software-test-user"></a>Vytvoření zkušebního uživatele Halogen softwaru

Cílem této části je vytvořte uživatele Britta Simon Halogen softwaru.

**Vytvořte uživatele v softwaru Halogen jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k vaší **Halogen softwaru** aplikaci jako správce.

1. Klikněte na tlačítko **uživatele Center** kartu a potom klikněte na tlačítko **Create User**.
   
    ![Co je služba Azure AD Connect][300]  

1. Na **nového uživatele** dialogového okna stránky, proveďte následující kroky:
   
    ![Co je služba Azure AD Connect][301]

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.
    
    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**. 

    c. V **uživatelské jméno** textové pole, typ **Britta Simon**, uživatelské jméno jako na webu Azure portal.

    d. V **heslo** textového pole zadejte heslo pro Britta.
    
    e. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k softwaru Halogen.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Halogen softwaru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Halogen softwaru**.

    ![Konfigurace jednotného přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Halogen softwaru na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Halogen softwaru.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
