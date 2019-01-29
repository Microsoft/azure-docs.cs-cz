---
title: 'Kurz: Integrace Azure Active Directory s ThousandEyes | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 06de55fd2bd1064e93e38fe0ff0cfb13fdc1463a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175430"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Kurz: Integrace Azure Active Directory s ThousandEyes

V tomto kurzu se dozvíte, jak integrovat ThousandEyes s Azure Active Directory (Azure AD).

ThousandEyes integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ThousandEyes
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ThousandEyes (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ThousandEyes, potřebujete následující položky:

- Předplatné Azure AD
- ThousandEyes jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ThousandEyes z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-thousandeyes-from-the-gallery"></a>Přidání ThousandEyes z Galerie
Konfigurace integrace ThousandEyes do služby Azure AD, budete muset přidat ThousandEyes z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ThousandEyes z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **ThousandEyes**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. Na panelu výsledků vyberte **ThousandEyes**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ThousandEyes podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ThousandEyes je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ThousandEyes potřeba navázat.

V ThousandEyes, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ThousandEyes, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele ThousandEyes](#creating-a-thousandeyes-test-user)**  – Pokud chcete mít protějšek Britta Simon ThousandEyes, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ThousandEyes.

**Ke konfiguraci Azure AD jednotné přihlašování s ThousandEyes, proveďte následující kroky:**

1. Na webu Azure Portal na **ThousandEyes** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. Na **ThousandEyes domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://app.thousandeyes.com/login/sso`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. Na **ThousandEyes konfigurace** klikněte na tlačítko **nakonfigurovat ThousandEyes** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. V okně jiné webové prohlížeče, přihlaste se k vaší **ThousandEyes** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/thousandeyes-tutorial/ic790066.png "nastavení")

1. Klikněte na tlačítko **účtu**

    ![Účet](./media/thousandeyes-tutorial/ic790067.png "účtu")

1. Klikněte na tlačítko **zabezpečení a ověřování** kartu.

    ![Zabezpečení a ověřování](./media/thousandeyes-tutorial/ic790068.png "zabezpečení a ověřování")

1. V **nastavení jednotného přihlašování** části, proveďte následující kroky:

    ![Nastavení jednotného přihlašování](./media/thousandeyes-tutorial/ic790069.png "nastavit jednotné přihlašování")

    a. Vyberte **povolit jednotné přihlašování**.

    b. V **adresu URL přihlašovací stránky** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **adresy URL odhlašovací stránky** vložit do textového pole **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. **Vystavitel zprostředkovatele identity** vložit do textového pole **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    e. V **ověřovací certifikát**, klikněte na tlačítko **zvolte soubor**a pak nahrajte certifikát, který jste si stáhli z webu Azure portal.

    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-thousandeyes-test-user"></a>Vytvoření zkušebního uživatele ThousandEyes

Cílem této části je vytvořte uživatele Britta Simon v ThousandEyes. ThousandEyes podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](thousandeyes-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlášení na webu společnosti ThousandEyes jako správce.

1. Klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/thousandeyes-tutorial/IC790066.png "nastavení")

1. Klikněte na tlačítko **účet**.

    ![Účet](./media/thousandeyes-tutorial/IC790067.png "účtu")

1. Klikněte na tlačítko **účty a uživatelé** kartu.

    ![Účty a uživatelé](./media/thousandeyes-tutorial/IC790073.png "účty a uživatelé")

1. V **přidat uživatele a účty** části, proveďte následující kroky:

    ![Přidejte uživatelské účty](./media/thousandeyes-tutorial/IC790074.png "přidejte uživatelské účty")

    a. V **název** textového pole, zadejte jméno uživatele, jako jsou **Britta Simon**.

    b. V **e-mailu** , jako je textové pole, typ e-mailu uživatele **brittasimon@contoso.com**.

    b. Klikněte na tlačítko **přidání nového uživatele do účtu**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory se zobrazí e-mailu včetně odkazu na potvrzení a aktivovat účet.

> [!NOTE]
> Můžete použít jakékoli jiné ThousandEyes uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných ThousandEyes ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k ThousandEyes použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit ThousandEyes Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ThousandEyes**.

    ![Konfigurace jednotného přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ThousandEyes na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ThousandEyes.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
