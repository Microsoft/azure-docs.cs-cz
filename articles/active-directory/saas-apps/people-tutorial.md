---
title: 'Kurz: Integrace Azure Active Directory s lidmi | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a lidi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cb01dde4ddfe26351c6e1147fcf3d1cbbd49ad5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193046"
---
# <a name="tutorial-azure-active-directory-integration-with-people"></a>Kurz: Integrace Azure Active Directory s uživateli

V tomto kurzu se dozvíte, jak integrovat lidé s Azure Active Directory (Azure AD).

Integrace lidé s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k uživatelům
- Můžete povolit uživatelům, aby automaticky získat přihlášeného lidem (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s lidmi, potřebujete následující položky:

- Předplatné Azure AD
- Lidé jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidávání uživatelů z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-people-from-the-gallery"></a>Přidávání uživatelů z Galerie
Pokud chcete nakonfigurovat integraci lidí do služby Azure AD, budete muset přidat lidi z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat lidi z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **lidé**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/people-tutorial/tutorial_people_search.png)

1. Na panelu výsledků vyberte **lidé**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/people-tutorial/tutorial_people_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s uživateli na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek uživatelé je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské uživatelé musí vytvořit.

Uživatelé, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s lidmi, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele lidé](#creating-a-people-test-user)**  – Pokud chcete mít protějšek Britta Simon osoby, které souvisí s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci uživatelům.

**Ke konfiguraci Azure AD jednotné přihlašování s lidmi, proveďte následující kroky:**

1. Na webu Azure Portal na **lidé** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_samlbase.png)

1. Na **uživatelé domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:  `https://<company name>.peoplehr.net`

    b. V **identifikátor** textového pole zadejte adresu URL: `https://www.peoplehr.com`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:  `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory lidé klienta](mailto:customerservices@peoplehr.com) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_general_400.png)
    
1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, budete muset přihlašování k vašemu tenantovi lidé jako správce.
   
1. V nabídce na levé straně klikněte na tlačítko **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_001.png)

1. Klikněte na tlačítko **společnosti**.

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_002.png)

1. Na **soubor metadat nahrávání "Single Sign On" SAML**, klikněte na tlačítko **Procházet** nahrát soubor stažený metadat.

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_003.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/people-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/people-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/people-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/people-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-people-test-user"></a>Vytvoření zkušebního uživatele lidí

V této části vytvořte uživatele Britta Simon uživatelé. Práce s [tým podpory lidé klienta](mailto:customerservices@peoplehr.com) přidat uživatele na platformě lidí. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup osobám.

![Přiřadit uživatele][200] 

**Chcete lidem přiřadit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **lidé**.

    ![Konfigurace jednotného přihlašování](./media/people-tutorial/tutorial_people_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici uživatelům na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci uživatelům.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/people-tutorial/tutorial_general_01.png
[2]: ./media/people-tutorial/tutorial_general_02.png
[3]: ./media/people-tutorial/tutorial_general_03.png
[4]: ./media/people-tutorial/tutorial_general_04.png

[100]: ./media/people-tutorial/tutorial_general_100.png

[200]: ./media/people-tutorial/tutorial_general_200.png
[201]: ./media/people-tutorial/tutorial_general_201.png
[202]: ./media/people-tutorial/tutorial_general_202.png
[203]: ./media/people-tutorial/tutorial_general_203.png

