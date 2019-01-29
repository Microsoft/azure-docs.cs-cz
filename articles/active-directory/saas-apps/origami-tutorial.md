---
title: 'Kurz: Integrace Azure Active Directory s Origami | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 042167b77679338ca7e982b1b1cd80c22997b766
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171062"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Kurz: Integrace Azure Active Directory s Origami

V tomto kurzu se dozvíte, jak integrovat Origami s Azure Active Directory (Azure AD).

Origami integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Origami
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Origami (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Origami, potřebujete následující položky:

- Předplatné Azure AD
- Origami jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Origami z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-origami-from-the-gallery"></a>Přidání Origami z Galerie
Konfigurace integrace Origami do služby Azure AD, budete muset přidat Origami z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Origami z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Origami**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/origami-tutorial/tutorial_origami_search.png)

1. Na panelu výsledků vyberte **Origami**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Origami podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Origami je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Origami potřeba navázat.

V Origami, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Origami, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se testovací uživatelské jméno Origami](#creating-an-origami-test-user)**  – Pokud chcete mít protějšek Britta Simon Origami, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Origami.

**Ke konfiguraci Azure AD jednotné přihlašování s Origami, proveďte následující kroky:**

1. Na webu Azure Portal na **Origami** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_samlbase.png)

1. Na **Origami domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Origami klienta](https://wordpress.org/support/theme/origami) má být získána hodnota. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_general_400.png)

1. Na **Origami konfigurace** klikněte na tlačítko **nakonfigurovat Origami** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_configure.png) 

1. Přihlaste se k Origami účet s právy správce.

1. V nabídce v horní části klikněte na tlačítko **správce**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_51.png)

1. Na stránce dialogové okno jednotné přihlašování na nastavení proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_531.png)

    a. Vyberte **povolit jednotné přihlašování**.

    b. V **zprostředkovatele Identity adresa URL přihlašovací stránky** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **adresa URL stránky odhlašování zprostředkovatele Identity** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **Procházet** se nahrát certifikát, který jste si stáhli z portálu Azure portal.

    e. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/origami-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/origami-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/origami-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/origami-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-origami-test-user"></a>Vytvoření Origami testovacího uživatele

V této části vytvoříte uživatele v Origami jako Britta Simon. 

1. Přihlaste se k Origami účet s právy správce.

1. V nabídce v horní části klikněte na tlačítko **správce**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_51.png)

1. Na **uživatelů a zabezpečení** dialogového okna, klikněte na tlačítko **uživatelé**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_54.png)

1. Klikněte na tlačítko **přidání nového uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_55.png)

1. V dialogovém okně Přidat nového uživatele proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_56.png)

    a. V **uživatelské jméno** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    b. V **heslo** textového pole zadejte heslo.

    c. V **potvrzení hesla** textového pole zadejte heslo znovu.

    d. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    e. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

    f. Klikněte na **Uložit**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_57.png)

1. Přiřadit **role uživatelů** a **klientský přístup** uživateli. 
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Origami použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Origami Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Origami**.

    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Origami na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Origami.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/origami-tutorial/tutorial_general_01.png
[2]: ./media/origami-tutorial/tutorial_general_02.png
[3]: ./media/origami-tutorial/tutorial_general_03.png
[4]: ./media/origami-tutorial/tutorial_general_04.png

[100]: ./media/origami-tutorial/tutorial_general_100.png

[200]: ./media/origami-tutorial/tutorial_general_200.png
[201]: ./media/origami-tutorial/tutorial_general_201.png
[202]: ./media/origami-tutorial/tutorial_general_202.png
[203]: ./media/origami-tutorial/tutorial_general_203.png

