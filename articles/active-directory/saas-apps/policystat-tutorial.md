---
title: 'Kurz: Integrace Azure Active Directory s PolicyStat | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 456d66b87f626391f6e81ffd7be244381f0978b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155982"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Kurz: Integrace Azure Active Directory s PolicyStat

V tomto kurzu se dozvíte, jak integrovat PolicyStat s Azure Active Directory (Azure AD).

PolicyStat integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PolicyStat
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PolicyStat (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PolicyStat, potřebujete následující položky:

- Předplatné Azure AD
- PolicyStat jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PolicyStat z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-policystat-from-the-gallery"></a>Přidání PolicyStat z Galerie
Konfigurace integrace PolicyStat do služby Azure AD, budete muset přidat PolicyStat z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat PolicyStat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **PolicyStat**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/policystat-tutorial/tutorial_policystat_search.png)

1. Na panelu výsledků vyberte **PolicyStat**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí PolicyStat podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v PolicyStat je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v PolicyStat potřeba navázat.

V PolicyStat, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PolicyStat, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele PolicyStat](#creating-a-policystat-test-user)**  – Pokud chcete mít protějšek Britta Simon PolicyStat, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci PolicyStat.

**Ke konfiguraci Azure AD jednotné přihlašování s PolicyStat, proveďte následující kroky:**

1. Na webu Azure Portal na **PolicyStat** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. Na **PolicyStat domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.policystat.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory PolicyStat klienta](http://www.policystat.com/support/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. Cílem této části se popisují, jak povolit uživatelům ověření pro PolicyStat pomocí svého účtu ve službě Azure AD využívající federaci založené na protokolu SAML.

    Aplikace PolicyStat očekává, že kontrolní výrazy SAML v určitém formátu, které je potřeba přidat vlastní atribut mapování vaší **atributy tokenu SAML** konfigurace.  

     Následující snímek obrazovky ukazuje příklad tohoto objektu.

     ![Atributy](./media/policystat-tutorial/tutorial_policystat_attribute.png "atributy")

1. Chcete-li přidat mapování požadovaný atribut, postupujte následovně:

    | Název atributu    |   Hodnota atributu |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. V **název atributu** textové pole, typ **uid**.

    c. V **hodnota atributu** textové pole, vyberte **ExtractMailPrefix()**.    
   
    d. Z **e-mailu** seznamu vyberte **User.mail**.
    
    e. Klikněte na tlačítko **Ok**

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti PolicyStat.

1. Klikněte na tlačítko **správce** kartu a potom klikněte na tlačítko **Konfigurace jednotného přihlašování** v levém navigačním podokně.
   
    ![Správce nabídek](./media/policystat-tutorial/ic808633.png "správce nabídek")

1. V **nastavení** vyberte **povolit jednotné přihlašování – integrace**.
   
    ![Jednotné přihlašování](./media/policystat-tutorial/ic808634.png "jednotného přihlašování")

1. Klikněte na tlačítko **konfigurace atributů**a pak na **konfigurace atributů** části, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/policystat-tutorial/ic808635.png "jednotného přihlašování")
   
    a. V **atribut uživatelského jména** textové pole, typ **uid**.

    b. V **křestní jméno atributu** textové pole, typ **firstname** uživatele **Britta**.

    c. V **poslední název atributu** textové pole, typ **lastname** uživatele **Simon**.

    d. V **atribut e-mailové** textové pole, typ **emailaddress** uživatele **BrittaSimon@contoso.com**.

    e. Klikněte na tlačítko **uložit změny**.

1. Klikněte na tlačítko **Your metadat zprostředkovatele identity**a pak na **Your metadat zprostředkovatele identity** části, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/policystat-tutorial/ic808636.png "jednotného přihlašování")
   
    a. Otevřete soubor stažený metadat, zkopírujte jeho obsah a vložte jej do **Your metadat zprostředkovatele Identity** textového pole.

    b. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/policystat-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/policystat-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/policystat-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/policystat-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-policystat-test-user"></a>Vytvoření zkušebního uživatele PolicyStat

Chcete-li povolit uživatele Azure AD k přihlášení do PolicyStat, musí být poskytnuty do PolicyStat.  

PolicyStat podporuje jenom v době zřizování uživatelů. To znamená, že není potřeba ručně přidat uživatele do PolicyStat. Uživatelé se přidají automaticky při jejich první přihlášení pomocí jednotného přihlašování.

>[!NOTE]
>Můžete použít jakékoli jiné PolicyStat uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných PolicyStat zřízení uživatelských účtů služby Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k PolicyStat použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit PolicyStat Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **PolicyStat**.

    ![Konfigurace jednotného přihlašování](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici PolicyStat na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci PolicyStat.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

