---
title: 'Kurz: Azure Active Directory integrace s SciQuest tráví ředitel | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SciQuest tráví ředitel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: fd0bf33f7929d67839303d5a1e584a25862e58fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352714"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Kurz: Azure Active Directory integrace s SciQuest tráví ředitel

V tomto kurzu zjistěte, jak integrovat SciQuest tráví ředitel s Azure Active Directory (Azure AD).

Integrace SciQuest tráví ředitel s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SciQuest tráví ředitel.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SciQuest tráví ředitel (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SciQuest tráví ředitel, potřebujete následující položky:

- Předplatné služby Azure AD
- SciQuest tráví ředitel jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SciQuest tráví ředitel z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Přidání SciQuest tráví ředitel z Galerie
Při konfiguraci integrace SciQuest tráví ředitel do služby Azure AD potřebujete přidat SciQuest tráví ředitel z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat SciQuest tráví ředitel z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SciQuest tráví ředitel**, vyberte **SciQuest tráví ředitel** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SciQuest tráví nacházející v seznamu výsledků](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SciQuest tráví ředitel podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v SciQuest tráví nacházející se na uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SciQuest tráví ředitel musí navázat.

V SciQuest tráví ředitel přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SciQuest tráví ředitel, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ředitel tráví SciQuest](#create-a-sciquest-spend-director-test-user)**  – Pokud chcete mít protějšek Britta Simon v tráví ředitel SciQuest, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SciQuest tráví ředitel.

**Ke konfiguraci Azure AD jednotné přihlašování s SciQuest tráví ředitel, proveďte následující kroky:**

1. Na portálu Azure na **SciQuest tráví ředitel** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_samlbase.png)

3. Na **SciQuest tráví ředitel domény a adresy URL** část, proveďte následující kroky:

    ![SciQuest tráví ředitel domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.sciquest.com`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory SciQuest tráví ředitel klienta](https://www.jaggaer.com/contact-us/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **SciQuest tráví ředitel** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory SciQuest tráví ředitel](https://www.jaggaer.com/contact-us/).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-sciquest-spend-director-test-user"></a>Vytvoření zkušebního uživatele SciQuest tráví ředitel

Cílem této části je vytvoření uživatele volal Britta Simon v SciQuest tráví ředitel.

Budete muset kontaktovat vaše [tým podpory SciQuest tráví ředitel](https://www.jaggaer.com/contact-us/) a poskytněte s podrobnostmi o vašem účtu testovací získat vytvořen.

Alternativně můžete využít i za běhu zřizování, jeden přihlašování funkce, která podporuje SciQuest tráví ředitel.  
Pokud za běhu zřizování je povolená, uživatelé jsou automaticky vytváří SciQuest tráví ředitel během jednoho pokusu o přihlášení Pokud ještě neexistují. Tato funkce eliminuje nutnost ručně vytvořit protějšku přihlašování uživatelů.

Pokud chcete získat za běhu zřizování povoleno, budete muset kontaktovat vaše [tým podpory SciQuest tráví ředitel](https://www.jaggaer.com/contact-us/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu SciQuest tráví ředitel.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SciQuest tráví ředitel, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SciQuest tráví ředitel**.

    ![Odkaz SciQuest tráví nacházející v seznamu aplikací](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici SciQuest tráví ředitel na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci SciQuest tráví ředitel.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_203.png

