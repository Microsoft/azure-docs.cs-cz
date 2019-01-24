---
title: 'Kurz: Integrace Azure Active Directory s SmartRecruiters | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: jeedes
ms.openlocfilehash: 6624368e0438b8f41a0f90c5da3f7f00f4cbc86c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813555"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Kurz: Integrace Azure Active Directory s SmartRecruiters

V tomto kurzu se dozvíte, jak integrovat SmartRecruiters s Azure Active Directory (Azure AD).

SmartRecruiters integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SmartRecruiters.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SmartRecruiters (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SmartRecruiters, potřebujete následující položky:

- Předplatné Azure AD
- SmartRecruiters jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SmartRecruiters z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-smartrecruiters-from-the-gallery"></a>Přidání SmartRecruiters z Galerie
Konfigurace integrace SmartRecruiters do služby Azure AD, budete muset přidat SmartRecruiters z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SmartRecruiters z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **SmartRecruiters**vyberte **SmartRecruiters** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SmartRecruiters v seznamu výsledků](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SmartRecruiters podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SmartRecruiters je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SmartRecruiters potřeba navázat.

V SmartRecruiters, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SmartRecruiters, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SmartRecruiters](#create-a-smartrecruiters-test-user)**  – Pokud chcete mít protějšek Britta Simon SmartRecruiters, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SmartRecruiters.

**Ke konfiguraci Azure AD jednotné přihlašování s SmartRecruiters, proveďte následující kroky:**

1. Na webu Azure Portal na **SmartRecruiters** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_samlbase.png)

1. Na **SmartRecruiters domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![SmartRecruiters domény a adresy URL jednotného přihlašování – informace](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![SmartRecruiters domény a adresy URL jednotného přihlašování – informace](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory SmartRecruiters klienta](https://www.smartrecruiters.com/about-us/contact-us/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte certifikát ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/smartrecruiters-tutorial/tutorial_general_400.png)
    
1. Na **SmartRecruiters konfigurace** klikněte na tlačítko **nakonfigurovat SmartRecruiters** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace SmartRecruiters](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti SmartRecruiters jako správce.

1. Přejděte na **nastavení / Admin**.

    ![Konfigurace SmartRecruiters](./media/smartrecruiters-tutorial/configure.png)

1. V **konfigurace** klikněte na tlačítko **jednotného přihlašování k webu**.

    ![Konfigurace SmartRecruiters](./media/smartrecruiters-tutorial/configure1.png)

1. Přepnout **povolit jednotné přihlašování k webu**.

    ![Konfigurace SmartRecruiters](./media/smartrecruiters-tutorial/configure2.png)

1. V **konfigurace zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurace SmartRecruiters](./media/smartrecruiters-tutorial/configure4.png)

    a. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    b. Otevřít **certificate(Base64)** kterou jste si stáhli z webu Azure portal a vložte hodnotu do **certifikát zprostředkovatele Identity** textového pole.

1. Klikněte na tlačítko **uložit jednotného přihlašování k webu configuration**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/smartrecruiters-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/smartrecruiters-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/smartrecruiters-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/smartrecruiters-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-smartrecruiters-test-user"></a>Vytvoření zkušebního uživatele SmartRecruiters

V této části vytvoříte uživatele v SmartRecruiters jako Britta Simon. Práce s [tým podpory SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/) přidat uživatele na platformě SmartRecruiters. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SmartRecruiters použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit SmartRecruiters Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SmartRecruiters**.

    ![Odkaz SmartRecruiters v seznamu aplikací](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SmartRecruiters na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SmartRecruiters.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smartrecruiters-tutorial/tutorial_general_01.png
[2]: ./media/smartrecruiters-tutorial/tutorial_general_02.png
[3]: ./media/smartrecruiters-tutorial/tutorial_general_03.png
[4]: ./media/smartrecruiters-tutorial/tutorial_general_04.png

[100]: ./media/smartrecruiters-tutorial/tutorial_general_100.png

[200]: ./media/smartrecruiters-tutorial/tutorial_general_200.png
[201]: ./media/smartrecruiters-tutorial/tutorial_general_201.png
[202]: ./media/smartrecruiters-tutorial/tutorial_general_202.png
[203]: ./media/smartrecruiters-tutorial/tutorial_general_203.png

