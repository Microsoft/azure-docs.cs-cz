---
title: 'Kurz: Integrace Azure Active Directory s Lynda.com | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lynda.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e0f14ed72ae1609d4d6605e4a53e88b8fe33f28
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217679"
---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Kurz: Integrace Azure Active Directory s Lynda.com

V tomto kurzu se dozvíte, jak integrovat Lynda.com s Azure Active Directory (Azure AD).

Lynda.com integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Lynda.com
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Lynda.com (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Lynda.com, potřebujete následující položky:

- Předplatné Azure AD
- Lynda.com jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Lynda.com z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lyndacom-from-the-gallery"></a>Přidání Lynda.com z Galerie
Konfigurace integrace Lynda.com do služby Azure AD, budete muset přidat Lynda.com z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Lynda.com z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Lynda.com**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lynda-tutorial/tutorial_lynda.com_search.png)

1. Na panelu výsledků vyberte **Lynda.com**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lynda-tutorial/tutorial_lynda.com_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Lynda.com podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Lynda.com je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Lynda.com potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Lynda.com.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Lynda.com, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Lynda.com](#creating-a-lyndacom-test-user)**  – Pokud chcete mít protějšek Britta Simon Lynda.com, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Lynda.com.

**Ke konfiguraci Azure AD jednotné přihlašování s Lynda.com, proveďte následující kroky:**

1. Na webu Azure Portal na **Lynda.com** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/lynda-tutorial/tutorial_lynda.com_samlbase.png)

1. Na **Lynda.com domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/lynda-tutorial/tutorial_lynda.com_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.lynda.com/Shibboleth.sso/InCommon?providerId=<url>&target=<url> `

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Lynda.com klienta](https://www.linkedin.com/help/lynda/ask) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/lynda-tutorial/tutorial_lynda.com_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/lynda-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Lynda.com** straně, je nutné odeslat na stažený **soubor XML s metadaty** [Lynda.com podporu](https://www.linkedin.com/help/lynda/ask).

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lynda-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/lynda-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lynda-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lynda-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lyndacom-test-user"></a>Vytvoření zkušebního uživatele Lynda.com

Neexistuje žádná položka akce konfigurace zřizování uživatelů pro Lynda.com.  
Přiřazený uživatel se pokusí přihlásit k Lynda.com pomocí přístupového panelu, Lynda.com kontroluje, zda uživatel existuje.  

Pokud není žádný uživatelský účet k dispozici dosud, vytvoří se automaticky podle Lynda.com.

>[!NOTE]
>Můžete použít jakékoli jiné Lynda.com uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Lynda.com uživatelským účtům, zřídit AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Lynda.com použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Lynda.com Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Lynda.com**.

    ![Konfigurace jednotného přihlašování](./media/lynda-tutorial/tutorial_lynda.com_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/lynda-tutorial/tutorial_general_01.png
[2]: ./media/lynda-tutorial/tutorial_general_02.png
[3]: ./media/lynda-tutorial/tutorial_general_03.png
[4]: ./media/lynda-tutorial/tutorial_general_04.png

[100]: ./media/lynda-tutorial/tutorial_general_100.png

[200]: ./media/lynda-tutorial/tutorial_general_200.png
[201]: ./media/lynda-tutorial/tutorial_general_201.png
[202]: ./media/lynda-tutorial/tutorial_general_202.png
[203]: ./media/lynda-tutorial/tutorial_general_203.png

