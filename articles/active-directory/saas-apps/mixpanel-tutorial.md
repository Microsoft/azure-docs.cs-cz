---
title: 'Kurz: Integrace Azure Active Directory s Mixpanelu | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mixpanelu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 93203e82e71b66243818cac52f9ec431e85ba18f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169242"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Kurz: Integrace Azure Active Directory s Mixpanelu

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Mixpanelu.

Mixpanelu integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do Mixpanelu
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Mixpanelu (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Mixpanelu, potřebujete následující položky:

- Předplatné Azure AD
- Mixpanelu jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mixpanelu z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mixpanel-from-the-gallery"></a>Přidání Mixpanelu z Galerie
Konfigurace integrace Mixpanelu do služby Azure AD, budete muset přidat Mixpanelu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Mixpanelu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Mixpanelu**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/mixpanel-tutorial/tutorial_mixpanel_search.png)

1. Na panelu výsledků vyberte **Mixpanelu**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Mixpanelu podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Mixpanelu je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Mixpanelu.

V Mixpanelu, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mixpanelu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Mixpanelu](#creating-a-mixpanel-test-user)**  – Pokud chcete mít protějšek Britta Simon v Mixpanelu, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Mixpanelu.

**Ke konfiguraci Azure AD jednotné přihlašování s Mixpanelu, proveďte následující kroky:**

1. Na webu Azure Portal na **Mixpanelu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

1. Na **Mixpanelu domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_url.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://mixpanel.com/login/`

    > [!NOTE] 
    > Zaregistrujte se prosím na [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) nastavit přihlašovací údaje a obraťte se [tým podpory Mixpanelu](mailto:support@mixpanel.com) povolit nastavení jednotného přihlašování pro vašeho tenanta. Vaše přihlašovací adresa URL hodnotu lze také získat v případě potřeby váš tým podpory Mixpanelu. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/mixpanel-tutorial/tutorial_general_400.png)

1. Na **Mixpanelu konfigurace** klikněte na tlačítko **nakonfigurovat Mixpanelu** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_configure.png) 

1. V jiném okně prohlížeče přihlašování k aplikaci Mixpanelu jako správce.

1. V dolní části stránky klikněte na daném malém **ozubené kolečko** ikony v levém rohu. 
   
    ![Mixpanelu jednotné přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

1. Klikněte na tlačítko **zabezpečení přístupu k** kartu a potom klikněte na tlačítko **změnit nastavení**.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

1. Na **změnit certifikát** dialogového okna stránky, klikněte na tlačítko **zvolte soubor** nahrát svůj certifikát stažený, a potom klikněte na **Další**.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

1.  Ověřování adresy URL do textového pole na **změnit adresu URL svého ověřování** dialogového okna stránky, vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal a potom klikněte na tlačítko **Další**.
   
   ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

1. Klikněte na **Done** (Hotovo).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/mixpanel-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/mixpanel-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/mixpanel-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/mixpanel-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-mixpanel-test-user"></a>Vytvoření zkušebního uživatele Mixpanelu

Cílem této části je vytvořte uživatele Britta Simon v Mixpanelu. 

1. Přihlaste se k webu společnosti Mixpanelu jako správce.

1. V dolní části stránky klikněte na malé tlačítko ozubeného kola na levém rohu otevřete **nastavení** okna.

1. Klikněte na tlačítko **týmu** kartu.

1. V **člen týmu** textového pole zadejte Haniny e-mailovou adresu v Azure.
   
    ![Nastavení Mixpanelu](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

1. Klikněte na tlačítko **pozvat**. 

> [!Note]
> Uživatel dostane e-mailu k profilu.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do Mixpanelu.

![Přiřadit uživatele][200] 

**Přiřadit Mixpanelu Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Mixpanelu**.

    ![Konfigurace jednotného přihlašování](./media/mixpanel-tutorial/tutorial_mixpanel_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Mixpanelu na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Mixpanelu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/mixpanel-tutorial/tutorial_general_203.png

