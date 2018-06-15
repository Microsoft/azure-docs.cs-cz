---
title: 'Kurz: Azure Active Directory integrace s TargetProcess | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 0f2dba144f0b7ff70e7c9996e82d0f841a76854b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346526"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Kurz: Azure Active Directory integrace s TargetProcess

V tomto kurzu zjistěte, jak integrovat TargetProcess s Azure Active Directory (Azure AD).

Integrace TargetProcess s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TargetProcess
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TargetProcess (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TargetProcess, potřebujete následující položky:

- Předplatné služby Azure AD
- TargetProcess jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat TargetProcess z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-targetprocess-from-the-gallery"></a>Přidat TargetProcess z Galerie
Při konfiguraci integrace TargetProcess do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS TargetProcess z galerie.

**Pokud chcete přidat TargetProcess z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **TargetProcess**, vyberte **TargetProcess** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Přidat TargetProcess z Galerie](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s TargetProcess podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v TargetProcess je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TargetProcess musí navázat.

V TargetProcess, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TargetProcess, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele TargetProcess](#create-a-targetprocess-test-user)**  – Pokud chcete mít protějšek Britta Simon v TargetProcess propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci TargetProcess.

**Ke konfiguraci Azure AD jednotné přihlašování s TargetProcess, proveďte následující kroky:**

1. Na portálu Azure na **TargetProcess** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Přihlašování založené na SAML](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_samlbase.png)

3. Na **TargetProcess domény a adresy URL** část, proveďte následující kroky:

    ![Část TargetProcess domény a adresy URL](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.tpondemand.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory TargetProcess klienta](mailto:support@targetprocess.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Certifikát pro podpis SAML části](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/active-directory-saas-target-process-tutorial/tutorial_general_400.png)

6. Na **TargetProcess konfigurace** klikněte na tlačítko **konfigurace TargetProcess** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![TargetProcess konfigurační oddíl](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_configure.png) 

7. Přihlášení do aplikace TargetProcess jako správce.

8. V nabídce v horní části, klikněte na tlačítko **instalační program**.
   
    ![Nastavení](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

9. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 

10. Klikněte na tlačítko **jednotného přihlašování**.
   
    ![Klikněte na jednotné přihlašování](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 

11. Na jednotné přihlašování v dialogovém okně nastavení proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)
    
    a. Klikněte na tlačítko **povolit jednotné přihlašování**.
    
    b. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. V poznámkovém bloku otevřete stažený certifikát, kopírovat obsah a vložte ji do **certifikát** textové pole.
    
    d. Klikněte na tlačítko **povolit zřizování JIT**.

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-target-process-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Chcete-li zobrazit seznam uživatelů](./media/active-directory-saas-target-process-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/active-directory-saas-target-process-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Oddíl uživatele](./media/active-directory-saas-target-process-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-targetprocess-test-user"></a>Vytvoření zkušebního uživatele TargetProcess

Cílem této části je vytvoření uživatele v TargetProcess nazývá Britta Simon.

TargetProcess podporuje zřizování za běhu. Již je v povolíte [konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

Neexistuje žádná položka akce pro vás v této části.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TargetProcess.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon TargetProcess, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TargetProcess**.

    ![TargetProcess v seznamu aplikací](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici TargetProcess na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci TargetProcess. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png

