---
title: 'Kurz: Integrace Azure Active Directory s Palo Alto Networks - clona | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - clona.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 7f94921126900c8e124cd41bd2e4f913b28396a2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153670"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks - clona

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks - clona se službou Azure Active Directory (Azure AD).

Integrace Palo Alto Networks - clona s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks - clona.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Palo Alto Networks - Clona (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - clona, potřebujete následující položky:

- Předplatné Azure AD
- Palo Alto Networks - clona jednotného přihlašování povoleno předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Palo Alto Networks - clona z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Přidání Palo Alto Networks - clona z Galerie
Ke konfiguraci integrace Palo Alto Networks - clona do služby Azure AD, budete muset přidat Palo Alto Networks - clona z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - clona z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Palo Alto Networks - clona**vyberte **Palo Alto Networks - clona** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Palo Alto Networks - otvorů v seznamu výsledků](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - clona podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Palo Alto Networks - clona je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Palo Alto Networks - Clona musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - clona, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření Palo Alto Networks - clona testovacího uživatele](#create-a-palo-alto-networks---aperture-test-user)**  – Pokud chcete mít protějšek Britta Simon Palo Alto Networks - clona, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Palo Alto Networks - clona aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s Palo Alto Networks - clona, proveďte následující kroky:**

1. Na webu Azure Portal na **Palo Alto Networks - clona** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

1. Na **Palo Alto Networks - clona domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Palo Alto Networks - clona domény a adresy URL jednotného přihlašování – informace](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Palo Alto Networks - clona domény a adresy URL jednotného přihlašování – informace](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [Palo Alto Networks - tým podpory clona klienta](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


1. Na **Palo Alto Networks - konfigurace clona** klikněte na tlačítko **konfigurace Palo Alto Networks - clona** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace propojení](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

1. V jiné okno webového prohlížeče, přihlaste se k Palo Alto Networks - clona jako správce.

1. V horní nabídce klikněte na **nastavení**.

    ![Na kartě nastavení](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

1. Přejděte do **aplikace** klikněte na **ověřování** formuláře levé nabídce.

    ![Na kartě vícefaktorového ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
1. Na **ověřování** stránku, proveďte následující kroky:
    
    ![Na kartě ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Zkontrolujte, **povolit jednotné přihlašování – On(Supported SSP Providers are Okta, Onelogin)** z **Single Sign-On** pole.

    b. V **ID zprostředkovatele Identity** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    c. Klikněte na tlačítko **zvolit soubor** na kterou odešlete certifikát stažený z Azure AD **certifikát poskytovatele Identity** pole.

    d. V **adresa URL jednotného přihlašování pro zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    e. Přečtěte si informace zprostředkovatele identity z **clona informace** části a stáhněte si certifikát od **clona klíč** pole.

    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Vytvoření Palo Alto Networks - clona testovacího uživatele

V této části vytvoříte uživatele v Palo Alto Networks - clona jako Britta Simon. Práce s [Palo Alto Networks - tým podpory clona klienta](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) k přidání uživatelů do Palo Alto Networks - clona platformy. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Palo Alto Networks - clona použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon Palo Alto Networks - clona, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Palo Alto Networks - clona**.

    ![Palo Alto Networks - link clona v seznamu aplikací](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Palo Alto Networks - clona dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k Palo Alto Networks - clona aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

