---
title: 'Kurz: Integrace Azure Active Directory s Workrite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workrite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2a5c2956-a011-4d5c-877b-80679b6587b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d3b3702ca2daad002bde1b4e64bf9d43a03028d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180432"
---
# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>Kurz: Integrace Azure Active Directory s Workrite

V tomto kurzu se dozvíte, jak integrovat Workrite s Azure Active Directory (Azure AD).

Workrite integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Workrite.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Workrite (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Workrite, potřebujete následující položky:

- Předplatné Azure AD
- Workrite jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Workrite z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workrite-from-the-gallery"></a>Přidání Workrite z Galerie
Konfigurace integrace Workrite do služby Azure AD, budete muset přidat Workrite z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Workrite z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Workrite**vyberte **Workrite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Workrite v seznamu výsledků](./media/workrite-tutorial/tutorial_workrite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Workrite podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Workrite je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Workrite potřeba navázat.

V Workrite, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workrite, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Workrite](#create-a-workrite-test-user)**  – Pokud chcete mít protějšek Britta Simon Workrite, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Workrite.

**Ke konfiguraci Azure AD jednotné přihlašování s Workrite, proveďte následující kroky:**

1. Na webu Azure Portal na **Workrite** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/workrite-tutorial/tutorial_workrite_samlbase.png)

1. Na **Workrite domény a adresy URL** části, proveďte následující kroky:

    ![Workrite domény a adresy URL jednotného přihlašování – informace](./media/workrite-tutorial/tutorial_workrite_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=<uniqueid>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Workrite klienta](mailto:support@workrite.co.uk) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/workrite-tutorial/tutorial_workrite_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/workrite-tutorial/tutorial_general_400.png)

1. Na **Workrite konfigurace** klikněte na tlačítko **nakonfigurovat Workrite** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Workrite](./media/workrite-tutorial/tutorial_workrite_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Workrite** straně, je nutné odeslat na stažený **Certificate(Base64), adresa URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [Workrite tým podpory](mailto:support@workrite.co.uk).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/workrite-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/workrite-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/workrite-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/workrite-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-workrite-test-user"></a>Vytvoření zkušebního uživatele Workrite

Cílem této části je vytvořte uživatele Britta Simon v Workrite.

**Vytvořte uživatele v Workrite jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti workrite jako správce.

1. V navigačním podokně klikněte na tlačítko **správce**.
   
    ![Řízení správy][400]

1. Přejděte do rychlé odkazy a pak klikněte na tlačítko **vytvořte uživatele**.
   
    ![Vytvořit oddíl uživatele][401]

1. Na **vytvořit uživatele** dialogového okna, proveďte následující kroky:
   
    ![Vytvořit uživatele Dailog][402]
    
    a. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je Britta.

    c. V **příjmení** textového pole zadejte příjmení uživatele jako Simon.
    
    d. Vyberte **správce klienta** jako **zvolte roli,**.
    
    e. Klikněte na **Uložit**.   

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Workrite použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Workrite Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Workrite**.

    ![Odkaz Workrite v seznamu aplikací](./media/workrite-tutorial/tutorial_workrite_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Workrite na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Workrite.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workrite-tutorial/tutorial_general_01.png
[2]: ./media/workrite-tutorial/tutorial_general_02.png
[3]: ./media/workrite-tutorial/tutorial_general_03.png
[4]: ./media/workrite-tutorial/tutorial_general_04.png

[100]: ./media/workrite-tutorial/tutorial_general_100.png

[200]: ./media/workrite-tutorial/tutorial_general_200.png
[201]: ./media/workrite-tutorial/tutorial_general_201.png
[202]: ./media/workrite-tutorial/tutorial_general_202.png
[203]: ./media/workrite-tutorial/tutorial_general_203.png
[400]: ./media/workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/workrite-tutorial/tutorial_workrite_402.png

