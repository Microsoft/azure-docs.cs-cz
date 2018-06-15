---
title: 'Kurz: Azure Active Directory integrace s Vodeclic | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: bc889919f2d869478843881cc8eae06fc9cb232c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348202"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Kurz: Azure Active Directory integrace s Vodeclic

V tomto kurzu zjistěte, jak integrovat Vodeclic s Azure Active Directory (Azure AD).

Integrace Vodeclic s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Vodeclic.
- Můžete povolit uživatelům automaticky získat přihlášený k Vodeclic (jednotné přihlašování a jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Vodeclic, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené Vodeclic jednotného přihlašování

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí, zkušební verze Azure AD, [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Vodeclic z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-vodeclic-from-the-gallery"></a>Přidat Vodeclic z Galerie
Při konfiguraci integrace Vodeclic do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Vodeclic z galerie.

**Pokud chcete přidat Vodeclic z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítka v horní části dialogových oken.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Vodeclic**. Vyberte **Vodeclic** z panelu výsledky a potom vyberte **přidat** tlačítko Přidat aplikaci.

    ![Vodeclic v seznamu výsledků](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vodeclic podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v Vodeclic je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v Vodeclic.

V Vodeclic, zadejte hodnotu **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dvěma uživateli.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vodeclic, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele Vodeclic](#create-a-vodeclic-test-user) tak, aby měl protějšek Britta Simon v Vodeclic propojeném s Azure AD reprezentace uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on) ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Vodeclic.

**Ke konfiguraci Azure AD jednotné přihlašování s Vodeclic, proveďte následující kroky:**

1. Na portálu Azure na **Vodeclic** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. V **jednotného přihlašování** dialogovém **režimu Single-Sign-on**, vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Pokud chcete nakonfigurovat aplikace **IDP** spustil v režimu **Vodeclic domény a adresy URL** část, proveďte následující kroky:

    ![Vodeclic domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. V **identifikátor** pole, zadejte adresu URL pomocí vzoru následující: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. V **adresa URL odpovědi** pole, zadejte adresu URL pomocí vzoru následující: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Pokud chcete nakonfigurovat aplikace **SP** initiated režimu, vyberte **zobrazit upřesňující nastavení adresy URL** zaškrtněte políčko a proveďte následující krok:

    ![Vodeclic domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí vzoru následující: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat skutečným identifikátorem odpovědět adresu URL a přihlašovací adresa URL. Obraťte se [tým podpory Vodeclic klienta](mailto:hotline@vodeclic.com) k získání těchto hodnot.

5. V **SAML podpisový certifikát** vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Vyberte **Uložit**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Konfigurace jednotného přihlašování na **Vodeclic** straně, odesílat stažené **soubor XML s metadaty** k [tým podpory Vodeclic](mailto:hotline@vodeclic.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com) při k nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a přístup k vložený dokumentace prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-vodeclic-test-user"></a>Vytvoření zkušebního uživatele Vodeclic

V této části vytvoříte volal Britta Simon v Vodeclic uživatele. Pracovat [tým podpory Vodeclic](mailto:hotline@vodeclic.com) přidat uživatele do Vodeclic platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

> [!NOTE]
> V souladu s požadavky na aplikace může být potřeba získat seznam povolených adres váš počítač. K tomu dochází, je nutné sdílet veřejnou IP adresu s [tým podpory Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Vodeclic.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Vodeclic, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Potom přejděte na **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Vodeclic**.

    ![V seznamu aplikací na Vodeclic odkaz](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v **uživatelé** seznamu.

6. V **uživatelů a skupin** dialogové okno, vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždici Vodeclic na přístupovém panelu, můžete získat automaticky přihlášeni k vaší aplikaci Vodeclic.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

