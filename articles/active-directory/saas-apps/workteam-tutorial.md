---
title: 'Kurz: Integrace Azure Active Directory se službou Workteam | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 8d6ca6395e4f5e1aca361c56e21afc4e6bd1fc0c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43132881"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Kurz: Integrace Azure Active Directory se službou Workteam

V tomto kurzu se dozvíte, jak integrovat Workteam s Azure Active Directory (Azure AD).

Workteam integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Workteam.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Workteam (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Workteam, potřebujete následující položky:

- Předplatné Azure AD
- Workteam jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Workteam z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workteam-from-the-gallery"></a>Přidání Workteam z Galerie
Konfigurace integrace Workteam do služby Azure AD, budete muset přidat Workteam z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Workteam z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Workteam**vyberte **Workteam** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Workteam v seznamu výsledků](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Workteam podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Workteam je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Workteam potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workteam, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Workteam](#create-a-workteam-test-user)**  – Pokud chcete mít protějšek Britta Simon Workteam, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Workteam.

**Ke konfiguraci Azure AD jednotné přihlašování s Workteam, proveďte následující kroky:**

1. Na webu Azure Portal na **Workteam** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. Na **Workteam domény a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Workteam domény a adresy URL jednotného přihlašování – informace](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Workteam domény a adresy URL jednotného přihlašování – informace](./media/workteam-tutorial/tutorial_workteam_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.workte.am`
     
5. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/workteam-tutorial/tutorial_general_400.png)
    
7. Na **Workteam konfigurace** klikněte na tlačítko **nakonfigurovat Workteam** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Workteam](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. V okně jiné webové prohlížeče Přihlaste se k Workteam jako správce zabezpečení.

9. V pravém horním rohu klikněte na **profilu logo** a potom klikněte na **nastavení organizace**. 

    ![Nastavení Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. V části **ověřování** části, klikněte na **nastavení logo**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. Na **nastavení SAML** stránce, proveďte následující kroky:

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Vyberte **SAML zprostředkovatele identity** jako **AD Azure**.

    b. V **SAML jednotné přihlašování – adresa URL služby** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **SAML Entity ID** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    d. V poznámkovém bloku, otevřete **certifikáty s kódováním base-64** , který jste stáhli z portálu Azure portal, zkopírujte jeho obsah a vložte jej do **podpisový certifikát SAML (Base64)** pole.

    e. Klikněte na **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/workteam-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/workteam-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/workteam-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/workteam-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-workteam-test-user"></a>Vytvoření zkušebního uživatele Workteam

Azure AD uživatelům se přihlásit a Workteam povolit, musí být poskytnuty do Workteam. Zřizování v Workteam, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Workteam jako správce zabezpečení.

2. Na nahoře uprostřed **nastavení organizace** klikněte na **uživatelé** a potom klikněte na tlačítko **nového uživatele**.

    ![Workteam uživatele](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na **nového zaměstnance** stránce, proveďte následující kroky:

    ![Workteam newuser](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. V **název** textové pole, zadejte jméno uživatele, jako je **Brittasimon**.

    b. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    c. Klikněte na **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Workteam použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Workteam Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Workteam**.

    ![Odkaz Workteam v seznamu aplikací](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Workteam na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Workteam.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

