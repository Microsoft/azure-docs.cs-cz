---
title: 'Kurz: Integrace Azure Active Directory se službou O.C. Nováková - AppreciateHub | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a O.C. Nováková - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 2a3c6641c3fd9402ede2176e3c5c3f3ec15ed9de
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438701"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Kurz: Integrace Azure Active Directory se službou O.C. Nováková - AppreciateHub

V tomto kurzu se dozvíte, jak integrovat O.C. Nováková - AppreciateHub s Azure Active Directory (Azure AD).

Integrace O.C. Nováková - AppreciateHub s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k O.C. Nováková - AppreciateHub
- Můžete povolit uživatelům, aby automaticky získat přihlášení k O.C. Nováková - AppreciateHub (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s O.C. Nováková - AppreciateHub, potřebujete následující položky:

- S předplatným služby Azure AD
- O.C. Nováková - AppreciateHub jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání O.C. Nováková - AppreciateHub z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Přidání O.C. Nováková - AppreciateHub z Galerie
Konfigurace integrace O.C. Nováková - AppreciateHub do služby Azure AD, je třeba přidat O.C. Nováková - AppreciateHub z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat O.C. Nováková - AppreciateHub z galerie, proveďte následující kroky:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **O.C. Nováková - AppreciateHub**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

1. Na panelu výsledků vyberte **O.C. Nováková - AppreciateHub**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub založená na uživateli test "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatel v O.C. Nováková - AppreciateHub je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v O.C. Nováková - AppreciateHub musí být vytvořeno.

V O.C. Nováková - AppreciateHub, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření O.C. Nováková - AppreciateHub testovacího uživatele](#creating-a-oc-tanner---appreciatehub-test-user)**  – Pokud chcete mít protějšek Britta Simon O.C. Nováková - AppreciateHub, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich O.C. Nováková - AppreciateHub aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub, proveďte následující kroky:**

1. Na webu Azure Portal na **O.C. Nováková - AppreciateHub** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

1. Na **O.C. Nováková - AppreciateHub domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu Skutečná adresa URL odpovědi. Kontakt [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com) tuto výhodu získáte.

    b. Otevřete soubor metadat pomocí následujícího odkazu: [ https://fed.appreciatehub.com/fed/sp/metadata ](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Vyhledejte **md:AssertionConsumerService** uzlu. 
   
    d. Zkopírujte hodnotu **umístění** atribut. 
   
    ![Konfigurace nastavení aplikace][12]
   
    e. V **přihlašovací adresa URL** textové pole, poslední hodnota jste získali v předchozím kroku.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/oc-tanner-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **O.C. Nováková - AppreciateHub** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/oc-tanner-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/oc-tanner-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/oc-tanner-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Vytváření O.C. Nováková - AppreciateHub testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v O.C. Nováková - AppreciateHub.

**Vytvořte uživatele v O.C. jako Britta Simon Nováková - AppreciateHub, proveďte následující kroky:**

Požádejte vašeho [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com) pro vytvoření uživatele, který má jako atribut nameID stejnou hodnotu jako uživatelské jméno Britta Simon ve službě Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k O.C. Nováková - AppreciateHub.

![Přiřadit uživatele][200] 

**Chcete-li přiřadit Britta Simon O.C. Nováková - AppreciateHub, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **O.C. Nováková - AppreciateHub**.

    ![Konfigurace jednotného přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.  
Když kliknete O.C. Nováková - AppreciateHub dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší O.C. Nováková - AppreciateHub aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

