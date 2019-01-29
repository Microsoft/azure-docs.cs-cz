---
title: 'Kurz: Integrace Azure Active Directory s Hackerone | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Hackerone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: fdc9b67ef7f2becbeffe10261b3f2a96a225b5db
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159086"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Kurz: Integrace Azure Active Directory s HackerOne

V tomto kurzu se dozvíte, jak integrovat HackerOne s Azure Active Directory (Azure AD).

HackerOne integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k HackerOne
- Můžete povolit uživatelům, aby automaticky získat přihlášení k HackerOne (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HackerOne, potřebujete následující položky:

- Předplatné Azure AD
- HackerOne jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání HackerOne z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-hackerone-from-the-gallery"></a>Přidání HackerOne z Galerie
Konfigurace integrace HackerOne do služby Azure AD, budete muset přidat HackerOne z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat HackerOne z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **HackerOne**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hackerone-tutorial/tutorial_hackerone_search.png)

1. Na panelu výsledků vyberte **HackerOne**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s HackerOne podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v HackerOne je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v HackerOne potřeba navázat.

V HackerOne, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s HackerOne, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele HackerOne](#creating-a-hackerone-test-user)**  – Pokud chcete mít protějšek Britta Simon HackerOne, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci HackerOne.

**Ke konfiguraci Azure AD jednotné přihlašování s HackerOne, proveďte následující kroky:**

1. Na webu Azure Portal na **HackerOne** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_samlbase.png)

1. Na **HackerOne jedním přihlašovací adresu URL a identifikátoru** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://hackerone.com/<company name>/authentication`

    b. V **identifikátor** textového pole zadejte adresu URL jako:  `https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory HackerOne](mailto:support@hackerone.com) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_general_400.png)

1. Na **HackerOne konfigurace** klikněte na tlačítko **nakonfigurovat HackerOne** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_configure.png) 

1. Přihlášení k tenantovi HackerOne jako správce.

1. V nabídce v horní části klikněte "**nastavení**."
   
    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_001.png) 

1. Přejděte na "**ověřování**"a klikněte na tlačítko"**přidat nastavení SAML**."
   
    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_003.png) 

1. Na **nastavení SAML** dialogového okna, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_004.png) 

    a. V **e-mailové doméně** textového pole zadejte registrované domény.

    b. V  **adresy jednotného přihlašování** textových polí, vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. Otevřete váš **soubor certifikátu** v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **X509 certifikátu**  textového pole.
    
    d. Klikněte na **Uložit**.

1. V dialogovém okně Nastavení ověřování proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Klikněte na tlačítko **spuštění testu**.

    b. Pokud hodnotu **stav** pole se rovná **poslední stav testu: vytvořili**, požádejte vašeho [tým podpory HackerOne](mailto:support@hackerone.com) požádat o kontrolu konfigurace.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hackerone-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/hackerone-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hackerone-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hackerone-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-hackerone-test-user"></a>Vytvoření zkušebního uživatele HackerOne

V dalším kroku vytvořte uživatele Britta Simon v HackerOne. HackerOne podporuje just-in-time zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Při přístupu k HackerOne nového uživatele se vytvoří, pokud ještě neexistuje.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset kontaktovat tým podpory Certify. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k HackerOne použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit HackerOne Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **HackerOne**.

    ![Konfigurace jednotného přihlašování](./media/hackerone-tutorial/tutorial_hackerone_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Nakonec test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.  

Po kliknutí na dlaždici HackerOne na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci HackerOne.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hackerone-tutorial/tutorial_general_01.png
[2]: ./media/hackerone-tutorial/tutorial_general_02.png
[3]: ./media/hackerone-tutorial/tutorial_general_03.png
[4]: ./media/hackerone-tutorial/tutorial_general_04.png

[100]: ./media/hackerone-tutorial/tutorial_general_100.png

[200]: ./media/hackerone-tutorial/tutorial_general_200.png
[201]: ./media/hackerone-tutorial/tutorial_general_201.png
[202]: ./media/hackerone-tutorial/tutorial_general_202.png
[203]: ./media/hackerone-tutorial/tutorial_general_203.png

