---
title: 'Kurz: Integrace Azure Active Directory s 8 x 8 virtuálních Office | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 8 x 8 virtuální Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 3a33f9ba0ca744709e21e9e55acc22b657c2adc2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048415"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Kurz: Integrace Azure Active Directory s 8 x 8 virtuálních Office

V tomto kurzu se dozvíte, jak integrovat 8 x 8 virtuálních Office se službou Azure Active Directory (Azure AD).

Integrace 8 x 8 virtuální Office pomocí služby Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Office virtuální 8 x 8
- Uživatele, aby automaticky získat přihlášení k Office virtuální 8 x 8 (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 8 x 8 virtuálních Office, potřebujete následující položky:

- S předplatným služby Azure AD
- 8 x 8 virtuální Office jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání virtuální Office 8 x 8 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Přidání virtuální Office 8 x 8 z Galerie
Pokud chcete nakonfigurovat integraci Office virtuální 8 x 8 do služby Azure AD, budete muset přidat Office virtuální 8 x 8 z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Office virtuální 8 x 8 z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **8 x 8 virtuálních Office**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_search.png)

5. Na panelu výsledků vyberte **8 x 8 virtuálních Office**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s 8 x 8 virtuální Office založená na uživateli test "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v 8 x 8 virtuální Office je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v 8 x 8 virtuální Office je potřeba navázat.

V systému Office virtuální 8 x 8 přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 8 x 8 virtuálních Office, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření testovacího uživatele virtuální Office 8 x 8](#creating-a-8x8-virtual-office-test-user)**  – Pokud chcete mít protějšek Britta Simon v Office virtuální 8 x 8, která souvisí s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Office virtuální 8 x 8.

**Ke konfiguraci Azure AD jednotné přihlašování s 8 x 8 virtuálních Office, proveďte následující kroky:**

1. Na webu Azure Portal na **8 x 8 virtuálních Office** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_samlbase.png)

3. Na **8 x 8 virtuálních Office domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | `https://sso.8x8.com/<companyname>` |
    | `https://www.8x8.com/<companyname>` |
    | `https://sso.8x8pilot.com/<companyname>` |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | `https://<subdomain>.8x8.com/saml2` |
    | `https://<subdomain>.8x8pilot.com/saml2`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory virtuální Office 8 x 8](https://www.8x8.com/about-us/contact-us) k získání těchto hodnot.
 


4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_general_400.png)

6. Na **8 x 8 virtuálních Office konfigurace** klikněte na tlačítko **virtuální Office konfigurovat 8 x 8** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_configure.png) 

7. Přihlášení k tenantovi virtuální Office 8 x 8 jako správce.

8. Vyberte **virtuální Office účet správce** na panelu aplikace.
   
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Vyberte **obchodní** účtu pro správu a klikněte na tlačítko **Sign In** tlačítko.
   
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Klikněte na tlačítko **účty** karty v seznamu v nabídce.
   
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Klikněte na tlačítko **Single Sign On** v seznamu účtů.
   
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Vyberte **Single Sign On** podle metody ověřování a klikněte na tlačítko **SAML**.
    
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Kopírování **adresu URL jednotného přihlašování SAML**, **využitím si adresu URL služby jednotného** a **URL vystavitele** z Azure AD, aby **přihlásit v adrese URL**, **odhlašovací adresa URL**  a **URL vystavitele** v Office virtuální 8 x 8. 
    
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
14. Klikněte na tlačítko **prohlížeče** tlačítko Nahrát certifikát, který jste stáhli ze služby Azure AD a klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-8x8-virtual-office-test-user"></a>Vytváření 8 x 8 virtuální Office testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Office virtuální 8 x 8. 8 x 8 virtuální podporuje Office just-in-time zřizování, který je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Office virtuální 8 x 8, pokud ještě neexistuje. 

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory virtuální Office 8 x 8](https://www.8x8.com/about-us/contact-us). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Office virtuální 8 x 8.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit 8 x 8 virtuálních Office, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **8 x 8 virtuálních Office**.

    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Office virtuální 8 x 8 na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Office virtuální 8 x 8.
Další informace o přístupovém panelu, naleznete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/8x8virtualoffice-tutorial/tutorial_general_04.png

[100]: ./media/8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/8x8virtualoffice-tutorial/tutorial_general_201.png
[202]: ./media/8x8virtualoffice-tutorial/tutorial_general_202.png
[203]: ./media/8x8virtualoffice-tutorial/tutorial_general_203.png

