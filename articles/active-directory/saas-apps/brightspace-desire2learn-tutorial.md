---
title: 'Kurz: Integrace Azure Active Directory s Brightspace podle Desire2Learn | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Brightspace podle Desire2Learn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 28bd444bffd67e9c13df597d103019610243be33
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823551"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Kurz: Integrace Azure Active Directory s Brightspace podle Desire2Learn

V tomto kurzu se dozvíte, jak integrovat Brightspace podle Desire2Learn s Azure Active Directory (Azure AD).

Integrace Brightspace podle Desire2Learn s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Brightspace podle Desire2Learn
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Brightspace podle Desire2Learn (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Brightspace podle Desire2Learn, potřebujete následující položky:

- Předplatné Azure AD
- Brightspace podle Desire2Learn jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Brightspace podle Desire2Learn z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-brightspace-by-desire2learn-from-the-gallery"></a>Přidání Brightspace podle Desire2Learn z Galerie
Konfigurace integrace Brightspace podle Desire2Learn do služby Azure AD, budete muset přidat Brightspace podle Desire2Learn z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Brightspace podle Desire2Learn z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Brightspace podle Desire2Learn**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_search.png)

1. Na panelu výsledků vyberte **Brightspace podle Desire2Learn**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Brightspace podle Desire2Learn podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Brightspace podle Desire2Learn je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Brightspace podle Desire2Learn potřeba navázat.

V Brightspace podle Desire2Learn přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Brightspace podle Desire2Learn, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření Brightspace Desire2Learn testovací uživatel](#creating-a-brightspace-by-desire2learn-test-user)**  – Pokud chcete mít protějšek Britta Simon Brightspace podle Desire2Learn, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich Brightspace Desire2Learn aplikací.

**Ke konfiguraci Azure AD jednotné přihlašování s Brightspace podle Desire2Learn, proveďte následující kroky:**

1. Na webu Azure Portal na **Brightspace podle Desire2Learn** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_samlbase.png)

1. Na **Brightspace Desire2Learn domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.tenants.brightspace.com/samlLogin`|
    | `https://<companyname>.desire2learn.com/shibboleth-sp`|

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [Brightspace týmem podpory Desire2Learn](https://www.d2l.com/contact/) k získání těchto hodnot.
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/brightspace-desire2learn-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Brightspace podle Desire2Learn** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [Brightspace týmem podpory Desire2Learn](https://www.d2l.com/contact/).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/brightspace-desire2learn-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/brightspace-desire2learn-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/brightspace-desire2learn-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/brightspace-desire2learn-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-brightspace-by-desire2learn-test-user"></a>Vytváření Brightspace podle Desire2Learn testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do Brightspace podle Desire2Learn, musí být poskytnuty do Brightspace podle Desire2Learn.  

V případě Brightspace podle Desire2Learn uživatelské účty musí být vytvořené vaší [Brightspace týmem podpory Desire2Learn](https://www.d2l.com/contact/).

>[!NOTE]
>Můžete použít jiné Brightspace pomocí nástrojů pro vytváření Desire2Learn uživatelského účtu nebo rozhraní API poskytovaných Brightspace podle Desire2Learn ke zřízení služby Azure Active Directory uživatelské účty. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Brightspace podle Desire2Learn.

![Přiřadit uživatele][200] 

**Přiřadit Brightspace podle Desire2Learn Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Brightspace podle Desire2Learn**.

    ![Konfigurace jednotného přihlašování](./media/brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete Brightspace podle Desire2Learn dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší Brightspace Desire2Learn aplikací.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/brightspace-desire2learn-tutorial/tutorial_general_01.png
[2]: ./media/brightspace-desire2learn-tutorial/tutorial_general_02.png
[3]: ./media/brightspace-desire2learn-tutorial/tutorial_general_03.png
[4]: ./media/brightspace-desire2learn-tutorial/tutorial_general_04.png

[100]: ./media/brightspace-desire2learn-tutorial/tutorial_general_100.png

[200]: ./media/brightspace-desire2learn-tutorial/tutorial_general_200.png
[201]: ./media/brightspace-desire2learn-tutorial/tutorial_general_201.png
[202]: ./media/brightspace-desire2learn-tutorial/tutorial_general_202.png
[203]: ./media/brightspace-desire2learn-tutorial/tutorial_general_203.png

