---
title: 'Kurz: Integrace Azure Active Directory se službou Syncplicity | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 3b74ca178d3bf380dc759ce0325d4047891a39d3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422388"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Kurz: Integrace Azure Active Directory se službou Syncplicity

V tomto kurzu se dozvíte, jak integrovat Syncplicity s Azure Active Directory (Azure AD).

Syncplicity integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Syncplicity
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Syncplicity (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Syncplicity, potřebujete následující položky:

- S předplatným služby Azure AD
- Syncplicity jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Syncplicity z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-syncplicity-from-the-gallery"></a>Přidání Syncplicity z Galerie
Konfigurace integrace Syncplicity do služby Azure AD, budete muset přidat Syncplicity z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Syncplicity z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Syncplicity**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. Na panelu výsledků vyberte **Syncplicity**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Syncplicity podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Syncplicity je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Syncplicity potřeba navázat.

V Syncplicity, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Syncplicity, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Syncplicity](#creating-a-syncplicity-test-user)**  – Pokud chcete mít protějšek Britta Simon Syncplicity, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Syncplicity.

**Ke konfiguraci Azure AD jednotné přihlašování s Syncplicity, proveďte následující kroky:**

1. Na webu Azure Portal na **Syncplicity** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. Na **Syncplicity domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.syncplicity.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Syncplicity klienta](https://www.syncplicity.com/contact-us) k získání těchto hodnot. 
 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/syncplicity-tutorial/tutorial_general_400.png)

1. Na **Syncplicity konfigurace** klikněte na tlačítko **nakonfigurovat Syncplicity** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Přihlaste se k vaší **Syncplicity** tenanta.

1. V nabídce v horní části klikněte na tlačítko **správce**vyberte **nastavení**a potom klikněte na tlačítko **vlastní domény a jednotné přihlašování**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na **jednotné přihlašování (SSO)** dialogového okna stránky, proveďte následující kroky:
   
    ![Jednotné přihlašování \(jednotného přihlašování\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. V **vlastní domény** textového pole zadejte název vaší domény.
  
    b. Vyberte **povolené** jako **jednotné přihlašování – stav**.

    c. V **Entity Id** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.

    d. V **přihlašovací adresa URL stránky** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    e. V **adresy URL odhlašovací stránky** vložit do textového pole **odhlašování URL** zkopírovanou z webu Azure portal.

    f. V **certifikát poskytovatele Identity**, klikněte na tlačítko **zvolte soubor**a pak nahrajte certifikát, který jste si stáhli z portálu Azure portal. 

    g. Klikněte na tlačítko **ULOŽTE změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-syncplicity-test-user"></a>Vytvoření zkušebního uživatele Syncplicity
Pro uživatele AAD, bude moct přihlásit musí být zřízená Syncplicity aplikace. Tato část popisuje, jak v Syncplicity vytvořte uživatelské účty AAD.

**Ke zřízení uživatelského účtu pro Syncplicity, proveďte následující kroky:**

1. Přihlaste se k vaší **Syncplicity** tenanta (například: `https://company.Syncplicity.com`).

1. Klikněte na tlačítko **správce** a vyberte **uživatelské účty**.

1. Klikněte na tlačítko **přidání uživatele**.
   
    ![Správa uživatelů](./media/syncplicity-tutorial/ic769764.png "Správa uživatelů")

1. Typ **e-mailových adres** účet AAD, které chcete zřídit, vyberte **uživatele** jako **Role**a potom klikněte na tlačítko **Další**.
   
    ![Informace o účtu](./media/syncplicity-tutorial/ic769765.png "informace o účtu")
   
    >[!NOTE]
    >Držitel účtu AAD získá e-mailu včetně odkazu na potvrzení a aktivovat účet. 
    > 

1. Vyberte skupinu ve vaší společnosti, nový uživatel by měl stane členem, a pak klikněte na **Další**.
   
    ![Členství ve skupině](./media/syncplicity-tutorial/ic769772.png "členství ve skupině")
   
    >[!NOTE]
    >Pokud nejsou k dispozici žádné skupiny uvedeny, klikněte na tlačítko **Další**. 
    > 

1. Vyberte složky, které chcete umístit pod kontrolou společnosti Syncplicity na počítači uživatele a potom klikněte na **Další**.
   
    ![Složky Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity složek")

>[!NOTE]
>Můžete použít jakékoli jiné Syncplicity uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Syncplicity uživatelským účtům, zřídit AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Syncplicity použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Syncplicity Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Syncplicity**.

    ![Konfigurace jednotného přihlašování](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Syncplicity na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Syncplicity.
## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

