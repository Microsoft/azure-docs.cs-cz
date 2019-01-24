---
title: 'Kurz: Integrace Azure Active Directory s Evidence.com | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Evidence.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: c1eebca0677cf14d59cf24e1a7acb5cebc692648
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812773"
---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Kurz: Integrace Azure Active Directory s Evidence.com

V tomto kurzu se dozvíte, jak integrovat Evidence.com s Azure Active Directory (Azure AD).

Evidence.com integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Evidence.com.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Evidence.com (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Evidence.com, potřebujete následující položky:

- Předplatné Azure AD
- Evidence.com jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Evidence.com z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-evidencecom-from-the-gallery"></a>Přidání Evidence.com z Galerie
Konfigurace integrace Evidence.com do služby Azure AD, budete muset přidat Evidence.com z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Evidence.com z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Evidence.com**vyberte **Evidence.com** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Evidence.com v seznamu výsledků](./media/evidence-tutorial/tutorial_evidence.com_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Evidence.com podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Evidence.com je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Evidence.com potřeba navázat.

V Evidence.com, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Evidence.com, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Evidence.com](#create-a-evidencecom-test-user)**  – Pokud chcete mít protějšek Britta Simon Evidence.com, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Evidence.com.

**Ke konfiguraci Azure AD jednotné přihlašování s Evidence.com, proveďte následující kroky:**

1. Na webu Azure Portal na **Evidence.com** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/evidence-tutorial/tutorial_evidence.com_samlbase.png)

1. Na **Evidence.com domény a adresy URL** části, proveďte následující kroky:

    ![Evidence.com domény a adresy URL jednotného přihlašování – informace](./media/evidence-tutorial/tutorial_evidence.com_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<yourtenant>.evidence.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<yourtenant>.evidence.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Evidence.com klienta](https://communities.taser.com/support/SupportContactUs?typ=LE) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/evidence-tutorial/tutorial_evidence.com_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/evidence-tutorial/tutorial_general_400.png)

1. Na **Evidence.com konfigurace** klikněte na tlačítko **nakonfigurovat Evidence.com** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace evidence.com](./media/evidence-tutorial/tutorial_evidence.com_configure.png) 

1. V okně samostatné webové prohlížeče, přihlaste se k vaší Evidence.com tenanta jako správce a přejděte do **správce** kartu

1. Klikněte na **agentura jednotného přihlašování**

1. Vyberte **jednotného přihlašování na základě SAML**

1. Kopírovat **SAML Entity ID**, **SAML jednotné přihlašování – adresa URL služby** a **odhlašování URL** hodnoty zobrazené na webu Azure Portal a na odpovídající pole v Evidence.com.

1. V poznámkovém bloku otevřete stažený soubor Certificate(Base64), zkopírujte obsah ho do schránky a vložte jej do **certifikát zabezpečení** pole. 

1. V Evidence.com uložte konfiguraci.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/evidence-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/evidence-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/evidence-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/evidence-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-evidencecom-test-user"></a>Vytvoření zkušebního uživatele Evidence.com

Pro uživatele Azure AD bude moct přihlásit musí být zřízený pro přístup do Evidence.com aplikace. Tato část popisuje, jak vytvořit uživatelské účty služby Azure AD uvnitř Evidence.com

**K poskytnutí uživatelského účtu v Evidence.com:**

1. V okně webového prohlížeče přihlaste jako správce serveru vaší společnosti Evidence.com.

1. Přejděte do **správce** kartu.

1. Klikněte na **přidat uživatele**.

1. Klikněte na tlačítko **Přidat**.

1. **E-mailovou adresu** přidání uživatele, musí odpovídat uživatelské jméno uživatele ve službě Azure AD, který chcete poskytnout přístup. Pokud uživatelské jméno a e-mailové adresy nejsou stejnou hodnotu ve vaší organizaci, můžete použít **Evidence.com > atributy > Single Sign-On** části webu Azure portal, chcete-li změnit nameidenitifer odesílat Evidence.com bude e-mailovou adresu.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Evidence.com použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Evidence.com Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Evidence.com**.

    ![Odkaz Evidence.com v seznamu aplikací](./media/evidence-tutorial/tutorial_evidence.com_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Evidence.com na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Evidence.com.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/evidence-tutorial/tutorial_general_01.png
[2]: ./media/evidence-tutorial/tutorial_general_02.png
[3]: ./media/evidence-tutorial/tutorial_general_03.png
[4]: ./media/evidence-tutorial/tutorial_general_04.png

[100]: ./media/evidence-tutorial/tutorial_general_100.png

[200]: ./media/evidence-tutorial/tutorial_general_200.png
[201]: ./media/evidence-tutorial/tutorial_general_201.png
[202]: ./media/evidence-tutorial/tutorial_general_202.png
[203]: ./media/evidence-tutorial/tutorial_general_203.png

