---
title: 'Kurz: Azure Active Directory integrace s Form.com | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Form.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f1bc0112-315c-4e6f-8c69-7c6873007bcf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 0b1d990337c7c5caaee79bc8e3280c2690fc47b0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-formcom"></a>Kurz: Azure Active Directory integrace s Form.com

V tomto kurzu zjistěte, jak integrovat Form.com s Azure Active Directory (Azure AD).

Integrace Form.com s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Form.com.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Form.com (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Form.com, potřebujete následující položky:

- Předplatné služby Azure AD
- Form.com jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Form.com z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-formcom-from-the-gallery"></a>Přidání Form.com z Galerie
Při konfiguraci integrace Form.com do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Form.com z galerie.

**Pokud chcete přidat Form.com z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Form.com**, vyberte **Form.com** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Form.com v seznamu výsledků](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Form.com podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Form.com je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Form.com musí navázat.

V Form.com, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Form.com, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Form.com](#create-a-formcom-test-user)**  – Pokud chcete mít protějšek Britta Simon v Form.com propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Form.com.

**Ke konfiguraci Azure AD jednotné přihlašování s Form.com, proveďte následující kroky:**

1. Na portálu Azure na **Form.com** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_samlbase.png)

3. Na **Form.com domény a adresy URL** část, proveďte následující kroky:

    ![Form.com domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.wa-form.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.form.com`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<subdomain>.wa-form.com/Member/UserAccount/SAML2.action` |
    | `https://<subdomain>.form.com/Member/UserAccount/SAML2.action` |
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, adresa URL odpovědi a identifikátor. Obraťte se na [tým podpory Form.com klienta](https://form.com/about/company/contact-us/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** část, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-formcom-tutorial/tutorial_metadataurl.png)

    a. Klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

    b. Klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.
     
5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-formcom-tutorial/tutorial_general_400.png)

6. Na **Form.com konfigurace** klikněte na tlačítko **konfigurace Form.com** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Form.com](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_configure.png) 

7. Konfigurace jednotného přihlašování na **Form.com** straně, budete muset odeslat stažené **certifikátu (Base64)**, **adresu Url aplikace federační Metadata**, a **SAML Jednotné přihlašování adresa URL služby** k [tým podpory Form.com](https://form.com/about/company/contact-us/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-formcom-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-formcom-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-formcom-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-formcom-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-formcom-test-user"></a>Vytvoření zkušebního uživatele Form.com

Cílem této části je vytvoření uživatele v Form.com nazývá Britta Simon. Práce s [tým podpory Form.com](https://form.com/about/company/contact-us/) přidat uživatele do Form.com účtu.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Form.com.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Form.com, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Form.com**.

    ![V seznamu aplikací na Form.com odkaz](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Form.com na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Form.com.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_203.png

