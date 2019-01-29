---
title: 'Kurz: Integrace Azure Active Directory s PlanGrid | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PlanGrid.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ba72432-9b49-4358-b756-14c982422be8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 5ceb5f09b599b6c42dea3359b18c319e7b44fed7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165077"
---
# <a name="tutorial-azure-active-directory-integration-with-plangrid"></a>Kurz: Integrace Azure Active Directory s PlanGrid

V tomto kurzu se dozvíte, jak integrovat PlanGrid s Azure Active Directory (Azure AD).

PlanGrid integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PlanGrid.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PlanGrid (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PlanGrid, potřebujete následující položky:

- Předplatné Azure AD
- PlanGrid jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PlanGrid z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-plangrid-from-the-gallery"></a>Přidání PlanGrid z Galerie
Konfigurace integrace PlanGrid do služby Azure AD, budete muset přidat PlanGrid z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat PlanGrid z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **PlanGrid**vyberte **PlanGrid** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![PlanGrid v seznamu výsledků](./media/plangrid-tutorial/tutorial_plangrid_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí PlanGrid podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v PlanGrid je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v PlanGrid potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PlanGrid, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele PlanGrid](#create-a-plangrid-test-user)**  – Pokud chcete mít protějšek Britta Simon PlanGrid, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci PlanGrid.

**Ke konfiguraci Azure AD jednotné přihlašování s PlanGrid, proveďte následující kroky:**

1. Na webu Azure Portal na **PlanGrid** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/plangrid-tutorial/tutorial_plangrid_samlbase.png)

3. Na **PlanGrid domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![PlanGrid domény a adresy URL jednotného přihlašování – informace](./media/plangrid-tutorial/tutorial_plangrid_url1.png)

    V **identifikátor (Entity ID)** textového pole zadejte adresu URL: `https://io.plangrid.com/sessions/saml/metadata`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![PlanGrid domény a adresy URL jednotného přihlašování – informace](./media/plangrid-tutorial/tutorial_plangrid_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.plangrid.com/login`

5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/plangrid-tutorial/tutorial_plangrid_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/plangrid-tutorial/tutorial_general_400.png)
    
7. Ke konfiguraci jednotného přihlašování na **PlanGrid** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory PlanGrid](mailto:help@plangrid.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/plangrid-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/plangrid-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/plangrid-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/plangrid-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-plangrid-test-user"></a>Vytvoření zkušebního uživatele PlanGrid

V této části vytvoříte uživatele v PlanGrid jako Britta Simon. Práce s [tým podpory PlanGrid](mailto:help@plangrid.com) přidat uživatele na platformě PlanGrid. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k PlanGrid použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit PlanGrid Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **PlanGrid**.

    ![Odkaz PlanGrid v seznamu aplikací](./media/plangrid-tutorial/tutorial_plangrid_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici PlanGrid na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci PlanGrid.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/plangrid-tutorial/tutorial_general_01.png
[2]: ./media/plangrid-tutorial/tutorial_general_02.png
[3]: ./media/plangrid-tutorial/tutorial_general_03.png
[4]: ./media/plangrid-tutorial/tutorial_general_04.png

[100]: ./media/plangrid-tutorial/tutorial_general_100.png

[200]: ./media/plangrid-tutorial/tutorial_general_200.png
[201]: ./media/plangrid-tutorial/tutorial_general_201.png
[202]: ./media/plangrid-tutorial/tutorial_general_202.png
[203]: ./media/plangrid-tutorial/tutorial_general_203.png

