---
title: 'Kurz: Integrace Azure Active Directory s plošší souborů | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a plošší soubory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 744d18b39ffc696d0973628c60687c6b70fbcaad
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168664"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Kurz: Integrace Azure Active Directory s plošší soubory

V tomto kurzu se dozvíte, jak integrovat plošší soubory pomocí Azure Active Directory (Azure AD).

Integrace plošší soubory s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k souborům plošší
- Uživatele, aby automaticky získat přihlášení k plošší soubory (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s plošší soubory, potřebujete následující položky:

- Předplatné Azure AD
- Plošší soubory jednotné přihlášení povolený předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání plošší soubory z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-flatter-files-from-the-gallery"></a>Přidání plošší soubory z Galerie
Konfigurace integrace plošší soubory do služby Azure AD, budete muset přidat plošší soubory z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat plošší soubory z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **plošší soubory**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/flatter-files-tutorial/tutorial_flatterfiles_search.png)

1. Na panelu výsledků vyberte **plošší soubory**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí plošší soubory na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v plošší soubory je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v plošší soubory je potřeba navázat.

V souborech plošší, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se plošší soubory, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele plošší soubory](#creating-a-flatter-files-test-user)**  – Pokud chcete mít protějšek Britta Simon plošší souborů, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci plošší soubory.

**Ke konfiguraci Azure AD jednotného přihlašování se plošší soubory, proveďte následující kroky:**

1. Na webu Azure Portal na **plošší soubory** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

1. Na **plošší soubory domény a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_general_400.png)

1. Na **plošší konfigurační soubory** klikněte na tlačítko **konfigurace plošší soubory** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

1. Přihlášení do aplikace plošší soubory jako správce.

1. Klikněte na tlačítko **řídicí panel**. 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

1. Klikněte na tlačítko **nastavení**a pak proveďte následující kroky na **společnosti** kartu: 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Vyberte **používat protokol SAML 2.0 pro ověřování**.
    
    b. Klikněte na tlačítko **konfigurace SAML**.

1. Na **konfigurace SAML** dialogového okna, proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. V **domény** textového pole zadejte registrované domény.
   
    >[!NOTE]
    >Pokud nemáte k dispozici registrované domény ještě, kontaktujte tým prostřednictvím podpory plošší soubory [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** který jste zkopírovali formuláře webu Azure portal.
   
    c.  V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    d. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/flatter-files-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/flatter-files-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/flatter-files-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/flatter-files-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-flatter-files-test-user"></a>Vytvoření zkušebního uživatele plošší soubory

Cílem této části je vytvořte uživatele Britta Simon v plošší soubory.

**Vytvořte uživatele v plošší soubory jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k vaší **plošší soubory** společnosti serveru jako správce.

1. V navigačním podokně na levé straně klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **uživatelé** kartu.
   
    ![Vytvoření plošší soubory uživatele](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

1. Klikněte na tlačítko **přidat uživatele**. 

1. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
   
    ![Vytvoření plošší soubory uživatele](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. V **křestní jméno** textové pole, typ **Britta**.
   
    b. V **příjmení** textové pole, typ **Simon**. 
   
    c. V **e-mailovou adresu** textového pole zadejte Haniny e-mailovou adresu na webu Azure Portal.
   
    d. Klikněte na **Submit** (Odeslat).   


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k souborům plošší.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit plošší soubory, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **plošší soubory**.

    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatterfiles_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici plošší soubory na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci plošší soubory.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/flatter-files-tutorial/tutorial_general_203.png

