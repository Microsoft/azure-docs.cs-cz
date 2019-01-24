---
title: 'Kurz: Integrace Azure Active Directory s Marketo | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: ac6d7c23c6bb107ce6920cce600a57143dafc62d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816428"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Integrace Azure Active Directory s platformy Marketo

V tomto kurzu se dozvíte, jak integrovat služby Marketo se službou Azure Active Directory (Azure AD).

Integrace platformy Marketo s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k službě Marketo
- Můžete povolit uživatelům, aby automaticky získat přihlášení k službě Marketo (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí služby Marketo, potřebujete následující položky:

- Předplatné Azure AD
- Služby Marketo jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání služby Marketo z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-marketo-from-the-gallery"></a>Přidání služby Marketo z Galerie
Pokud chcete nakonfigurovat integraci platformy Marketo do služby Azure AD, budete muset přidat Marketo z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání služby Marketo z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Marketo**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/marketo-tutorial/tutorial_marketo_search.png)

1. Na panelu výsledků vyberte **Marketo**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí služby Marketo podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek služby marketo je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské služby marketo.

Služby marketo, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby Marketo, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Marketo](#creating-a-marketo-test-user)**  – Pokud chcete mít protějšek Britta Simon služby marketo, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci služby Marketo.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí služby Marketo, proveďte následující kroky:**

1. Na webu Azure Portal na **Marketo** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

1. Na **Marketo domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://saml.marketo.com/sp`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory služby Marketo](http://investors.marketo.com/contactus.cfm) k získání těchto hodnot.
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_general_400.png)

1. Na **Marketo konfigurace** klikněte na tlačítko **nakonfigurovat Marketo** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_configure.png) 

1. K získání Id Munchkin vaší aplikace, přihlaste se k Marketo pomocí přihlašovacích údajů správce a proveďte následující akce:
   
    a. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na tlačítko **Munchkin odkaz**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Zkopírujte Id Munchkin uvedené na obrazovce a dokončení vaší adresy URL odpovědi v Průvodci konfigurací služby Azure AD.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_12.png) 

1. Pokud chcete nakonfigurovat jednotné přihlašování v aplikaci, postupujte následujících kroků:
   
    a. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na tlačítko **Single Sign On**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Chcete-li povolit nastavení SAML, klikněte na tlačítko **upravit** tlačítko.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Povolené** nastavení jednotného přihlašování.
   
    f. Vložit **SAML Entity ID**v **ID vystavitele** textového pole.
   
    g. V **Entity ID** textového pole zadejte adresu URL jako `http://saml.marketo.com/sp`.
   
    h. Vyberte umístění, ID uživatele jako **identifikátor názvu elementu**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Pokud není uživatelský identifikátor hodnotu hlavního názvu uživatele, pak změňte hodnotu atributu kartě.
   
    i. Nahrajte certifikát, který jste si stáhli z Průvodce konfigurací služby Azure AD. **Uložit** nastavení.
   
    j. Upravte nastavení přesměrování stránky.
   
    k. Vložit **SAML jednotné přihlašování – adresa URL služby** v **přihlašovací adresa URL** textového pole.
   
    l. Vložit **odhlašování URL** v **odhlašovací adresa URL** textového pole.
   
    m. V **chybová adresa URL**, kopie vašeho **adresu URL instance služby Marketo** a klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_10.png)

1. Pokud chcete povolit jednotné přihlašování pro uživatele, proveďte následující akce:
   
    a. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte **zabezpečení** nabídky a klikněte na tlačítko **nastavení přihlášení**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Zkontrolujte **vyžadovat jednotné přihlašování** možnost a **Uložit** nastavení.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/marketo-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/marketo-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/marketo-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/marketo-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-marketo-test-user"></a>Vytvoření zkušebního uživatele služby Marketo

V této části vytvořte uživatele Britta Simon služby marketo. postupujte podle těchto kroků a vytvořte uživatele v platformy Marketo.

1. Přihlaste se k aplikaci služby Marketo pomocí přihlašovacích údajů správce.

1. Klikněte na tlačítko **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 

1. Přejděte **zabezpečení** nabídky a klikněte na tlačítko **uživatelů a rolí**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_19.png)  

1. Klikněte na tlačítko **pozvat nového uživatele** odkaz na kartě Uživatelé
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_15.png) 

1. V Průvodci pozvat nového uživatele zadejte následující informace
   
    a. Zadejte uživatele **e-mailu** adresu do textového pole
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Zadejte **křestní jméno** do textového pole
   
    c. Zadejte **příjmení** do textového pole
   
    d. Klikněte na **Další**

1. V **oprávnění** kartu, vyberte **userRoles** a klikněte na tlačítko **další**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_17.png)
1. Klikněte na tlačítko **odeslat** tlačítko si pošlete e-mailové pozvánce uživatele
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_18.png)

1. Uživatel obdrží e-mailové oznámení a klikněte na odkaz a změnit heslo k aktivaci účtu. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k službě Marketo.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Marketo, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Marketo**.

    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby Marketo na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci služby Marketo.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

