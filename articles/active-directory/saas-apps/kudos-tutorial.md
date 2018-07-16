---
title: 'Kurz: Integrace Azure Active Directory se službou pochvalné hodnocení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pochvalné hodnocení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 5807ab0090f401c02599db40f110b7a9b1504060
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047048"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Kurz: Integrace Azure Active Directory se službou pochvalné hodnocení

V tomto kurzu se dozvíte, jak integrovat pochvalné hodnocení s Azure Active Directory (Azure AD).

Integrace pochvalné hodnocení s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k pochvalné hodnocení
- Můžete povolit uživatelům, aby automaticky získat přihlášení k pochvalné hodnocení (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s pochvalné hodnocení, potřebujete následující položky:

- S předplatným služby Azure AD
- Pochvalné hodnocení jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání pochvalné hodnocení z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kudos-from-the-gallery"></a>Přidání pochvalné hodnocení z Galerie
Konfigurace integrace pochvalné hodnocení do služby Azure AD, budete muset přidat pochvalné hodnocení z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat pochvalné hodnocení z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **pochvalné hodnocení**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kudos-tutorial/tutorial_kudos_search.png)

5. Na panelu výsledků vyberte **pochvalné hodnocení**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí pochvalné hodnocení na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v pochvalné hodnocení je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v pochvalné hodnocení.

V pochvalné hodnocení, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s pochvalné hodnocení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele pochvalné hodnocení](#creating-a-kudos-test-user)**  – Pokud chcete mít protějšek Britta Simon pochvalné hodnocení, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci pochvalné hodnocení.

**Ke konfiguraci Azure AD jednotné přihlašování s pochvalné hodnocení, proveďte následující kroky:**

1. Na webu Azure Portal na **pochvalné hodnocení** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

3. Na **pochvalné hodnocení domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kudos-tutorial/tutorial_kudos_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company>.kudosnow.com`
    
    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory pochvalné hodnocení klientů](http://success.kudosnow.com/home) tuto výhodu získáte. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/kudos-tutorial/tutorial_general_400.png)

6. Na **pochvalné hodnocení konfigurace** klikněte na tlačítko **pochvalné hodnocení konfigurace** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/kudos-tutorial/tutorial_kudos_configure.png) 

7. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti pochvalné hodnocení.

8. V nabídce v horní části klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/kudos-tutorial/ic787806.png "nastavení")

9. Klikněte na tlačítko **integrace \> jednotného přihlašování**.

10. V **jednotného přihlašování** části, proveďte následující kroky:
   
    ![JEDNOTNÉ PŘIHLAŠOVÁNÍ](./media/kudos-tutorial/ic787807.png "JEDNOTNÉHO PŘIHLAŠOVÁNÍ")
   
    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal. 

    b. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textové pole
   
    c. V **odhlášení na adresu URL**, vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
   
    d. V **vaše adresa URL pochvalné hodnocení** textového pole zadejte název vaší společnosti.
   
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kudos-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/kudos-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kudos-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kudos-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kudos-test-user"></a>Vytvoření zkušebního uživatele pochvalné hodnocení

Chcete-li povolit uživatele Azure AD k přihlášení do pochvalné hodnocení, musí být poskytnuty do pochvalné hodnocení. 

V případě pochvalné hodnocení zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **pochvalné hodnocení** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **nastavení**.
   
   ![Nastavení](./media/kudos-tutorial/ic787806.png "nastavení")

3. Klikněte na tlačítko **Správce uživatelů**.

4. Klikněte na tlačítko **uživatelé** kartu a potom klikněte na tlačítko **přidání uživatele**.
   
   ![Správce uživatelů](./media/kudos-tutorial/ic787809.png "Správce uživatelů")

5. V **přidání uživatele** části, proveďte následující kroky:
   
    ![Přidání uživatele](./media/kudos-tutorial/ic787810.png "přidat uživatele")
   
    a. Typ **křestní jméno**, **příjmení**, **e-mailu** a další podrobnosti ke zřízení do související textových polí platný účet služby Azure Active Directory.
   
    b. Klikněte na tlačítko **vytvořit uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné pochvalné hodnocení uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných pochvalné hodnocení uživatelským účtům, zřídit AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k pochvalné hodnocení.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit pochvalné hodnocení, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **pochvalné hodnocení**.

    ![Konfigurace jednotného přihlašování](./media/kudos-tutorial/tutorial_kudos_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici pochvalné hodnocení na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci pochvalné hodnocení. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png

