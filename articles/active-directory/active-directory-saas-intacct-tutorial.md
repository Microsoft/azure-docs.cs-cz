---
title: 'Kurz: Azure Active Directory integrace s Intacct | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 54fdfff8ef7c59857fc14e6d54f64a027b2c46a9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Kurz: Azure Active Directory integrace s Intacct

V tomto kurzu zjistěte, jak integrovat Intacct s Azure Active Directory (Azure AD).

Integrace Intacct s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Intacct
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Intacct (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Intacct, potřebujete následující položky:

- Předplatné služby Azure AD
- Intacct jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Intacct z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-intacct-from-the-gallery"></a>Přidání Intacct z Galerie
Při konfiguraci integrace Intacct do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Intacct z galerie.

**Pokud chcete přidat Intacct z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Intacct**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_search.png)

5. Na panelu výsledků vyberte **Intacct**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Intacct podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Intacct je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Intacct musí navázat.

V Intacct, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Intacct, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele Intacct](#creating-an-intacct-test-user)**  – Pokud chcete mít protějšek Britta Simon v Intacct propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Intacct.

**Ke konfiguraci Azure AD jednotné přihlašování s Intacct, proveďte následující kroky:**

1. Na portálu Azure na **Intacct** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_samlbase.png)

3. Na **Intacct domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_url.png)

    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory Intacct](https://us.intacct.com/support) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/tutorial_general_400.png)

6. Na **Intacct konfigurace** klikněte na tlačítko **konfigurace Intacct** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Intacct jako správce.

8. Klikněte **společnosti** a pak klikněte **informace společnosti**.

    ![Společnosti](./media/active-directory-saas-intacct-tutorial/ic790037.png "společnosti")

9. Klikněte **zabezpečení** a pak klikněte **upravit**.

    ![Zabezpečení](./media/active-directory-saas-intacct-tutorial/ic790038.png "zabezpečení")

10. V **jednotné přihlašování (SSO)** část, proveďte následující kroky:

    ![Jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/ic790039.png "jednotné přihlašování")

    a. Vyberte **povolení jednotného přihlašování na**.

    b. Jako **typ zprostředkovatele Identity**, vyberte **SAML 2.0**.

    c. V **URL vystavitele** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
   
    d. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    e. Otevřete váš **kódování base-64** kódování certifikátu v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **certifikát** pole.
   
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-intacct-test-user"></a>Vytváření testovacího uživatele Intacct

Nastavit uživatele Azure AD, takže se můžete přihlásit k Intacct, musí být zřízená do Intacct. Pro Intacct zřizování je ruční úloha.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Intacct** klienta.

2. Klikněte **společnosti** a pak klikněte **uživatelé**.

    ![Uživatelé](./media/active-directory-saas-intacct-tutorial/ic790041.png "uživatelů")
3. Klikněte **přidat** kartě.

    ![Přidat](./media/active-directory-saas-intacct-tutorial/ic790042.png "přidat")
4. V **informace o uživateli** část, proveďte následující kroky:

    ![Informace o uživateli](./media/active-directory-saas-intacct-tutorial/ic790043.png "informace o uživateli")

    a. Zadejte **ID uživatele**, **příjmení**, **křestní jméno**, **e-mailová adresa**, **název**a **Phone** účtu Azure AD, který chcete mají být zahrnuty do **informace o uživateli** části.

    b. Vyberte **oprávnění správce** účtu Azure AD, který chcete zřídit.
   
    c. Klikněte na **Uložit**. Držitel účtu Azure AD obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

>[!NOTE]
>Ke zřízení uživatelských účtů Azure AD, můžete použít jiné nástroje pro tvorbu Intacct uživatelského účtu nebo rozhraní API, které jsou poskytovány Intacct.
        
### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Intacct.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Intacct, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Intacct**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když kliknete na dlaždici Intacct na přístupovém panelu, můžete by měl být automaticky přihlášeni do vaší aplikace Intacct.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

