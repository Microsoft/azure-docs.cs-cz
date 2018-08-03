---
title: 'Kurz: Integrace Azure Active Directory se službou Kintone | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 693211245ee98849548bee4a52f7e424dd8b4cc6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430453"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Kurz: Integrace Azure Active Directory se službou Kintone

V tomto kurzu se dozvíte, jak integrovat Kintone s Azure Active Directory (Azure AD).

Kintone integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup pro Kintone
- Můžete povolit uživatelům, aby automaticky získat přihlášeného pro Kintone (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Kintone, potřebujete následující položky:

- S předplatným služby Azure AD
- Kintone jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kintone z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kintone-from-the-gallery"></a>Přidání Kintone z Galerie
Konfigurace integrace Kintone do služby Azure AD, budete muset přidat Kintone z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Kintone z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Kintone**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kintone-tutorial/tutorial_kintone_search.png)

1. Na panelu výsledků vyberte **Kintone**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Kintone podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Kintone je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Kintone.

V Kintone, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Kintone, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Kintone](#creating-a-kintone-test-user)**  – Pokud chcete mít protějšek Britta Simon Kintone, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Kintone.

**Ke konfiguraci Azure AD jednotné přihlašování s Kintone, proveďte následující kroky:**

1. Na webu Azure Portal na **Kintone** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/kintone-tutorial/tutorial_kintone_samlbase.png)

1. Na **Kintone domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kintone-tutorial/tutorial_kintone_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.kintone.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Kintone klienta](https://www.kintone.com/contact/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/kintone-tutorial/tutorial_kintone_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/kintone-tutorial/tutorial_general_400.png)

1. Na **Kintone konfigurace** klikněte na tlačítko **nakonfigurovat Kintone** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/kintone-tutorial/tutorial_kintone_configure.png) 

1. V okně jiné webové prohlížeče, přihlaste se do vaší **Kintone** společnosti serveru jako správce.

1. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/kintone-tutorial/ic785879.png "nastavení")

1. Klikněte na tlačítko **uživatelů a správa systému**.
   
    ![Uživatelé a správa systému](./media/kintone-tutorial/ic785880.png "uživatelů a správa systému")

1. V části **Správa systému \> zabezpečení** klikněte na tlačítko **přihlášení**.
   
    ![Přihlášení](./media/kintone-tutorial/ic785881.png "přihlášení")

1. Klikněte na tlačítko **ověřování povolit SAML**.
   
    ![Ověřování SAML](./media/kintone-tutorial/ic785882.png "ověřování SAML")

1. V části ověřování SAML proveďte následující kroky:
    
    ![Ověřování SAML](./media/kintone-tutorial/ic785883.png "ověřování SAML")
    
    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    b. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
    
    c. Klikněte na tlačítko **Procházet** na stažený certifikát nahrajete.
    
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kintone-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/kintone-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kintone-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kintone-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kintone-test-user"></a>Vytvoření zkušebního uživatele Kintone

Pokud chcete povolit Azure AD uživatelům přihlášení pro Kintone, musí být poskytnuty do Kintone.  
V případě Kintone zřizování se ruční úlohy.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k vaší **Kintone** společnosti serveru jako správce.

1. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/kintone-tutorial/ic785879.png "nastavení")

1. Klikněte na tlačítko **uživatelů a správa systému**.
   
    ![Uživatel & Správa systému](./media/kintone-tutorial/ic785880.png "uživateli a správa systému")

1. V části **Správa uživatelů**, klikněte na tlačítko **oddělení a uživatelé**.
   
    ![Oddělení a uživatelé](./media/kintone-tutorial/ic785888.png "oddělení a uživatelů")

1. Klikněte na tlačítko **nového uživatele**.
   
    ![Noví uživatelé](./media/kintone-tutorial/ic785889.png "noví uživatelé")

1. V **nového uživatele** části, proveďte následující kroky:
   
    ![Noví uživatelé](./media/kintone-tutorial/ic785890.png "noví uživatelé")
   
    a. Zadejte **zobrazovaný název**, **přihlašovací jméno**, **nové heslo**, **potvrzení hesla**, **e-mailová adresa**, a další podrobnosti o platný účet AAD chcete zřídit do související textových polí.
 
    b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné Kintone uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Kintone uživatelským účtům, zřídit AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup pro Kintone.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon pro Kintone, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Kintone**.

    ![Konfigurace jednotného přihlašování](./media/kintone-tutorial/tutorial_kintone_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Kintone na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Kintone.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kintone-tutorial/tutorial_general_01.png
[2]: ./media/kintone-tutorial/tutorial_general_02.png
[3]: ./media/kintone-tutorial/tutorial_general_03.png
[4]: ./media/kintone-tutorial/tutorial_general_04.png

[100]: ./media/kintone-tutorial/tutorial_general_100.png

[200]: ./media/kintone-tutorial/tutorial_general_200.png
[201]: ./media/kintone-tutorial/tutorial_general_201.png
[202]: ./media/kintone-tutorial/tutorial_general_202.png
[203]: ./media/kintone-tutorial/tutorial_general_203.png

