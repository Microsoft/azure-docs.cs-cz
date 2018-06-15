---
title: 'Kurz: Azure Active Directory integrace s Origami | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: c8943d6cdda21970167846d9301556d20750614d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351847"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Kurz: Azure Active Directory integrace s Origami

V tomto kurzu zjistěte, jak integrovat Origami s Azure Active Directory (Azure AD).

Integrace Origami s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Origami
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Origami (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Origami, potřebujete následující položky:

- Předplatné služby Azure AD
- Origami jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Origami z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-origami-from-the-gallery"></a>Přidání Origami z Galerie
Při konfiguraci integrace Origami do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Origami z galerie.

**Pokud chcete přidat Origami z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Origami**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_search.png)

5. Na panelu výsledků vyberte **Origami**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Origami podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Origami je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Origami musí navázat.

V Origami, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Origami, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele Origami](#creating-an-origami-test-user)**  – Pokud chcete mít protějšek Britta Simon v Origami propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Origami.

**Ke konfiguraci Azure AD jednotné přihlašování s Origami, proveďte následující kroky:**

1. Na portálu Azure na **Origami** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_samlbase.png)

3. Na **Origami domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Origami klienta](https://wordpress.org/support/theme/origami) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_general_400.png)

6. Na **Origami konfigurace** klikněte na tlačítko **konfigurace Origami** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_configure.png) 

7. Přihlaste se k účtu Origami pomocí oprávnění správce.

8. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

9. V jednom přihlášení na stránce instalačního programu dialogové okno proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_531.png)

    a. Vyberte **povolení jednotného přihlašování na**.

    b. V **zprostředkovatele Identity adresu URL přihlašovací stránky** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    c. V **adresa URL stránky Sign-out zprostředkovatele Identity** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    d. Klikněte na tlačítko **Procházet** na kterou odešlete certifikát, který jste si stáhli z portálu Azure.

    e. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-origami-test-user"></a>Vytváření testovacího uživatele Origami

V této části vytvoříte volal Britta Simon v Origami uživatele. 

1. Přihlaste se k účtu Origami pomocí oprávnění správce.

2. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. Na **uživatelů a zabezpečení** dialogové okno, klikněte na tlačítko **uživatelé**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Klikněte na tlačítko **přidat nového uživatele**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. V dialogovém okně Přidat nové uživatele proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    a. V **uživatelské jméno** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    b. V **heslo** textovému poli, zadejte heslo.

    c. V **Potvrdit heslo** textovému poli, zadejte heslo znovu.

    d. V **křestní jméno** textovému poli, zadejte jméno uživatele jako **Britta**.

    e. V **příjmení** textovému poli, zadejte příjmení uživatele jako **Simon**.

    f. Klikněte na **Uložit**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

6. Přiřadit **role uživatele** a **klientský přístup** uživateli. 
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Origami.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Origami, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Origami**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-origami-tutorial/tutorial_origami_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Origami na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Origami.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-origami-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png

