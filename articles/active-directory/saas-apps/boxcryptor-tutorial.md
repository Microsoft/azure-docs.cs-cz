---
title: 'Kurz: Azure Active Directory integrace s Boxcryptor | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Boxcryptor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c46aa523-b58c-4a95-a800-db2e5e01c542
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeedes
ms.openlocfilehash: 8e1008636fe442d6d00a36fecd63a6e48f2cce04
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220046"
---
# <a name="tutorial-azure-active-directory-integration-with-boxcryptor"></a>Kurz: Azure Active Directory integrace s Boxcryptor

V tomto kurzu zjistěte, jak integrovat Boxcryptor s Azure Active Directory (Azure AD).

Integrace Boxcryptor s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Boxcryptor.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Boxcryptor (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Boxcryptor, potřebujete následující položky:

- Předplatné služby Azure AD
- Boxcryptor jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Boxcryptor z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-boxcryptor-from-the-gallery"></a>Přidání Boxcryptor z Galerie
Při konfiguraci integrace Boxcryptor do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Boxcryptor z galerie.

**Pokud chcete přidat Boxcryptor z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Boxcryptor**, vyberte **Boxcryptor** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Boxcryptor v seznamu výsledků](./media/boxcryptor-tutorial/tutorial_boxcryptor_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Boxcryptor podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Boxcryptor je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Boxcryptor musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Boxcryptor, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Boxcryptor](#create-a-boxcryptor-test-user)**  – Pokud chcete mít protějšek Britta Simon v Boxcryptor propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Boxcryptor.

**Ke konfiguraci Azure AD jednotné přihlašování s Boxcryptor, proveďte následující kroky:**

1. Na portálu Azure na **Boxcryptor** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/boxcryptor-tutorial/tutorial_boxcryptor_samlbase.png)

3. Na **Boxcryptor domény a adresy URL** část, proveďte následující kroky:

    ![Boxcryptor domény a adresy URL jednotné přihlašování informace](./media/boxcryptor-tutorial/tutorial_boxcryptor_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://www.boxcryptor.com/app`

    b. V **identifikátor** textovému poli, typ a hodnotu: `boxcryptor`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/boxcryptor-tutorial/tutorial_boxcryptor_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/boxcryptor-tutorial/tutorial_general_400.png)

6. Na **Boxcryptor konfigurace** klikněte na tlačítko **konfigurace Boxcryptor** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** a **SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurace SCM Andromeda](./media/boxcryptor-tutorial/tutorial_boxcryptor_configure.png)

7. Konfigurace jednotného přihlašování na **Boxcryptor** straně, budete muset odeslat stažené **certifikátu (Base64)**, **SAML jeden přihlašování adresa URL služby** a **SAML Entity ID** k [tým podpory Boxcryptor](mailto:support@boxcryptor.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/boxcryptor-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/boxcryptor-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/boxcryptor-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/boxcryptor-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-boxcryptor-test-user"></a>Vytvoření zkušebního uživatele Boxcryptor

V této části vytvoříte volal Britta Simon v Boxcryptor uživatele. Práce s [tým podpory Boxcryptor](mailto:support@boxcryptor.com) přidat uživatele nebo doménu, která je potřeba na seznam povolených adres Boxcryptor platformy. Pokud doména je přidána tým, se získat uživatelé automaticky zřizovat Boxcryptor platformě. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Boxcryptor.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Boxcryptor, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Boxcryptor**.

    ![V seznamu aplikací na Boxcryptor odkaz](./media/boxcryptor-tutorial/tutorial_boxcryptor_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Boxcryptor na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Boxcryptor.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boxcryptor-tutorial/tutorial_general_01.png
[2]: ./media/boxcryptor-tutorial/tutorial_general_02.png
[3]: ./media/boxcryptor-tutorial/tutorial_general_03.png
[4]: ./media/boxcryptor-tutorial/tutorial_general_04.png

[100]: ./media/boxcryptor-tutorial/tutorial_general_100.png

[200]: ./media/boxcryptor-tutorial/tutorial_general_200.png
[201]: ./media/boxcryptor-tutorial/tutorial_general_201.png
[202]: ./media/boxcryptor-tutorial/tutorial_general_202.png
[203]: ./media/boxcryptor-tutorial/tutorial_general_203.png

