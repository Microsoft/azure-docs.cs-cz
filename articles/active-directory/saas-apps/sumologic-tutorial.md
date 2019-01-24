---
title: 'Kurz: Integrace Azure Active Directory s SumoLogic | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 53a16977c74ebbf9e955ddd8e9d43315d71f84f6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808965"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Kurz: Integrace Azure Active Directory s SumoLogic

V tomto kurzu se dozvíte, jak integrovat SumoLogic s Azure Active Directory (Azure AD).

SumoLogic integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SumoLogic
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SumoLogic (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SumoLogic, potřebujete následující položky:

- Předplatné Azure AD
- SumoLogic jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SumoLogic z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sumologic-from-the-gallery"></a>Přidání SumoLogic z Galerie
Konfigurace integrace SumoLogic do služby Azure AD, budete muset přidat SumoLogic z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SumoLogic z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **SumoLogic**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sumologic-tutorial/tutorial_sumologic_search.png)

1. Na panelu výsledků vyberte **SumoLogic**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SumoLogic podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SumoLogic je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SumoLogic potřeba navázat.

V SumoLogic, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SumoLogic, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SumoLogic](#creating-a-sumologic-test-user)**  – Pokud chcete mít protějšek Britta Simon SumoLogic, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SumoLogic.

**Ke konfiguraci Azure AD jednotné přihlašování s SumoLogic, proveďte následující kroky:**

1. Na webu Azure Portal na **SumoLogic** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

1. Na **SumoLogic domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.SumoLogic.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory SumoLogic klienta](https://www.sumologic.com/contact-us/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sumologic-tutorial/tutorial_general_400.png)

1. Na **SumoLogic konfigurace** klikněte na tlačítko **nakonfigurovat SumoLogic** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti SumoLogic jako správce.

1. Přejděte na **spravovat \> zabezpečení**.
   
    ![Správa](./media/sumologic-tutorial/ic778556.png "spravovat")

1. Klikněte na tlačítko **SAML**.
   
    ![Nastavení globální zabezpečení](./media/sumologic-tutorial/ic778557.png "nastavení globální zabezpečení")

1. Z **vyberte konfiguraci, nebo vytvořte novou** vyberte **Azure AD**a potom klikněte na tlačítko **konfigurovat**.
   
    ![Konfigurace SAML 2.0](./media/sumologic-tutorial/ic778558.png "konfigurace SAML 2.0")

1. Na **konfigurace SAML 2.0** dialogového okna, proveďte následující kroky:
   
    ![Konfigurace SAML 2.0](./media/sumologic-tutorial/ic778559.png "konfigurace SAML 2.0")
   
    a. V **název konfigurace** textové pole, typ **Azure AD**. 

    b. Vyberte **režim ladění**.

    c. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 

    d. V **ověřovací adresy URL požadavku** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    e. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát X.509** textového pole.

    f. Jako **atribut e-mailové**vyberte **předmětu pomocí SAML**.  

    g. Vyberte **konfigurace přihlášení zahájené SP**.

    h. V **přihlašovací cestu** textové pole, typ **Azure** a klikněte na tlačítko **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sumologic-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sumologic-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sumologic-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sumologic-test-user"></a>Vytvoření zkušebního uživatele SumoLogic

Chcete-li povolit uživatele Azure AD se přihlaste k SumoLogic, musí být poskytnuty k SumoLogic.  

* V případě SumoLogic zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **SumoLogic** tenanta.

1. Přejděte na **spravovat \> uživatelé**.
   
    ![Uživatelé](./media/sumologic-tutorial/ic778561.png "uživatelů")

1. Klikněte na tlačítko **Add** (Přidat).
   
    ![Uživatelé](./media/sumologic-tutorial/ic778562.png "uživatelů")

1. Na **nového uživatele** dialogového okna, proveďte následující kroky:
   
    ![Nový uživatel](./media/sumologic-tutorial/ic778563.png "nového uživatele") 
 
    a. Související informace účtu Azure AD, které chcete zřídit do typu **křestní jméno**, **příjmení**, a **e-mailu** textových polí.
  
    b. Vyberte roli.
  
    c. Jako **stav**vyberte **aktivní**.
  
    d. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné SumoLogic uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných SumoLogic uživatelským účtům, zřídit AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SumoLogic použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit SumoLogic Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SumoLogic**.

    ![Konfigurace jednotného přihlašování](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici SumoLogic na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SumoLogic.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png

