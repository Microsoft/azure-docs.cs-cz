---
title: 'Kurz: Integrace Azure Active Directory s RunMyProcess | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 4060e385fabe10490c6ccfe77a4f67e705a1d0d0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158787"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Kurz: Integrace Azure Active Directory s RunMyProcess

V tomto kurzu se dozvíte, jak integrovat RunMyProcess s Azure Active Directory (Azure AD).

RunMyProcess integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k RunMyProcess
- Můžete povolit uživatelům, aby automaticky získat přihlášení k RunMyProcess (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RunMyProcess, potřebujete následující položky:

- Předplatné Azure AD
- RunMyProcess jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady:[nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání RunMyProcess z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-runmyprocess-from-the-gallery"></a>Přidání RunMyProcess z Galerie
Konfigurace integrace RunMyProcess do služby Azure AD, budete muset přidat RunMyProcess z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat RunMyProcess z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **RunMyProcess**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

1. Na panelu výsledků vyberte **RunMyProcess**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí RunMyProcess podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v RunMyProcess je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v RunMyProcess potřeba navázat.

V RunMyProcess, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RunMyProcess, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele RunMyProcess](#creating-a-runmyprocess-test-user)**  – Pokud chcete mít protějšek Britta Simon RunMyProcess, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci RunMyProcess.

**Ke konfiguraci Azure AD jednotné přihlašování s RunMyProcess, proveďte následující kroky:**

1. Na webu Azure Portal na **RunMyProcess** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

1. Na **RunMyProcess domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory RunMyProcess klienta](mailto:support@runmyprocess.com) má být získána hodnota. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/runmyprocess-tutorial/tutorial_general_400.png)

1. Na **RunMyProcess konfigurace** klikněte na tlačítko **nakonfigurovat RunMyProcess** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi RunMyProcess jako správce.

1. V levém navigačním panelu klikněte na tlačítko **účet** a vyberte **konfigurace**.
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Přejděte na **metodu ověřování** části a proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování na straně aplikace](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Jako **metoda**vyberte **jednotné přihlašování s Samlv2**. 

    b. V **jednotného přihlašování k přesměrování** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **odhlášení přesměrovat** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. V **formát Id názvu** textového pole zadejte hodnotu **formát názvu identifikátor** jako **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: emailAddress**.

    e. Obsah staženého souboru zkopírujte a vložte ho do **certifikát** textového pole. 
 
    f. Klikněte na tlačítko **Uložit** ikonu.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/runmyprocess-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/runmyprocess-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/runmyprocess-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Vytvoření zkušebního uživatele RunMyProcess

Chcete-li povolit uživatele Azure AD se přihlaste k RunMyProcess, musí být poskytnuty do RunMyProcess. V případě RunMyProcess zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti RunMyProcess jako správce.

1. Klikněte na tlačítko **účet** a vyberte **uživatelé** v levém navigačním panelu klikněte **nového uživatele**.
   
    ![Nový uživatel](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "nového uživatele")

1. V **uživatelská nastavení** části, proveďte následující kroky:
   
    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "profilu") 
  
    a. Typ **název** a **e-mailu** platný Azure AD účet ke zřízení do související textových polí. 

    b. Vyberte **integrovaného vývojového prostředí jazyka**, **jazyk**, a **profilu**. 

    c. Vyberte **Odeslat mi e-mail vytvoření účtu**. 

    d. Klikněte na **Uložit**.
   
    >[!NOTE]
    >Můžete použít jakékoli jiné RunMyProcess uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných RunMyProcess ke zřízení služby Azure Active Directory uživatelské účty. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k RunMyProcess použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit RunMyProcess Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **RunMyProcess**.

    ![Konfigurace jednotného přihlašování](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici RunMyProcess na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci RunMyProcess.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

