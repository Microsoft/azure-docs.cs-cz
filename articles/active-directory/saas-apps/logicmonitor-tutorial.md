---
title: 'Kurz: Integrace Azure Active Directory s LogicMonitor | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: jeedes
ms.openlocfilehash: e8cf814399661c62d38b0d95a0074a2d36c9b4b4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187636"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Kurz: Integrace Azure Active Directory s LogicMonitor

V tomto kurzu se dozvíte, jak integrovat LogicMonitor s Azure Active Directory (Azure AD).

LogicMonitor integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LogicMonitor
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LogicMonitor (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LogicMonitor, potřebujete následující položky:

- Předplatné Azure AD
- LogicMonitor jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LogicMonitor z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-logicmonitor-from-the-gallery"></a>Přidání LogicMonitor z Galerie
Konfigurace integrace LogicMonitor do služby Azure AD, budete muset přidat LogicMonitor z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LogicMonitor z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LogicMonitor**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/logicmonitor-tutorial/tutorial_logicmonitor_search.png)

1. Na panelu výsledků vyberte **LogicMonitor**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí LogicMonitor podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v LogicMonitor je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LogicMonitor potřeba navázat.

V LogicMonitor, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LogicMonitor, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LogicMonitor](#creating-a-logicmonitor-test-user)**  – Pokud chcete mít protějšek Britta Simon LogicMonitor, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LogicMonitor.

**Ke konfiguraci Azure AD jednotné přihlašování s LogicMonitor, proveďte následující kroky:**

1. Na webu Azure Portal na **LogicMonitor** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

1. Na **LogicMonitor domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.logicmonitor.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory LogicMonitor klienta](https://www.logicmonitor.com/contact/) k získání těchto hodnot. 
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/logicmonitor-tutorial/tutorial_general_400.png)

1. Přihlaste se k vaší **LogicMonitor** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/logicmonitor-tutorial/ic790052.png "nastavení")

1. V navigačním bat na levé straně, klikněte na tlačítko **jednotného přihlašování**
   
    ![Jednotné přihlašování](./media/logicmonitor-tutorial/ic790053.png "jednotného přihlašování")

1. V **nastavení jednotného přihlašování (SSO)** části, proveďte následující kroky:
   
    ![Jednotné přihlašování – nastavení](./media/logicmonitor-tutorial/ic790054.png "jednotné přihlašování – nastavení")
   
    a. Vyberte **povolit jednotné přihlašování**.

    b. Jako **výchozí přiřazení Role**vyberte **jen pro čtení**.
   
    c. Otevřete soubor stažený metadat v programu Poznámkový blok a vložte obsah souboru do **metadat zprostředkovatele Identity** textového pole.
   
    d. Klikněte na tlačítko **uložit změny**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/logicmonitor-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/logicmonitor-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/logicmonitor-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/logicmonitor-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Vytvoření zkušebního uživatele LogicMonitor

Pro uživatele Azure AD bude moct přihlásit musí být zřízená LogicMonitor aplikace pomocí jejich uživatelská jména Azure Active Directory.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se na web společnosti LogicMonitor jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **rolí a uživatelů**.
   
    ![Rolí a uživatelů](./media/logicmonitor-tutorial/ic790056.png "rolí a uživatelů")

1. Klikněte na tlačítko **Add** (Přidat).

1. V **přidat účet** části, proveďte následující kroky:
   
    ![Přidat účet](./media/logicmonitor-tutorial/ic790057.png "přidat účet")
   
    a. Typ **uživatelské jméno**, **e-mailu**, **heslo**, a **zadejte znovu heslo** hodnoty uživatele Azure Active Directory, které chcete zřídit do související textových polí.
   
    b. Vyberte **role**, **zobrazit oprávnění**a **stav**.
   
    c. Klikněte na **Submit** (Odeslat).

>[!NOTE]
>Můžete použít jakékoli jiné LogicMonitor uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných LogicMonitor ke zřízení služby Azure Active Directory uživatelské účty. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k LogicMonitor použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit LogicMonitor Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **LogicMonitor**.

    ![Konfigurace jednotného přihlašování](./media/logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.
 
Po kliknutí na dlaždici LogicMonitor na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci LogicMonitor.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/logicmonitor-tutorial/tutorial_general_203.png

