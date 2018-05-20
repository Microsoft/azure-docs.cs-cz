---
title: 'Kurz: Azure Active Directory integrace s Salesforce | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 2caf788f1c38c701699e0c275cabe3d12eb75270
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Kurz: Azure Active Directory integrace s Salesforce

V tomto kurzu zjistěte, jak integrovat Salesforce s Azure Active Directory (Azure AD).

Integrace služby Salesforce s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do služby Salesforce.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Salesforce (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí služby Salesforce, potřebujete následující položky:

- Předplatné služby Azure AD
- Služby Salesforce jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Salesforce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-salesforce-from-the-gallery"></a>Přidání Salesforce z Galerie
Při konfiguraci integrace služby Salesforce do služby Azure AD potřebujete přidat Salesforce z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Salesforce z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Salesforce**, vyberte **Salesforce** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Salesforce v seznamu výsledků](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí služby Salesforce podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Salesforce je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Salesforce musí navázat.

V Salesforce, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby Salesforce, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Salesforce](#create-a-salesforce-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Salesforce.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí služby Salesforce, proveďte následující kroky:**

1. Na portálu Azure na **Salesforce** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Na **Salesforce domény a adresy URL** část, proveďte následující kroky:

    ![Doména služby Salesforce a adresy URL jednotné přihlašování informace](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. V **přihlašovací adresa URL** textovému poli, zadejte hodnotu pomocí následujícího vzorce:
    
    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. V **identifikátor** textovému poli, zadejte hodnotu pomocí následujícího vzorce:
    
    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory služby Salesforce klienta](https://help.salesforce.com/support) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikát** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. Na **Salesforce konfigurace** klikněte na tlačítko **konfigurace Salesforce** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace služby Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. V prohlížeči a přihlaste se ke svému účtu Salesforce správce otevřete novou kartu.

8. Klikněte na **instalační program** pod **ikonu nastavení** v pravém horním rohu stránky.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Přejděte dolů k položce **nastavení** v navigačním podokně klikněte na tlačítko **Identity** rozbalte související část. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. Na **nastavení jednotného přihlašování** klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Pokud nelze povolit nastavení jednotného přihlašování pro váš účet Salesforce, budete možná muset kontaktovat [tým podpory služby Salesforce klienta](https://help.salesforce.com/support). 

11. Vyberte **povoleno SAML**a potom klikněte na **Uložit**.

      ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Chcete-li nakonfigurovat SAML jeden přihlašování nastavení, klikněte na tlačítko **nový**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. Na **SAML jeden přihlašování nastavení upravit** stránky, proveďte následující konfigurace:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Pro **název** pole, zadejte popisný název pro tuto konfiguraci. Poskytuje hodnotu pro **název** automaticky vyplnit **název rozhraní API** textové pole.

    b. V **vystavitele** pole, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    c. V **textové pole Entity Id**, zadejte název domény vaší služby Salesforce pomocí následujícího vzorce:
      
      * Účet organizace: `https://<subdomain>.my.salesforce.com`
      * Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Nahrát **certifikát zprostředkovatele Identity**, klikněte na tlačítko **zvolit soubor** a procházet a vyberte soubor certifikátu, který jste si stáhli z portálu Azure.

    e. Jako **typ Identity SAML**, vyberte jednu z následujících možností:
    
      * Vyberte **kontrolní výraz obsahuje uživatelské jméno Salesforce**, pokud uživatelské jméno uživatele Salesforce je předávána v kontrolního výrazu SAML

      * Vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**, pokud je předávána federace z objektu uživatele v kontrolního výrazu SAML

      * Vyberte **kontrolní výraz obsahuje ID použití z objektu uživatele**, pokud ID uživatele z objektu uživatele je předávána v kontrolního výrazu SAML

    f. Pro **umístění Identity SAML**, vyberte **identita je v elementu NameIdentifier příkaz subjektu**.

    g. Pro **zprostředkovatele iniciované žádosti vazby služby**, vyberte **přesměrování protokolu HTTP**.

    h. V **adresu URL pro přihlášení zprostředkovatele Identity** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure
    
    i. Nakonec klikněte na **Uložit** použít SAML jeden přihlašování nastavení.

14. V levém navigačním podokně v Salesforce, klikněte na tlačítko **nastavení společnosti** rozbalte související část, a potom klikněte na **Moje domény**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Přejděte dolů k položce **konfiguraci ověřování** části a klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. V **konfiguraci ověřování** část, zkontrolujte **přihlašovací stránku** jako **službu v ověřování** Konfigurace jednotného přihlašování SAML, a pak klikněte na tlačítko  **Uložit**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Pokud je vybrána více než jedna služba ověřování, jsou uživatelé vyzváni k vyberte služby ověřování, která se má při inicializaci jednotné přihlašování pro vaše prostředí Salesforce přihlásit. Pokud nechcete, aby mohla nastat, pak byste měli **nechte nezaškrtnuté všechny ostatní služby ověřování**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-salesforce-test-user"></a>Vytvoření zkušebního uživatele Salesforce

V této části se uživatel volá Britta Simon vytvoří v Salesforce. Salesforce podporuje za běhu zřizování, který je ve výchozím nastavení povolené.
Neexistuje žádná položka akce pro vás v této části. Pokud uživatel v Salesforce ještě neexistuje, vytvoří se nový při pokusu o přístup k Salesforce.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup do služby Salesforce.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Salesforce, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Salesforce**.

    ![V seznamu aplikací na odkaz Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici služby Salesforce na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Salesforce.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

