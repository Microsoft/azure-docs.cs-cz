---
title: 'Kurz: Integrace Azure Active Directory se totiž | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konkrétně.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f78e978e80e27babcf8d2a2736eec06f35aa5e05
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177831"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Kurz: Integrace Azure Active Directory se totiž

V tomto kurzu se dozvíte, jak můžete konkrétně integrovat s Azure Active Directory (Azure AD).

Konkrétně integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, která má přístup k totiž
- Můžete povolit uživatelům, automaticky získá přihlášení k totiž (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci Azure AD integrace se totiž, budete potřebovat tyto položky:

- Předplatné Azure AD
- Konkrétně jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání a to z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-namely-from-the-gallery"></a>Přidání a to z Galerie
Pokud chcete nakonfigurovat integraci konkrétně do služby Azure AD, musíte konkrétně přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat totiž z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **totiž**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/namely-tutorial/tutorial_namely_search.png)

1. Na panelu výsledků vyberte **totiž**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování se totiž podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v totiž je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v konkrétně je potřeba navázat.

V konkrétně, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se totiž, budete muset provést následující stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření konkrétně testovací uživatele](#creating-a-namely-test-user)**  – Pokud chcete mít protějšek Britta Simon v totiž připojený k Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v konkrétně aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování se totiž, postupujte následovně:**

1. Na webu Azure Portal na **totiž** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_samlbase.png)

1. Na **totiž domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.namely.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory totiž klienta](https://www.namely.com/contact/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_general_400.png)

1. Na **totiž konfigurace** klikněte na tlačítko **konfigurace totiž** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_configure.png) 

1. V jiném okně prohlížeče, přihlaste se k vaší totiž společnosti serveru jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **společnosti**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_06.png) 

1. Klikněte na kartu **Nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_07.png) 

1. Klikněte na tlačítko **SAML**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_08.png) 

1. Na **nastavení SAML** stránce, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Klikněte na tlačítko **povolit SAML**. 

    b. V **adresa url jednotného přihlašování pro zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
    
    c. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát poskytovatele Identity** textového pole.
     
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/namely-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/namely-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/namely-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/namely-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-namely-test-user"></a>Vytváření konkrétně testovací uživatele

Cílem této části je vytvořte uživatele Britta Simon totiž.

**Pokud chcete vytvořte uživatele Britta Simon konkrétně, proveďte následující kroky:**

1. Přihlášení k vaší totiž společnosti serveru jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **lidé**.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_10.png) 

1. Klikněte na tlačítko **Directory** kartu.
   
    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_11.png) 

1. Klikněte na tlačítko **přidat nové osobě**.

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_12.png)

1. Na **přidat nové osobě** dialogového okna, proveďte následující kroky:

    a. V **křestní jméno** textové pole, typ **Britta**.

    b. V **příjmení** textové pole, typ **Simon**.

    c. V **e-mailu** textové pole, typ **e-mailová adresa** z BrittaSimon.

    d. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon totiž udělení přístupu k použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřazení Britta Simon konkrétně, provést následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **totiž**.

    ![Konfigurace jednotného přihlašování](./media/namely-tutorial/tutorial_namely_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Když kliknete konkrétně dlaždici na přístupovém panelu, které by měl získat automaticky přihlášení k konkrétně aplikace

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/namely-tutorial/tutorial_general_01.png
[2]: ./media/namely-tutorial/tutorial_general_02.png
[3]: ./media/namely-tutorial/tutorial_general_03.png
[4]: ./media/namely-tutorial/tutorial_general_04.png

[100]: ./media/namely-tutorial/tutorial_general_100.png

[200]: ./media/namely-tutorial/tutorial_general_200.png
[201]: ./media/namely-tutorial/tutorial_general_201.png
[202]: ./media/namely-tutorial/tutorial_general_202.png
[203]: ./media/namely-tutorial/tutorial_general_203.png

