---
title: 'Kurz: Integrace Azure Active Directory s rozpoznávat | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a rozpoznání.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: e47b8d6490ddc9620574c6834378471c8c02bac7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190050"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Kurz: Integrace Azure Active Directory s rozpoznávat

V tomto kurzu se dozvíte, jak můžete rozpoznávat integrovat s Azure Active Directory (Azure AD).

Rozpoznávat integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k rozpoznání
- Můžete povolit uživatelům, aby automaticky získat přihlášení k rozpoznání (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s rozpoznávat, potřebujete následující položky:

- Předplatné Azure AD
- Rozpoznávat jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání rozpoznávat z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-recognize-from-the-gallery"></a>Přidání rozpoznávat z Galerie
Konfigurace integrace rozpoznávat do služby Azure AD, budete muset přidat rozpoznávat v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li rozpoznávat přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **rozpoznávat**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/recognize-tutorial/tutorial_recognize_search.png)

1. Na panelu výsledků vyberte **rozpoznávat**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí rozpoznávat podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v rozpoznávat je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v rozpoznávat potřeba navázat.

V rozpoznávat, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se rozpoznávat, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele rozpoznávat](#creating-a-recognize-test-user)**  – Pokud chcete mít protějšek Britta Simon rozpoznávat, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci rozpoznávat.

**Ke konfiguraci Azure AD jednotného přihlašování se rozpoznávat, proveďte následující kroky:**

1. Na webu Azure Portal na **rozpoznávat** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

1. Na **rozpoznat domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory rozpoznat klienta](mailto:support@recognizeapp.com) přihlašovací adresu URL a můžete získat hodnotu identifikátoru tak, že otevřete adresu URL služby zprostředkovatele metadat z části Nastavení jednotného přihlašování, která je vysvětlená v pozdější části kurzu. . 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/recognize-tutorial/tutorial_general_400.png)

1. Na **rozpoznat konfigurace** klikněte na tlačítko **konfigurace rozpoznat** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/recognize-tutorial/tutorial_recognize_configure.png) 

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi rozpoznávat jako správce.

1. V pravém horním rohu klikněte na **nabídky**. Přejděte na **společnosti správce**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_000.png)

1. V levém navigačním podokně klikněte na tlačítko **nastavení**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_001.png)

1. Následující postup proveďte **nastavení jednotného přihlašování** oddílu.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **povolit jednotné přihlašování**vyberte **ON**.

    b. V **IDP Entity ID** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.
    
    c. V **jednotného přihlašování k cílové adrese url** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
    
    d. V **cílová adresa url cíle na úrovni služby** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal. 
    
    e. Otevřete váš stažené **certifikát (Base64)** v poznámkovém bloku soubor, zkopírujte obsah ho do schránky a vložte ho do **certifikát** textového pole.
    
    f. Klikněte na tlačítko **uložit nastavení** tlačítko. 

1. Vedle položky **nastavení jednotného přihlašování** tématu, zkopírujte adresu URL v části **adresa url služby zprostředkovatele metadat**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_003.png)

1. Otevřít **odkazem na adresu URL metadat** pod prázdnou prohlížeče a stáhněte dokumentu metadat. Potom zkopírujte EntityDescriptor value(entityID) ze souboru a vložte ji **identifikátor** textového pole v **rozpoznat domény a adresy URL části** na portálu Azure portal.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/recognize-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/recognize-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/recognize-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/recognize-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-recognize-test-user"></a>Vytvoření zkušebního uživatele rozpoznávat

Chcete-li povolit uživatele Azure AD k přihlášení do rozpoznávat, musí být poskytnuty do rozpoznávat. V případě rozpoznání zřizování se ruční úlohy.

Tato aplikace nepodporuje zřizování SCIM ale má alternativní synchronizace, které zřizuje uživatele. 

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlášení na webu společnosti rozpoznávat jako správce.

1. V pravém horním rohu klikněte na **nabídky**. Přejděte na **společnosti správce**.

1. V levém navigačním podokně klikněte na tlačítko **nastavení**.

1. Následující postup proveďte **synchronizace uživatelů** oddílu.
   
   ![Nový uživatel](./media/recognize-tutorial/tutorial_recognize_005.png "nového uživatele")
   
   a. Jako **s povolenou synchronizací**vyberte **ON**.
   
   b. Jako **zprostředkovatele synchronizace zvolit**vyberte **Microsoft / Office 365**.
   
   c. Klikněte na tlačítko **spuštění synchronizace uživatelů**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k rozpoznání.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit rozpoznávat, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **rozpoznávat**.

    ![Konfigurace jednotného přihlašování](./media/recognize-tutorial/tutorial_recognize_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici rozpoznávat na přístupovém panelu, vám by měl získat automaticky přihlášení k rozpoznání aplikace. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

