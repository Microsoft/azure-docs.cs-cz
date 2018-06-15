---
title: 'Kurz: Azure Active Directory integrace s Optimizely | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 995b440cbf9dc93ec01fe0b86c69ea09cd4714ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346039"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Kurz: Azure Active Directory integrace s Optimizely

V tomto kurzu zjistěte, jak integrovat Optimizely s Azure Active Directory (Azure AD).

Integrace Optimizely s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Optimizely
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Optimizely (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Optimizely, potřebujete následující položky:

- Předplatné služby Azure AD
- Optimizely jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Optimizely z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-optimizely-from-the-gallery"></a>Přidání Optimizely z Galerie
Při konfiguraci integrace Optimizely do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Optimizely z galerie.

**Pokud chcete přidat Optimizely z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Optimizely**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. Na panelu výsledků vyberte **Optimizely**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Optimizely podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Optimizely je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Optimizely musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Optimizely.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Optimizely, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele Optimizely](#creating-an-optimizely-test-user)**  – Pokud chcete mít protějšek Britta Simon v Optimizely propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Optimizely.

**Ke konfiguraci Azure AD jednotné přihlašování s Optimizely, proveďte následující kroky:**

1. Na portálu Azure na **Optimizely** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Na **Optimizely domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://app.optimizely.net/<instance name>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:  `urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte hodnotu s skutečné přihlašovací adresa URL a identifikátor, který je vysvětlen později v tomto kurzu. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. Na **Optimizely konfigurace** klikněte na tlačítko **konfigurace Optimizely** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Konfigurace jednotného přihlašování na **Optimizely** straně, kontaktujte vašeho Account manažera Optimizely a poskytují stažené **certifikátu (Base64)**, a **SAML jeden přihlašování adresa URL služby**. 

8. V reakci na e-mailu Optimizely poskytuje adresu URL na přihlášení (SSO spouštěná SP) a hodnot identifikátoru (ID Entity poskytovatele služby).

    a. Kopírování **URL jednotné přihlašování iniciované SP** zadaný Optimizely a vložit do **přihlašovací adresa URL** textového pole v **Optimizely domény a adresy URL** části na portálu Azure 

    b. Kopírování **ID Entity poskytovatele služby** zadaný Optimizely a vložit do **identifikátor** textového pole v **Optimizely domény a adresy URL** části na portálu Azure 

9. V okně jiný prohlížeč přihlášení do aplikace Optimizely.

10. Kliknutím můžete: název účtu v horním pravém rohu a pak **nastavení účtu**.
   
    ![Azure AD jednotné přihlášení](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. Na kartě Účet zaškrtněte políčko **povolit jednotné přihlašování** pod jednotné přihlašování v **přehled** části.
   
    ![Azure AD jednotné přihlášení](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z Britta Simon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-optimizely-test-user"></a>Vytváření testovacího uživatele Optimizely

V této části vytvoříte volal Britta Simon v Optimizely uživatele.

1. Na domovské stránce vyberte **spolupracovníci** kartě.

2. Chcete-li přidat nový spolupracovník do projektu, klikněte na tlačítko **nové spolupracovník**.
   
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Zadejte e-mailovou adresu a přiřadit jim roli. Klikněte na tlačítko **pozvat**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Zobrazí se jim pozvání e-mailu. Pomocí e-mailovou adresu, mají k Optimizely přihlásit.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Optimizely.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Optimizely, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Optimizely**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Optimizely na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Optimizely. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png

