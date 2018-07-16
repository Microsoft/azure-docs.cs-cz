---
title: 'Kurz: Integrace Azure Active Directory s použitím PagerDuty | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 2ac5dee8fe9a27ffeed717e010cade522b9fefc0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046488"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Kurz: Integrace Azure Active Directory s použitím PagerDuty

V tomto kurzu se dozvíte, jak integrovat PagerDuty s Azure Active Directory (Azure AD).

PagerDuty integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PagerDuty
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PagerDuty (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s použitím PagerDuty, potřebujete následující položky:

- S předplatným služby Azure AD
- PagerDuty jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PagerDuty z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pagerduty-from-the-gallery"></a>Přidání PagerDuty z Galerie
Pokud chcete nakonfigurovat integraci PagerDuty do služby Azure AD, budete muset přidat PagerDuty z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat PagerDuty z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **PagerDuty**vyberte **PagerDuty** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím PagerDuty podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v PagerDuty je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v PagerDuty.

V PagerDuty, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím PagerDuty, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele PagerDuty](#create-a-pagerduty-test-user)**  – Pokud chcete mít protějšek Britta Simon v PagerDuty, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci PagerDuty.

**Ke konfiguraci Azure AD jednotné přihlašování s použitím PagerDuty, proveďte následující kroky:**

1. Na webu Azure Portal na **PagerDuty** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. Na **PagerDuty domény a adresy URL** části, proveďte následující kroky:

    ![PagerDuty domény a adresy URL jednotného přihlašování – informace](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.pagerduty.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory PagerDuty klienta](https://www.pagerduty.com/support/) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pagerduty-tutorial/tutorial_general_400.png)

6. Na **PagerDuty konfigurace** klikněte na tlačítko **nakonfigurovat PagerDuty** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace PagerDuty](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

7. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Pagerduty.

8. V nabídce v horní části klikněte na tlačítko **nastavení účtu**.

    ![Nastavení účtu](./media/pagerduty-tutorial/ic778535.png "nastavení účtu")

9. Klikněte na tlačítko **jednotného přihlašování**.

    ![Jednotné přihlašování](./media/pagerduty-tutorial/ic778536.png "jednotného přihlašování")

10. Na **povolit jednotné přihlašování (SSO)** stránce, proveďte následující kroky:

    ![Povolit jednotné přihlašování](./media/pagerduty-tutorial/ic778537.png "povolit jednotné přihlašování")

    a. Otevření certifikátu kódováním base-64 stáhnout z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textové pole
  
    b. V **přihlašovací adresa URL** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
  
    c. V **odhlašovací adresa URL** vložit do textového pole **odhlašování URL** zkopírovanou z webu Azure portal.

    d. Vyberte **povolit uživatelskému jménu nebo heslu**.

    e. Vyberte **vyžadují přesné porovnání kontext ověřování** zaškrtávací políčko.

    f. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/pagerduty-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/pagerduty-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Tlačítko Přidat](./media/pagerduty-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-pagerduty-test-user"></a>Vytvoření zkušebního uživatele PagerDuty

Povolit uživatele Azure AD se přihlaste k PagerDuty, musí být poskytnuty do PagerDuty.  
V případě PagerDuty zřizování se ruční úlohy.

>[!NOTE]
>Můžete použít jakékoli jiné Pagerduty uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Pagerduty ke zřízení služby Azure Active Directory uživatelské účty.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Pagerduty** tenanta.

2. V nabídce v horní části klikněte na tlačítko **uživatelé**.

3. Klikněte na tlačítko **přidat uživatele**.
   
    ![Přidání uživatelů](./media/pagerduty-tutorial/ic778539.png "přidat uživatele")

4.  Na **pozvat váš tým** dialogového okna, proveďte následující kroky:
   
    ![Pozvat váš tým](./media/pagerduty-tutorial/ic778540.png "pozvat váš tým")

    a. Typ **jméno a příjmení** uživatele, jako je **Britta Simon**. 
   
    b. Zadejte **e-mailu** adresa uživatele, jako jsou **brittasimon@contoso.com**.
   
    c. Klikněte na tlačítko **přidat**a potom klikněte na tlačítko **odeslat vyzývá**.
   
    >[!NOTE]
    >Všechny přidaní uživatelé obdrží pozvánku k vytvoření účtu PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k PagerDuty.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit PagerDuty, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **PagerDuty**.

    ![V seznamu aplikací na odkaz PagerDuty](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici PagerDuty v Panelyou přístup by měl získat automaticky přihlášení k aplikaci PagerDuty.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
