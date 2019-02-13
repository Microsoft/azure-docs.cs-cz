---
title: 'Kurz: Integrace Azure Active Directory s Vodeclic | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3dcd39d58089b202d9e9d61cfc5d25e12ff7a6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217764"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Kurz: Integrace Azure Active Directory s Vodeclic

V tomto kurzu se dozvíte, jak integrovat Vodeclic s Azure Active Directory (Azure AD).

Vodeclic integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Vodeclic.
- Můžete povolit uživatelům, aby automaticky získat přihlášený k Vodeclic (jednotné přihlašování a jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Vodeclic, potřebujete následující položky:

- Předplatné Azure AD
- Předplatné podporující Vodeclic jednotného přihlašování

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Pokud nemáte Azure AD zkušební prostředí [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Vodeclic z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-vodeclic-from-the-gallery"></a>Přidání Vodeclic z Galerie
Konfigurace integrace Vodeclic do služby Azure AD, budete muset přidat Vodeclic z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Vodeclic z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Vodeclic**. Vyberte **Vodeclic** z panel výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Vodeclic v seznamu výsledků](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Vodeclic podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo tento uživatel protějšky v Vodeclic je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatel v Vodeclic.

V Vodeclic, zadejte hodnotu **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dva uživatele.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vodeclic, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvoření zkušebního uživatele Vodeclic](#create-a-vodeclic-test-user) mít protějšek Britta Simon Vodeclic, který je propojený s Azure AD zastoupení uživatele.
1. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#test-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Vodeclic.

**Ke konfiguraci Azure AD jednotné přihlašování s Vodeclic, proveďte následující kroky:**

1. Na webu Azure Portal na **Vodeclic** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. V **jednotného přihlašování** dialogovém okně **režimu Single-Sign-on**vyberte **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

1. Pokud chcete nakonfigurovat aplikace v **zprostředkovatele identity** zahájeno v režimu **Vodeclic domény a adresy URL** části, proveďte následující kroky:

    ![Vodeclic domény a adresy URL jednotného přihlašování – informace](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. V **identifikátor** pole, zadejte adresu URL s následujícím vzorem: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

1. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, vyberte **zobrazit pokročilé nastavení URL** zaškrtněte políčko a proveďte následující krok:

    ![Vodeclic domény a adresy URL jednotného přihlašování – informace](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat s identifikátorem skutečné odpovědi adresy URL a přihlašovací adresa URL. Obraťte se [tým podpory Vodeclic klienta](mailto:hotline@vodeclic.com) k získání těchto hodnot.

1. V **podpisový certifikát SAML** vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

1. Vyberte **Uložit**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/vodeclic-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **Vodeclic** straně, odeslat na stažený **soubor XML s metadaty** k [tým podpory Vodeclic](mailto:hotline@vodeclic.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com) při nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a přístup k vložený dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace na [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/vodeclic-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/vodeclic-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/vodeclic-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-vodeclic-test-user"></a>Vytvoření zkušebního uživatele Vodeclic

V této části vytvoříte uživatele v Vodeclic jako Britta Simon. Práce s [tým podpory Vodeclic](mailto:hotline@vodeclic.com) přidat uživatele na platformě Vodeclic. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

> [!NOTE]
> Podle požadavků aplikací můžete potřebovat získat seznam povolených adres vašeho počítače. Pro, které se provedou, budete chtít sdílet svou veřejnou IP adresu s [tým podpory Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Vodeclic použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Chcete-li přiřadit Vodeclic Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Dále přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Vodeclic**.

    ![Odkaz Vodeclic v seznamu aplikací](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu.

1. V **uživatelů a skupin** dialogové okno, vyberte **vyberte** tlačítko.

1. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici Vodeclic na přístupovém panelu, můžete získat automaticky přihlášeni k Vodeclic aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/vodeclic-tutorial/tutorial_general_203.png

