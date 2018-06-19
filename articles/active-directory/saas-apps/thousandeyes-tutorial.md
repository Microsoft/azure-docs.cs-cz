---
title: 'Kurz: Azure Active Directory integrace s ThousandEyes | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: a08023e4853d999e5e339f6fd54fa9d968c4fec1
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899474"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Kurz: Azure Active Directory integrace s ThousandEyes

V tomto kurzu zjistěte, jak integrovat ThousandEyes s Azure Active Directory (Azure AD).

Integrace ThousandEyes s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ThousandEyes
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ThousandEyes (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ThousandEyes, potřebujete následující položky:

- Předplatné služby Azure AD
- ThousandEyes jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ThousandEyes z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-thousandeyes-from-the-gallery"></a>Přidání ThousandEyes z Galerie
Při konfiguraci integrace ThousandEyes do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ThousandEyes z galerie.

**Pokud chcete přidat ThousandEyes z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **ThousandEyes**.

    ![Vytváření testovacího uživatele Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. Na panelu výsledků vyberte **ThousandEyes**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ThousandEyes podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ThousandEyes je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ThousandEyes musí navázat.

V ThousandEyes, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ThousandEyes, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ThousandEyes](#creating-a-thousandeyes-test-user)**  – Pokud chcete mít protějšek Britta Simon v ThousandEyes propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ThousandEyes.

**Ke konfiguraci Azure AD jednotné přihlašování s ThousandEyes, proveďte následující kroky:**

1. Na portálu Azure na **ThousandEyes** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Na **ThousandEyes domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://app.thousandeyes.com/login/sso`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/thousandeyes-tutorial/tutorial_general_400.png)

6. Na **ThousandEyes konfigurace** klikněte na tlačítko **konfigurace ThousandEyes** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. V okně prohlížeče jiný web, přihlaste se k vaší **ThousandEyes** společnosti lokality jako správce.

8. V nabídce v horní části, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/thousandeyes-tutorial/ic790066.png "nastavení")

9. Klikněte na tlačítko **účtu**

    ![Účet](./media/thousandeyes-tutorial/ic790067.png "účtu")

10. Klikněte **ověřování a zabezpečení** kartě.

    ![Zabezpečení a ověřování](./media/thousandeyes-tutorial/ic790068.png "ověřování a zabezpečení")

11. V **instalace Single Sign-On** část, proveďte následující kroky:

    ![Nastavení jednotného přihlašování](./media/thousandeyes-tutorial/ic790069.png "nastavit jednotné přihlašování")

    a. Vyberte **povolit jednotné přihlašování**.

    b. V **URL přihlašovací stránky** textovému poli, vložte **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    c. V **adresa URL odhlašovací stránky** textovému poli, vložte **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    d. **Vystavitel zprostředkovatele identity** textovému poli, vložte **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    e. V **ověřovací certifikát**, klikněte na tlačítko **zvolte soubor**a pak nahrajte certifikát, který jste si stáhli z portálu Azure.

    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.

    ![Vytváření testovacího uživatele Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Vytváření testovacího uživatele Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-thousandeyes-test-user"></a>Vytvoření zkušebního uživatele ThousandEyes

Cílem této části je vytvoření uživatele v ThousandEyes nazývá Britta Simon. ThousandEyes podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](thousandeyes-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti ThousandEyes jako správce.

2. Klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/thousandeyes-tutorial/IC790066.png "nastavení")

3. Klikněte na tlačítko **účet**.

    ![Účet](./media/thousandeyes-tutorial/IC790067.png "účtu")

4. Klikněte **účty a uživatelé** kartě.

    ![Účty a uživatelé](./media/thousandeyes-tutorial/IC790073.png "účty a uživatelé")

5. V **přidat uživatele & účty** část, proveďte následující kroky:

    ![Přidání uživatelských účtů](./media/thousandeyes-tutorial/IC790074.png "přidejte uživatelské účty")

    a. V **název** textovému poli, zadejte jméno uživatele jako **Britta Simon**.

    b. V **e-mailu** jako typ e-mailu uživatele k textovému poli, **brittasimon@contoso.com**.

    b. Klikněte na tlačítko **přidat nového uživatele k účtu**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory dostane e-mailu, včetně odkaz k potvrzení a aktivovat účet.

> [!NOTE]
> Můžete použít všechny ostatní ThousandEyes uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované ThousandEyes zřídit služby Azure Active Directory uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ThousandEyes.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon ThousandEyes, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ThousandEyes**.

    ![Konfigurovat jednotné přihlašování](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ThousandEyes na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ThousandEyes.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](thousandeyes-provisioning-tutorial.md)


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
