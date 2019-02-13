---
title: 'Kurz: Integrace Azure Active Directory s SciQuest věnovat ředitel | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ředitel SciQuest výdajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75c8f4111ec5679dd04ec23c3e8fb3b2b8634825
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202719"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Kurz: Integrace Azure Active Directory s SciQuest věnovat ředitel

V tomto kurzu se dozvíte, jak integrovat SciQuest věnovat ředitel Azure Active Directory (Azure AD).

Ředitel věnovat SciQuest integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k ředitel SciQuest výdajů.
- Uživatele, aby automaticky získat přihlášení k SciQuest ředitel výdajů (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SciQuest věnovat ředitel, potřebujete následující položky:

- Předplatné Azure AD
- Ředitel věnovat SciQuest jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SciQuest věnovat ředitel z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Přidání SciQuest věnovat ředitel z Galerie
Pokud chcete nakonfigurovat integraci SciQuest věnovat ředitel do služby Azure AD, budete muset SciQuest věnovat ředitel přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Ředitel věnovat SciQuest přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **SciQuest věnovat ředitel**vyberte **SciQuest věnovat ředitel** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Ředitel SciQuest výdajů v seznamu výsledků](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ředitel SciQuest výdaje podle testovacího uživatele nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co uživatel protějšek v SciQuest věnovat ředitel je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v SciQuest věnovat ředitel potřeba navázat.

V SciQuest výdaje, ředitel přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SciQuest věnovat ředitel, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SciQuest věnovat ředitel](#create-a-sciquest-spend-director-test-user)**  – Pokud chcete mít protějšek Britta Simon věnovat ředitel SciQuest, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a nakonfigurovat jednotné přihlašování ve vaší aplikaci ředitel SciQuest výdajů.

**Ke konfiguraci Azure AD jednotné přihlašování s SciQuest věnovat ředitel, proveďte následující kroky:**

1. Na webu Azure Portal na **SciQuest věnovat ředitel** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_samlbase.png)

1. Na **SciQuest věnovat ředitel domény a adresy URL** části, proveďte následující kroky:

    ![SciQuest věnovat ředitel domény a adresy URL jednotného přihlašování – informace](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.sciquest.com`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory SciQuest věnovat ředitel klienta](https://www.jaggaer.com/contact-us/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/sciquest-spend-director-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **SciQuest věnovat ředitel** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory SciQuest věnovat ředitel](https://www.jaggaer.com/contact-us/).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sciquest-spend-director-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/sciquest-spend-director-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sciquest-spend-director-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sciquest-spend-director-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-sciquest-spend-director-test-user"></a>Vytvoření zkušebního uživatele SciQuest věnovat ředitel

Cílem této části je vytvořte uživatele Britta Simon v ředitel SciQuest výdajů.

Budete muset kontaktovat vaše [tým podpory SciQuest věnovat ředitel](https://www.jaggaer.com/contact-us/) a poskytnout podrobné informace o účtu testu ho vytvořili.

Alternativně můžete taky využít just-in-time zřizování, jednotné přihlašování funkce, která podporuje ředitel SciQuest výdajů.  
Pokud zřizování just-in-time je povolená, uživatelé automaticky vytvořila SciQuest věnovat ředitel během jedné pokusu o přihlášení Pokud ještě neexistují. Tato funkce eliminuje nutnost ručně vytvořit protějšek jednotné přihlašování uživatelů.

Získat just-in-time zřizování povolena, budete muset požádat vašeho [tým podpory SciQuest věnovat ředitel](https://www.jaggaer.com/contact-us/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k SciQuest věnovat ředitel.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit SciQuest věnovat ředitel, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SciQuest věnovat ředitel**.

    ![Odkaz ředitel SciQuest výdajů v seznamu aplikací](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ředitel SciQuest výdaje na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SciQuest věnovat ředitel.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/sciquest-spend-director-tutorial/tutorial_general_04.png

[100]: ./media/sciquest-spend-director-tutorial/tutorial_general_100.png

[200]: ./media/sciquest-spend-director-tutorial/tutorial_general_200.png
[201]: ./media/sciquest-spend-director-tutorial/tutorial_general_201.png
[202]: ./media/sciquest-spend-director-tutorial/tutorial_general_202.png
[203]: ./media/sciquest-spend-director-tutorial/tutorial_general_203.png

