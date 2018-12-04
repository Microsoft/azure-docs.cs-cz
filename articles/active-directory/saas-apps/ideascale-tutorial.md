---
title: 'Kurz: Integrace Azure Active Directory se službou IdeaScale | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: ecb73e4b520936b573254f2cf209d4a02c0fdd32
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848744"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Kurz: Integrace Azure Active Directory se službou IdeaScale

V tomto kurzu se dozvíte, jak integrovat IdeaScale s Azure Active Directory (Azure AD).

IdeaScale integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k IdeaScale
- Můžete povolit uživatelům, aby automaticky získat přihlášení k IdeaScale (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IdeaScale, potřebujete následující položky:

- Předplatné Azure AD
- IdeaScale jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání IdeaScale z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ideascale-from-the-gallery"></a>Přidání IdeaScale z Galerie
Konfigurace integrace IdeaScale do služby Azure AD, budete muset přidat IdeaScale z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat IdeaScale z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **IdeaScale**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/ideascale-tutorial/tutorial_ideascale_search.png)

1. Na panelu výsledků vyberte **IdeaScale**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s IdeaScale podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v IdeaScale je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v IdeaScale potřeba navázat.

V IdeaScale, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s IdeaScale, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se testovací uživatelské jméno IdeaScale](#creating-an-ideascale-test-user)**  – Pokud chcete mít protějšek Britta Simon IdeaScale, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci IdeaScale.

**Ke konfiguraci Azure AD jednotné přihlašování s IdeaScale, proveďte následující kroky:**

1. Na webu Azure Portal na **IdeaScale** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/ideascale-tutorial/tutorial_ideascale_samlbase.png)

1. Na **IdeaScale domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/ideascale-tutorial/tutorial_ideascale_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.ideascale.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory IdeaScale klienta](https://support.ideascale.com/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/ideascale-tutorial/tutorial_ideascale_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/ideascale-tutorial/tutorial_general_400.png)

1. Na **IdeaScale konfigurace** klikněte na tlačítko **nakonfigurovat IdeaScale** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML Entity ID** z **Stručná referenční příručka části**.

    ![Konfigurace jednotného přihlašování](./media/ideascale-tutorial/tutorial_ideascale_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti IdeaScale jako správce.

1. Přejděte na **komunity nastavení**.
   
    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "nastavení komunity")

1. Přejděte na **zabezpečení \> jednotného přihlášení nastavení**.
   
    ![Jednotné přihlášení nastavení](./media/ideascale-tutorial/ic790848.png "jednotného přihlášení nastavení")

1. Jako **typ jednotného přihlašování**vyberte **SAML 2.0**.
   
    ![Jednotné přihlášení typu](./media/ideascale-tutorial/ic790849.png "jeden typ přihlášení")

1. Na **nastavení jednotného přihlášení** dialogového okna, proveďte následující kroky:
   
    ![Jednotné přihlášení nastavení](./media/ideascale-tutorial/ic790850.png "jednotného přihlášení nastavení")
   
    a. V **ID Entity SAML zprostředkovatele identity** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.

    b. Zkopírujte obsah souboru metadat stažené z webu Azure portal a vložte ho do **metadat SAML zprostředkovatele identity** textového pole.

    c. V **úspěch odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

    d. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/ideascale-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/ideascale-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/ideascale-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/ideascale-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-ideascale-test-user"></a>Vytvoření IdeaScale testovacího uživatele

Umožňuje uživatelům Azure AD k přihlášení do IdeaScale, musí být poskytnuty v k IdeaScale. V případě IdeaScale zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **IdeaScale** společnosti serveru jako správce.

1. Přejděte na **komunity nastavení**.
   
    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "nastavení komunity")

1. Přejděte na **základní nastavení \> člen správu**.

1. Klikněte na tlačítko **přidat člena**.
   
    ![Správa členů](./media/ideascale-tutorial/ic790852.png "člen správy")

1. V části přidat nového člena proveďte následující kroky:
   
    ![Přidání nového člena](./media/ideascale-tutorial/ic790853.png "přidat nového člena")
   
    a. V **e-mailové adresy** textového pole zadejte e-mailová adresa platný účet AAD, které chcete zřídit.
   
    b. Klikněte na tlačítko **uložit změny**. 
   
    >[!NOTE]
    >Držitel účtu Azure Active Directory obdrží e-mail s odkazem pro potvrzení účtu, pak se změní na aktivní.
      
>[!NOTE]
>Můžete použít jakékoli jiné IdeaScale uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných IdeaScale uživatelským účtům, zřídit AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k IdeaScale použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit IdeaScale Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **IdeaScale**.

    ![Konfigurace jednotného přihlašování](./media/ideascale-tutorial/tutorial_ideascale_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování


Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici IdeaScale na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci IdeaScale.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ideascale-tutorial/tutorial_general_01.png
[2]: ./media/ideascale-tutorial/tutorial_general_02.png
[3]: ./media/ideascale-tutorial/tutorial_general_03.png
[4]: ./media/ideascale-tutorial/tutorial_general_04.png

[100]: ./media/ideascale-tutorial/tutorial_general_100.png

[200]: ./media/ideascale-tutorial/tutorial_general_200.png
[201]: ./media/ideascale-tutorial/tutorial_general_201.png
[202]: ./media/ideascale-tutorial/tutorial_general_202.png
[203]: ./media/ideascale-tutorial/tutorial_general_203.png

