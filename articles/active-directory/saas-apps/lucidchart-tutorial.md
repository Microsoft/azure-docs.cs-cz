---
title: 'Kurz: Integrace Azure Active Directory se službou Lucidchart | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 45dbf350bc874d48b077ba8f7d67819eff741df2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448036"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Kurz: Integrace Azure Active Directory se službou Lucidchart

V tomto kurzu se dozvíte, jak integrovat Lucidchart s Azure Active Directory (Azure AD).

Lucidchart integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Lucidchart
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Lucidchart (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Lucidchart, potřebujete následující položky:

- S předplatným služby Azure AD
- Lucidchart jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Lucidchart z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lucidchart-from-the-gallery"></a>Přidání Lucidchart z Galerie
Konfigurace integrace Lucidchart do služby Azure AD, budete muset přidat Lucidchart z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Lucidchart z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Lucidchart**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lucidchart-tutorial/tutorial_lucidchart_search.png)

1. Na panelu výsledků vyberte **Lucidchart**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lucidchart-tutorial/tutorial_lucidchart_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Lucidchart podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Lucidchart je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Lucidchart potřeba navázat.

V Lucidchart, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Lucidchart, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Lucidchart](#creating-a-lucidchart-test-user)**  – Pokud chcete mít protějšek Britta Simon Lucidchart, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Lucidchart.

**Ke konfiguraci Azure AD jednotné přihlašování s Lucidchart, proveďte následující kroky:**

1. Na webu Azure Portal na **Lucidchart** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/lucidchart-tutorial/tutorial_lucidchart_samlbase.png)

1. Na **Lucidchart domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/lucidchart-tutorial/tutorial_lucidchart_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://chart2.office.lucidchart.com/saml/sso/azure`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/lucidchart-tutorial/tutorial_lucidchart_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/lucidchart-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Lucidchart.

1. V nabídce v horní části klikněte na tlačítko **týmu**.
   
    ![Tým](./media/lucidchart-tutorial/ic791190.png "týmu")

1. Klikněte na tlačítko **aplikací \> spravovat SAML**.
   
    ![Správa SAML](./media/lucidchart-tutorial/ic791191.png "spravovat SAML")

1. Na **nastavení ověřování SAML** dialogového okna stránky, proveďte následující kroky:
   
    a. Vyberte **povolit ověřování SAML**a potom klikněte na tlačítko **volitelné**.

    ![Nastavení ověřování SAML](./media/lucidchart-tutorial/ic791192.png "nastavení ověřování SAML")
 
    b. V **domény** textového pole zadejte vaši doménu a pak klikněte na **změnit certifikát**.

    ![Změna certifikátu](./media/lucidchart-tutorial/ic791193.png "změnit certifikát")
 
    c. Otevřete soubor stažený metadat, zkopírujte jeho obsah a vložte jej do **nahrát metadat** textového pole.

    ![Odesílání metadat](./media/lucidchart-tutorial/ic791194.png "nahrát metadat")
 
    d. Vyberte **automatické přidávání nových uživatelů do týmu**a potom klikněte na tlačítko **uložit změny**.

    ![Uložit změny](./media/lucidchart-tutorial/ic791195.png "uložit změny")

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lucidchart-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/lucidchart-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lucidchart-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lucidchart-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lucidchart-test-user"></a>Vytvoření zkušebního uživatele Lucidchart

Neexistuje žádná položka akce konfigurace zřizování uživatelů pro Lucidchart.  Přiřazený uživatel se pokusí přihlásit pomocí přístupového panelu Lucidchart, Lucidchart kontroluje, zda uživatel existuje.  

Pokud není žádný uživatelský účet k dispozici dosud, vytvoří se automaticky podle Lucidchart.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Lucidchart použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Lucidchart Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Lucidchart**.

    ![Konfigurace jednotného přihlašování](./media/lucidchart-tutorial/tutorial_lucidchart_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Lucidchart na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Lucidchart.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lucidchart-tutorial/tutorial_general_01.png
[2]: ./media/lucidchart-tutorial/tutorial_general_02.png
[3]: ./media/lucidchart-tutorial/tutorial_general_03.png
[4]: ./media/lucidchart-tutorial/tutorial_general_04.png

[100]: ./media/lucidchart-tutorial/tutorial_general_100.png

[200]: ./media/lucidchart-tutorial/tutorial_general_200.png
[201]: ./media/lucidchart-tutorial/tutorial_general_201.png
[202]: ./media/lucidchart-tutorial/tutorial_general_202.png
[203]: ./media/lucidchart-tutorial/tutorial_general_203.png

