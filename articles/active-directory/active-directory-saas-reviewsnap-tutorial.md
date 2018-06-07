---
title: 'Kurz: Azure Active Directory integrace s Reviewsnap | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Reviewsnap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b00fb373-2b31-4dcf-84ce-abc29e4c639c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 6af9384cbd675cab7a7a7ca9657fef21860a7c2a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654762"
---
# <a name="tutorial-azure-active-directory-integration-with-reviewsnap"></a>Kurz: Azure Active Directory integrace s Reviewsnap

V tomto kurzu zjistěte, jak integrovat Reviewsnap s Azure Active Directory (Azure AD).

Integrace Reviewsnap s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Reviewsnap.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Reviewsnap (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Reviewsnap, potřebujete následující položky:

- Předplatné služby Azure AD
- Reviewsnap jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Reviewsnap z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-reviewsnap-from-the-gallery"></a>Přidání Reviewsnap z Galerie
Při konfiguraci integrace Reviewsnap do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Reviewsnap z galerie.

**Pokud chcete přidat Reviewsnap z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Reviewsnap**, vyberte **Reviewsnap** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Reviewsnap v seznamu výsledků](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Reviewsnap podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Reviewsnap je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Reviewsnap musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Reviewsnap, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Reviewsnap](#create-a-reviewsnap-test-user)**  – Pokud chcete mít protějšek Britta Simon v Reviewsnap propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Reviewsnap.

**Ke konfiguraci Azure AD jednotné přihlašování s Reviewsnap, proveďte následující kroky:**

1. Na portálu Azure na **Reviewsnap** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_samlbase.png)

3. Na **Reviewsnap domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Reviewsnap domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://app.reviewsnap.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://app.reviewsnap.com/auth/saml/callback?namespace=<CUSTOMER_NAMESPACE>
`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Reviewsnap domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://app.reviewsnap.com/login`
     
    > [!NOTE] 
    > Adresa URL odpovědi hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory Reviewsnap klienta](mailto:support@reviewsnap.com) k získání hodnoty. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_400.png)
    
7. Na **Reviewsnap konfigurace** klikněte na tlačítko **konfigurace Reviewsnap** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Reviewsnap](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_configure.png) 

8. Konfigurace jednotného přihlašování na **Reviewsnap** straně, budete muset odeslat stažené **certifikát (Base64), adresu URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [Reviewsnap tým podpory](mailto:support@reviewsnap.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-reviewsnap-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-reviewsnap-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-reviewsnap-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-reviewsnap-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-reviewsnap-test-user"></a>Vytvoření zkušebního uživatele Reviewsnap

V této části vytvoříte volal Britta Simon v Reviewsnap uživatele. Práce s [tým podpory Reviewsnap](mailto:support@reviewsnap.com) přidat uživatele do Reviewsnap platformy. Uživatelé musí být vytvořen a aktivovat před použitím jednotného přihlašování

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Reviewsnap.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Reviewsnap, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Reviewsnap**.

    ![V seznamu aplikací na Reviewsnap odkaz](./media/active-directory-saas-reviewsnap-tutorial/tutorial_reviewsnap_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Reviewsnap na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Reviewsnap.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-reviewsnap-tutorial/tutorial_general_203.png

