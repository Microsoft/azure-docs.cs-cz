---
title: 'Kurz: Azure Active Directory integrace s Netsuite | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: 2f2bd227a3d8c0b797f37026032938fbcfe3de9e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Kurz: Azure Active Directory integrace s Netsuite

V tomto kurzu zjistěte, jak integrovat Netsuite s Azure Active Directory (Azure AD).

Integrace Netsuite s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Netsuite
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Netsuite (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Netsuite, potřebujete následující položky:

- Předplatné služby Azure AD
- Netsuite jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Netsuite z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-netsuite-from-the-gallery"></a>Přidání Netsuite z Galerie
Při konfiguraci integrace Netsuite do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Netsuite z galerie.

**Pokud chcete přidat Netsuite z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Netsuite**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. Na panelu výsledků vyberte **Netsuite**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Netsuite podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Netsuite je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Netsuite musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Netsuite.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Netsuite, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Netsuite](#creating-a-netsuite-test-user)**  – Pokud chcete mít protějšek Britta Simon v Netsuite propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Netsuite.

**Ke konfiguraci Azure AD jednotné přihlašování s Netsuite, proveďte následující kroky:**

1. Na portálu Azure na **Netsuite** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. Na **Netsuite domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:   `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Tyto nejsou skutečné hodnoty. Tyto hodnoty aktualizujte s skutečná adresa URL odpovědi. Obraťte se na [tým podpory Netsuite](http://www.netsuite.com/portal/services/support.shtml) k získání těchto hodnot.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. Na **Netsuite konfigurace** klikněte na tlačítko **konfigurace Netsuite** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. V prohlížeči otevřete novou kartu a přihlaste se k serveru vaší společnosti Netsuite jako správce.

8. Na panelu nástrojů v horní části stránky klikněte na tlačítko **instalace**, pak klikněte na tlačítko **Správce instalace**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Z **úlohy nastavení** seznamu, vyberte **integrace**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. V **spravovat ověřování** klikněte na tlačítko **SAML jednotné přihlašování**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. Na **SAML instalace** proveďte následující kroky:
   
    a. Kopírování **SAML jeden přihlašování adresa URL služby** z hodnoty **Stručná referenční příručka** části **konfigurovat přihlášení** a vložte ji do **stránka přihlášení zprostředkovatele Identity** pole Netsuite.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. V Netsuite, vyberte **primární metoda ověřování**.

    c. Pro pole s názvem bez přípony **metadat zprostředkovatelů Identity SAMLV2**, vyberte **nahrát soubor metadat IDP**. Pak klikněte na tlačítko **Procházet** nahrát soubor metadat, který jste si stáhli z portálu Azure.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Klikněte na tlačítko **odeslání**.

12. Ve službě Azure AD, klikněte na **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtávací políčko a přidání atributu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Pro **název atributu** pole, zadejte v `account`. Pro **hodnota atributu** pole, zadejte v ID Netsuite účtu. Tato hodnota je konstanta a změňte pomocí účtu. Pokyny o tom, jak najít ID vašeho účtu jsou uvedeny níže:

      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. V Netsuite, klikněte na **instalační program** v horním navigačním panelu nabídce.

    b. Klikněte v části **instalační úlohy, které** levé navigační nabídky, vyberte **integrace** a klikněte na **webové služby Předvolby**.

    c. Vaše ID pro účet Netsuite zkopírujte a vložte ji do **hodnota atributu** pole ve službě Azure AD.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Předtím, než mohou uživatelé provádět jednotné přihlašování do Netsuite, je třeba nejprve je přiřadit v Netsuite příslušná oprávnění. Postupujte podle pokynů pro toto přiřazení oprávnění.

    a. V nabídce horním navigačním panelu klikněte na tlačítko **instalace**, pak klikněte na tlačítko **Správce instalace**.
      
      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. V levém navigačním nabídce vyberte **uživatele/role**, pak klikněte na tlačítko **spravovat role**.
      
      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Klikněte na tlačítko **novou roli**.

    d. Zadejte **název** pro novou roli.
      
      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Klikněte na **Uložit**.

    f. V nabídce v horní části, klikněte na tlačítko **oprávnění**. Pak klikněte na tlačítko **instalační program**.
      
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Vyberte **nastavit až SAML jednotné přihlašování**a potom klikněte na **přidat**.

    h. Klikněte na **Uložit**.

    i. V nabídce horním navigačním panelu klikněte na tlačítko **instalace**, pak klikněte na tlačítko **Správce instalace**.
      
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. V levém navigačním nabídce vyberte **uživatele/role**, pak klikněte na tlačítko **spravovat uživatele**.
      
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Vyberte testovací uživatele. Pak klikněte na tlačítko **upravit**.
      
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. V dialogovém okně rolí vyberte roli, který jste vytvořili a klikněte na tlačítko **přidat**.
      
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Klikněte na **Uložit**.
    
### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na položku **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-netsuite-test-user"></a>Vytvoření zkušebního uživatele Netsuite

V této části se uživatel volá Britta Simon vytvoří v Netsuite. Netsuite podporuje za běhu zřizování, který je ve výchozím nastavení povolené.
Neexistuje žádná položka akce pro vás v této části. Pokud uživatel v Netsuite ještě neexistuje, vytvoří se při pokusu o přístup k Netsuite novou.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Netsuite.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Netsuite, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Netsuite**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

K testování vaše nastavení jednotného přihlašování, otevřete Panel přístupu na [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), přihlaste se k testovací účet a klikněte na tlačítko **Netsuite**.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

