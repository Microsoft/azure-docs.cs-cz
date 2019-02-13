---
title: 'Kurz: Integrace Azure Active Directory s Intacct | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d30bae699a69bed893e36e24b6f8370db4f8b1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167095"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Kurz: Integrace Azure Active Directory s Intacct

V tomto kurzu se dozvíte, jak integrovat Intacct s Azure Active Directory (Azure AD).

Intacct integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Intacct
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Intacct (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Intacct, potřebujete následující položky:

- Předplatné Azure AD
- Intacct jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Intacct z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-intacct-from-the-gallery"></a>Přidání Intacct z Galerie
Konfigurace integrace Intacct do služby Azure AD, budete muset přidat Intacct z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Intacct z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Intacct**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intacct-tutorial/tutorial_intacct_search.png)

1. Na panelu výsledků vyberte **Intacct**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Intacct podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Intacct je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Intacct potřeba navázat.

V Intacct, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Intacct, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se testovací uživatelské jméno Intacct](#creating-an-intacct-test-user)**  – Pokud chcete mít protějšek Britta Simon Intacct, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Intacct.

**Ke konfiguraci Azure AD jednotné přihlašování s Intacct, proveďte následující kroky:**

1. Na webu Azure Portal na **Intacct** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/intacct-tutorial/tutorial_intacct_samlbase.png)

1. Na **Intacct domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/intacct-tutorial/tutorial_intacct_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu Skutečná adresa URL odpovědi. Kontakt [tým podpory Intacct](https://us.intacct.com/support) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/intacct-tutorial/tutorial_intacct_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/intacct-tutorial/tutorial_general_400.png)

1. Na **Intacct konfigurace** klikněte na tlačítko **nakonfigurovat Intacct** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/intacct-tutorial/tutorial_intacct_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Intacct jako správce.

1. Klikněte na tlačítko **společnosti** kartu a potom klikněte na tlačítko **informace o společnosti**.

    ![Společnost](./media/intacct-tutorial/ic790037.png "společnosti")

1. Klikněte na tlačítko **zabezpečení** kartu a potom klikněte na tlačítko **upravit**.

    ![Zabezpečení](./media/intacct-tutorial/ic790038.png "zabezpečení")

1. V **jednotné přihlašování (SSO)** části, proveďte následující kroky:

    ![Jednotné přihlašování](./media/intacct-tutorial/ic790039.png "jednotné přihlašování")

    a. Vyberte **povolit jednotné přihlašování**.

    b. Jako **typ zprostředkovatele identit**vyberte **SAML 2.0**.

    c. V **URL vystavitele** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.
   
    d. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    e. Otevřete váš **base-64** kódování certifikátu v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte ho do **certifikát** pole.
   
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intacct-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/intacct-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/intacct-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/intacct-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-intacct-test-user"></a>Vytvoření Intacct testovacího uživatele

Nastavení uživatelů Azure AD, aby se můžete přihlásit k Intacct, musí být poskytnuty do Intacct. Zřizování je pro Intacct, úlohu.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Intacct** tenanta.

1. Klikněte na tlačítko **společnosti** kartu a potom klikněte na tlačítko **uživatelé**.

    ![Uživatelé](./media/intacct-tutorial/ic790041.png "uživatelů")
1. Klikněte na tlačítko **přidat** kartu.

    ![Přidat](./media/intacct-tutorial/ic790042.png "přidat")
1. V **informace o uživateli** části, proveďte následující kroky:

    ![Informace o uživateli](./media/intacct-tutorial/ic790043.png "informace o uživateli")

    a. Zadejte **ID uživatele**, **příjmení**, **křestní jméno**, **e-mailová adresa**, **Title**a **Phone** , které chcete zřídit do účtu služby Azure AD **informace o uživateli** oddílu.

    b. Vyberte **oprávnění správce** účtu služby Azure AD, který chcete zřídit.
   
    c. Klikněte na **Uložit**. Držitel účtu Azure AD obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

>[!NOTE]
>Ke zřízení uživatelských účtů služby Azure AD, můžete použít jiné nástroje pro tvorbu Intacct uživatelského účtu nebo rozhraní API, která jsou k dispozici v Intacct.
        
### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Intacct použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Intacct Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Intacct**.

    ![Konfigurace jednotného přihlašování](./media/intacct-tutorial/tutorial_intacct_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Intacct na přístupovém panelu, vám by měl být automaticky přihlášeni Intacct aplikace.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/intacct-tutorial/tutorial_general_01.png
[2]: ./media/intacct-tutorial/tutorial_general_02.png
[3]: ./media/intacct-tutorial/tutorial_general_03.png
[4]: ./media/intacct-tutorial/tutorial_general_04.png

[100]: ./media/intacct-tutorial/tutorial_general_100.png

[200]: ./media/intacct-tutorial/tutorial_general_200.png
[201]: ./media/intacct-tutorial/tutorial_general_201.png
[202]: ./media/intacct-tutorial/tutorial_general_202.png
[203]: ./media/intacct-tutorial/tutorial_general_203.png

