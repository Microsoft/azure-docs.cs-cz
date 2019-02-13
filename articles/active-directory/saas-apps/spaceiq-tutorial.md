---
title: 'Kurz: Integrace Azure Active Directory s SpaceIQ | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6695fe4854c6d91d3d2ba671104d1b481721356c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162267"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Kurz: Integrace Azure Active Directory s SpaceIQ

V tomto kurzu se dozvíte, jak integrovat SpaceIQ s Azure Active Directory (Azure AD).

SpaceIQ integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SpaceIQ.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SpaceIQ (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SpaceIQ, potřebujete následující položky:

- Předplatné Azure AD
- SpaceIQ jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SpaceIQ z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-spaceiq-from-the-gallery"></a>Přidání SpaceIQ z Galerie
Konfigurace integrace SpaceIQ do služby Azure AD, budete muset přidat SpaceIQ z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SpaceIQ z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **SpaceIQ**vyberte **SpaceIQ** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SpaceIQ v seznamu výsledků](./media/spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s SpaceIQ podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SpaceIQ je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SpaceIQ potřeba navázat.

V SpaceIQ, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SpaceIQ, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SpaceIQ](#create-a-spaceiq-test-user)**  – Pokud chcete mít protějšek Britta Simon SpaceIQ, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SpaceIQ.

**Ke konfiguraci Azure AD jednotné přihlašování s SpaceIQ, proveďte následující kroky:**

1. Na webu Azure Portal na **SpaceIQ** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

1. Na **SpaceIQ domény a adresy URL** části, proveďte následující kroky:

    ![SpaceIQ domény a adresy URL jednotného přihlašování – informace](./media/spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://api.spaceiq.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE] 
    > Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a identifikátor, který je vysvětlen později v tomto kurzu.
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **(certifikátu Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/spaceiq-tutorial/tutorial_general_400.png)

1. Na **SpaceIQ konfigurace** klikněte na tlačítko **nakonfigurovat SpaceIQ** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Konfigurace SpaceIQ](./media/spaceiq-tutorial/tutorial_spaceiq_configure.png) 

1.  Otevřete nové okno prohlížeče a přihlaste se k prostředí SpaceIQ jako správce.

1. Jakmile jste přihlášení, klikněte na znaménko díl stavebnice vpravo nahoře a potom klikněte na **"Integrace"**

    ![Nastavení účtu](./media/spaceiq-tutorial/setting1.png) 

1. V části **všechny zřizování a jednotné přihlašování**, klikněte na **Azure** dlaždici přidat instanci Azure jako zprostředkovatele identity.

    ![Ikona SAML](./media/spaceiq-tutorial/setting2.png)

1. V **jednotného přihlašování** dialogové okno pole, proveďte následující kroky:

    ![Nastavení ověřování SAML](./media/spaceiq-tutorial/setting3.png)

    a. V **URL vystavitele SAML** pole, vložte **SAML Entity ID** hodnotu zkopírovat v okně Konfigurace aplikací služby Azure AD.
    
    b. Kopírovat **SAML zpětného volání adresy URL koncového bodu (jen pro čtení)** hodnotu a vložte tuto hodnotu v **adresy URL odpovědi** pole na webu Azure Portal, v části SpaceIQ **domény a adresy URL** oddílu.
    
    c. Kopírovat **identifikátor URI cílové skupiny SAML (jen pro čtení)** hodnotu a vložte tuto hodnotu v **identifikátor** pole na webu Azure Portal, v části SpaceIQ **domény a adresy URL** oddílu.

    d. V poznámkovém bloku otevřete soubor stažený certifikát, zkopírujte jeho obsah a vložte jej do **certifikát X.509** pole.
    
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/spaceiq-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/spaceiq-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/spaceiq-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/spaceiq-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-spaceiq-test-user"></a>Vytvoření zkušebního uživatele SpaceIQ

V této části vytvoříte uživatele v SpaceIQ jako Britta Simon. Pracovní [tým podpory SpaceIQ](mailto:eng@spaceiq.com) přidat uživatele na platformě SpaceIQ. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SpaceIQ použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit SpaceIQ Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SpaceIQ**.

    ![Odkaz SpaceIQ v seznamu aplikací](./media/spaceiq-tutorial/tutorial_spaceiq_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SpaceIQ na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SpaceIQ.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/spaceiq-tutorial/tutorial_general_203.png

