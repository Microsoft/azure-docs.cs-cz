---
title: 'Kurz: Integrace Azure Active Directory se službou Egnyte | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: d52672099c309dfeac641cb3aedaf32c7230b676
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047755"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Kurz: Integrace Azure Active Directory se službou Egnyte

V tomto kurzu se dozvíte, jak integrovat Egnyte s Azure Active Directory (Azure AD).

Egnyte integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Egnyte
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Egnyte (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Egnyte, potřebujete následující položky:

- S předplatným služby Azure AD
- Egnyte jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Egnyte z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-egnyte-from-the-gallery"></a>Přidání Egnyte z Galerie
Konfigurace integrace Egnyte do služby Azure AD, budete muset přidat Egnyte z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Egnyte z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Egnyte**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/egnyte-tutorial/tutorial_egnyte_search.png)

5. Na panelu výsledků vyberte **Egnyte**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Egnyte podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Egnyte je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Egnyte potřeba navázat.

V Egnyte, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Egnyte, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno Egnyte](#creating-an-egnyte-test-user)**  – Pokud chcete mít protějšek Britta Simon Egnyte, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Egnyte.

**Ke konfiguraci Azure AD jednotné přihlašování s Egnyte, proveďte následující kroky:**

1. Na webu Azure Portal na **Egnyte** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Na **Egnyte domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Egnyte klienta](https://www.egnyte.com/corp/contact_egnyte.html) tuto výhodu získáte. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_general_400.png)

6. Na **Egnyte konfigurace** klikněte na tlačítko **nakonfigurovat Egnyte** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_egnyte_configure.png) 

7. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Egnyte jako správce.

8. Klikněte na tlačítko **nastavení**.
   
   ![Nastavení](./media/egnyte-tutorial/ic787819.png "nastavení")

9. V nabídce klikněte na tlačítko **nastavení**.

   ![Nastavení](./media/egnyte-tutorial/ic787820.png "nastavení")

10. Klikněte na tlačítko **konfigurace** kartu a potom klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/egnyte-tutorial/ic787821.png "zabezpečení")

11. V **ověření jednotného přihlašování** části, proveďte následující kroky:

    ![Jednotné přihlašování na ověřování](./media/egnyte-tutorial/ic787822.png "jednotné přihlašování na ověřování")   
    
    a. Jako **ověření jednotného přihlašování**vyberte **SAML 2.0**.
   
    b. Jako **zprostředkovatele Identity**vyberte **AzureAD**.
   
    c. Vložit **SAML jednotné přihlašování – adresa URL služby** zkopírovanými z webu Azure portal do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.
   
    d. Vložit **SAML Entity ID** zkopírovanou z webu Azure portal do **ID entity zprostředkovatele Identity** textového pole.
      
    e. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.
   
    f. Jako **výchozí mapování uživatele**vyberte **e-mailová adresa**.
   
    g. Jako **použijte hodnotu issuer specifického pro doménu**vyberte **zakázané**.
   
    h. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/egnyte-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/egnyte-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/egnyte-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/egnyte-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-egnyte-test-user"></a>Vytvoření Egnyte testovacího uživatele

Přihlaste se k Egnyte Azure AD uživatelům umožnit, musí být poskytnuty do Egnyte. V případě Egnyte zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Egnyte** společnosti serveru jako správce.

2. Přejděte na **nastavení \> uživatelů a skupin**.

3. Klikněte na tlačítko **Add New User**a pak vyberte typ uživatele, které chcete přidat.
   
   ![Uživatelé](./media/egnyte-tutorial/ic787824.png "uživatelů")

4. V **nového standardního uživatele** části, proveďte následující kroky:
   
   ![Nového standardního uživatele](./media/egnyte-tutorial/ic787825.png "nového standardního uživatele")   

   a. Typ **e-mailu**, **uživatelské jméno**a další podrobnosti platný účet služby Azure Active Directory, které chcete zřídit.
   
   b. Klikněte na **Uložit**.
    
    >[!NOTE]
    >Držitel účtu Azure Active Directory obdrží e-mailové oznámení.
    >

>[!NOTE]
>Můžete použít jakékoli jiné Egnyte uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Egnyte uživatelským účtům, zřídit AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Egnyte použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Egnyte Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Egnyte**.

    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_egnyte_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Egnyte na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Egnyte.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/egnyte-tutorial/tutorial_general_01.png
[2]: ./media/egnyte-tutorial/tutorial_general_02.png
[3]: ./media/egnyte-tutorial/tutorial_general_03.png
[4]: ./media/egnyte-tutorial/tutorial_general_04.png

[100]: ./media/egnyte-tutorial/tutorial_general_100.png

[200]: ./media/egnyte-tutorial/tutorial_general_200.png
[201]: ./media/egnyte-tutorial/tutorial_general_201.png
[202]: ./media/egnyte-tutorial/tutorial_general_202.png
[203]: ./media/egnyte-tutorial/tutorial_general_203.png

