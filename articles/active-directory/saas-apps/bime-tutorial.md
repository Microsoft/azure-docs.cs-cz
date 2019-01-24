---
title: 'Kurz: Integrace Azure Active Directory s Bime | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Bime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: f00d9e094f7ced7c8ac31e2e2dae1657db1e82f2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815821"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Kurz: Integrace Azure Active Directory s Bime

V tomto kurzu se dozvíte, jak integrovat Bime s Azure Active Directory (Azure AD).

Bime integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Bime
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Bime (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Bime, potřebujete následující položky:

- Předplatné Azure AD
- Bime jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [Zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Bime z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bime-from-the-gallery"></a>Přidání Bime z Galerie
Konfigurace integrace Bime do služby Azure AD, budete muset přidat Bime z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Bime z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Bime**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bime-tutorial/tutorial_bime_search.png)

1. Na panelu výsledků vyberte **Bime**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bime-tutorial/tutorial_bime_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Bime podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Bime je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Bime potřeba navázat.

V Bime, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bime, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Bime](#creating-a-bime-test-user)**  – Pokud chcete mít protějšek Britta Simon Bime, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Bime.

**Ke konfiguraci Azure AD jednotné přihlašování s Bime, proveďte následující kroky:**

1. Na webu Azure Portal na **Bime** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/tutorial_bime_samlbase.png)

1. Na **Bime domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/tutorial_bime_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.Bimeapp.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Bime klienta](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnotu z certifikátu.

    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/tutorial_bime_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/tutorial_general_400.png)

1. Na **Bime konfigurace** klikněte na tlačítko **nakonfigurovat Bime** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/tutorial_bime_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Bime.

1. Na panelu nástrojů klikněte na tlačítko **správce**a potom **účet**.
   
    ![Správce](./media/bime-tutorial/ic775558.png "správce")

1. Na stránce konfigurace účtu postupujte následovně:
   
    ![Konfigurovat Single Sign-On](./media/bime-tutorial/ic775559.png "nakonfigurovat jednotné přihlašování")
   
    a. Vyberte **ověřování povolit SAML**.

    b. V **Vzdálená adresa URL pro přihlášení** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c.  Vložit **kryptografický otisk** hodnotu z webu Azure portal do **otisku certifikátu** textového pole.       
   
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bime-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/bime-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/bime-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/bime-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-bime-test-user"></a>Vytvoření zkušebního uživatele Bime

Chcete-li povolit uživatele Azure AD se přihlaste k Bime, musí být poskytnuty do Bime. V případě Bime zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Bime** tenanta.

1. Na panelu nástrojů klikněte na tlačítko **správce**a potom **uživatelé**.
   
    ![Správce](./media/bime-tutorial/ic775561.png "správce")

1. V **seznamu uživatelů**, klikněte na tlačítko **přidat nového uživatele** ("+").
   
    ![Uživatelé](./media/bime-tutorial/ic775562.png "uživatelů")

1. Na **podrobnosti o uživateli** dialogového okna stránky, proveďte následující kroky:
   
    ![Podrobnosti o uživateli](./media/bime-tutorial/ic775563.png "podrobnosti o uživateli")
   
    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.
 
    c. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    d. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné Bime uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Bime uživatelským účtům, zřídit AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Bime použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Bime Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Bime**.

    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/tutorial_bime_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Bime na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Bime.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bime-tutorial/tutorial_general_01.png
[2]: ./media/bime-tutorial/tutorial_general_02.png
[3]: ./media/bime-tutorial/tutorial_general_03.png
[4]: ./media/bime-tutorial/tutorial_general_04.png

[100]: ./media/bime-tutorial/tutorial_general_100.png

[200]: ./media/bime-tutorial/tutorial_general_200.png
[201]: ./media/bime-tutorial/tutorial_general_201.png
[202]: ./media/bime-tutorial/tutorial_general_202.png
[203]: ./media/bime-tutorial/tutorial_general_203.png

