---
title: 'Kurz: Azure Active Directory integrace s Eli registrace | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Eli registrace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58579baf-53fb-4c34-a6aa-648ad8a22039
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: c6853c5dd3ec320afd70e095fe1909c7942f52e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320552"
---
# <a name="tutorial-azure-active-directory-integration-with-eli-onboarding"></a>Kurz: Azure Active Directory integrace s Eli registrace

V tomto kurzu zjistěte, jak integrovat Eli registrace v Azure Active Directory (Azure AD).

Integrace Eli registrace s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Eli registrace.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k připojování Eli (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Eli registrace, potřebujete následující položky:

- Předplatné služby Azure AD
- Registraci Eli jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Eli registrace z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-eli-onboarding-from-the-gallery"></a>Přidání Eli registrace z Galerie
Při konfiguraci integrace Eli registrace do služby Azure AD potřebujete přidat Eli registrace z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Eli registrace z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Eli registrace**, vyberte **Eli registrace** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Registrace eli v seznamu výsledků](./media/elionboarding-tutorial/tutorial_elionboarding_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Eli registrace podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Eli registrace je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Eli registrace musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Eli registrace, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Eli registrace](#create-an-eli-onboarding-test-user)**  – Pokud chcete mít protějšek Britta Simon v Eli registrace, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Eli registrace.

**Ke konfiguraci Azure AD jednotné přihlašování s Eli registrace, proveďte následující kroky:**

1. Na portálu Azure na **Eli registrace** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/elionboarding-tutorial/tutorial_elionboarding_samlbase.png)

3. Na **Eli registrace domény a adresy URL** část, proveďte následující kroky:

    ![Registrace domény eli a adresy URL jednotné přihlašování informace](./media/elionboarding-tutorial/tutorial_elionboarding_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<YOUR DOMAIN URL>/sso/saml/login`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<YOUR DOMAIN URL>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Eli registrace klienta](mailto:support@geteli.com) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/elionboarding-tutorial/tutorial_elionboarding_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/elionboarding-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **Eli registrace** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory registrace Eli](mailto:support@geteli.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/elionboarding-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/elionboarding-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/elionboarding-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/elionboarding-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-eli-onboarding-test-user"></a>Vytvořit testovací uživatele s Eli registrace

V této části vytvoříte volal Britta Simon v Eli registrace uživatele. Práce s [tým podpory registrace Eli](mailto:support@geteli.com) přidejte uživatele v platformě Eli registrace. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Eli registrace.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Eli registrace, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Eli registrace**.

    ![V seznamu aplikací na odkaz Eli registrace](./media/elionboarding-tutorial/tutorial_elionboarding_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Eli registrace na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Eli registrace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/elionboarding-tutorial/tutorial_general_01.png
[2]: ./media/elionboarding-tutorial/tutorial_general_02.png
[3]: ./media/elionboarding-tutorial/tutorial_general_03.png
[4]: ./media/elionboarding-tutorial/tutorial_general_04.png

[100]: ./media/elionboarding-tutorial/tutorial_general_100.png

[200]: ./media/elionboarding-tutorial/tutorial_general_200.png
[201]: ./media/elionboarding-tutorial/tutorial_general_201.png
[202]: ./media/elionboarding-tutorial/tutorial_general_202.png
[203]: ./media/elionboarding-tutorial/tutorial_general_203.png

