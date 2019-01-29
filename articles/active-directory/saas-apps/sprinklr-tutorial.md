---
title: 'Kurz: Integrace Azure Active Directory s Sprinklr | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 884cee735f6bf9f383b525890acf295040cc0c63
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194623"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Kurz: Integrace Azure Active Directory s Sprinklr

V tomto kurzu se dozvíte, jak integrovat Sprinklr s Azure Active Directory (Azure AD).

Sprinklr integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sprinklr
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Sprinklr (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sprinklr, potřebujete následující položky:

- Předplatné Azure AD
- Sprinklr jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sprinklr z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sprinklr-from-the-gallery"></a>Přidání Sprinklr z Galerie
Konfigurace integrace Sprinklr do služby Azure AD, budete muset přidat Sprinklr z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Sprinklr z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Sprinklr**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

1. Na panelu výsledků vyberte **Sprinklr**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Sprinklr podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Sprinklr je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sprinklr potřeba navázat.

V Sprinklr, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sprinklr, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Sprinklr](#creating-a-sprinklr-test-user)**  – Pokud chcete mít protějšek Britta Simon Sprinklr, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Sprinklr.

**Ke konfiguraci Azure AD jednotné přihlašování s Sprinklr, proveďte následující kroky:**

1. Na webu Azure Portal na **Sprinklr** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

1. Na **Sprinklr domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.sprinklr.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte hodnotu s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Sprinklr klienta](https://www.sprinklr.com/contact-us/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sprinklr-tutorial/tutorial_general_400.png)

1. Na **Sprinklr konfigurace** klikněte na tlačítko **nakonfigurovat Sprinklr** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Sprinklr jako správce.

1. Přejděte na **správu \> nastavení**.
   
    ![Správa](./media/sprinklr-tutorial/ic782907.png "správy")

1. Přejděte na **správa partnerů \> jednotného přihlašování** na v levém podokně.
   
    ![Správa partnera](./media/sprinklr-tutorial/ic782908.png "správa partnerů")

1. Klikněte na tlačítko **+ doplňky jednotného přihlašování**.
   
    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782909.png "jednotného přihlašování")

1. Na **jednotného přihlašování** stránce, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782910.png "jednotného přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: *WAADSSOTest*).

    b. Vyberte **povolené**.

    c. Vyberte **používat nový certifikát jednotného přihlašování**.
             
    e. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    f. Vložit **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure Portal do **Entity Id** textového pole.

    g. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure Portal do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.

    h. Vložit **odhlašování URL** hodnotu, která jste zkopírovali z portálu Azure Portal do **odhlašovací adresa URL zprostředkovatele Identity** textového pole.
     
    i. Jako **typ ID uživatele SAML**vyberte **kontrolní výraz obsahuje uživatelské jméno sprinklr.com**.

    j. Jako **umístění ID uživatele SAML**vyberte **ID uživatele není v elementu identifikátor názvu subjektu příkazu**.

    k. Klikněte na **Uložit**.
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sprinklr-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sprinklr-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sprinklr-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sprinklr-test-user"></a>Vytvoření zkušebního uživatele Sprinklr

1. Přihlaste se na web společnosti Sprinklr jako správce.

1. Přejděte na **správu \> nastavení**.
   
    ![Správa](./media/sprinklr-tutorial/ic782907.png "správy")

1. Přejděte na **spravovat klienta \> uživatelé** v levém podokně.
   
    ![Nastavení](./media/sprinklr-tutorial/ic782914.png "nastavení")

1. Klikněte na tlačítko **přidat uživatele**.
   
    ![Nastavení](./media/sprinklr-tutorial/ic782915.png "nastavení")

1. Na **upravit uživatele** dialogového okna, proveďte následující kroky:
   
    ![Upravit uživatele](./media/sprinklr-tutorial/ic782916.png "úpravy uživatele") 

    a. V **e-mailu**, **křestní jméno** a **příjmení** textová pole, zadejte informace o uživatelský účet služby Azure AD, které chcete zřídit.

    b. Vyberte **heslo zakázáno**.

    c. Vyberte **jazyka**.

    d. Vyberte **typ uživatele**.

    e. Klikněte na tlačítko **aktualizace**.
   
     >[!IMPORTANT]
     >**Heslo zakázáno** musí být vybrána a povolit tak uživateli při přihlašování přes zprostředkovatele Identity. 
     
1. Přejděte na **Role**a pak proveďte následující kroky:
   
    ![Partner role](./media/sprinklr-tutorial/ic782917.png "Partner role")

    a. Z **globální** seznamu vyberte **všechny\_oprávnění**.  

    b. Klikněte na tlačítko **aktualizace**.

>[!NOTE]
>Můžete použít jakékoli jiné Sprinklr uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Sprinklr zřízení uživatelských účtů služby Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Sprinklr použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Sprinklr Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Sprinklr**.

    ![Konfigurace jednotného přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Sprinklr na přístupovém panelu, naleznete v části Další informace o přístupovém panelu, měli byste získat automaticky přihlášení k aplikaci Sprinklr [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

