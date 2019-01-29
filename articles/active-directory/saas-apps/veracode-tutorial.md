---
title: 'Kurz: Integrace Azure Active Directory s Veracode | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 87eff8eb96f2a6b090883e0b557a568eb0a20d54
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156327"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Kurz: Integrace Azure Active Directory s Veracode

V tomto kurzu se dozvíte, jak integrovat Veracode s Azure Active Directory (Azure AD).

Veracode integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Veracode.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Veracode (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Veracode, potřebujete následující položky:

- Předplatné Azure AD
- Veracode jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Veracode z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-veracode-from-the-gallery"></a>Přidání Veracode z Galerie
Konfigurace integrace Veracode do služby Azure AD, budete muset přidat Veracode z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Veracode z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Veracode**vyberte **Veracode** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Veracode v seznamu výsledků](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Veracode podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Veracode je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Veracode potřeba navázat.

V Veracode, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Veracode, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Veracode](#create-a-veracode-test-user)**  – Pokud chcete mít protějšek Britta Simon Veracode, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Veracode.

**Ke konfiguraci Azure AD jednotné přihlašování s Veracode, proveďte následující kroky:**

1. Na webu Azure Portal na **Veracode** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. Na **Veracode domény a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure. 

    ![Konfigurace jednotného přihlašování](./media/veracode-tutorial/tutorial_veracode_url.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. Cílem této části se popisují, jak povolit uživatelům ověření pro Veracode pomocí svého účtu ve službě Azure AD využívající federaci založené na protokolu SAML.

    Vaše aplikace Veracode očekává, že kontrolní výrazy SAML v určitém formátu, které je potřeba přidat vlastní atribut mapování vaší **atributy tokenu saml** konfigurace. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Atributy](./media/veracode-tutorial/tutorial_veracode_attr.png "atributy")

1. Chcete-li přidat mapování požadovaný atribut, postupujte následovně:

    | Název atributu | Hodnota atributu |
    |--- |--- |
    | jméno |User.givenName |
    | Příjmení |User.Surname |
    | e-mail |User.mail |
    
    a. Pro každý řádek dat v tabulce výše, klikněte na tlačítko **přidat atribut uživatele**.
    
    ![Atributy](./media/veracode-tutorial/tutorial_veracode_addattr.png "atributy")
    
    ![Atributy](./media/veracode-tutorial/tutorial_veracode_addattr1.png "atributy")
    
    b. V **název atributu** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. V **hodnota atributu** textové pole, vyberte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/veracode-tutorial/tutorial_general_400.png)

1. Na **Veracode konfigurace** klikněte na tlačítko **nakonfigurovat Veracode** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Veracode](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Veracode.

1. V nabídce v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **správce**.
   
    ![Správa](./media/veracode-tutorial/ic802911.png "správy")

1. Klikněte na tlačítko **SAML** kartu.

1. V **nastavení SAML organizace** části, proveďte následující kroky:
   
    ![Správa](./media/veracode-tutorial/ic802912.png "správy")
   
    a.  V **vystavitele** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.
    
    b. Chcete-li nahrát svůj certifikát stažený z webu Azure portal, klikněte na tlačítko **zvolit soubor**.
   
    c. Vyberte **povolení samoobslužné registrace**.

1. V **nastavení samoobslužné registrace** části, proveďte následující kroky a pak klikněte na tlačítko **Uložit**:
   
    ![Správa](./media/veracode-tutorial/ic802913.png "správy")
   
    a. Jako **aktivace nového uživatele**vyberte **vyžaduje její aktivace bez**.
   
    b. Jako **aktualizace dat uživatele**vyberte **předvoleb Veracode uživatelská Data**.
   
    c. Pro **podrobných informací o atributech SAML**, vyberte následující:
      * **Role uživatele**
      * **Správce zásad**
      * **Revidující**
      * **Zabezpečení zájemce**
      * **Výkonný**
      * **Odesílatel**
      * **Creator**
      * **Všechny kontroly typů**
      * **Členství v týmech**
      * **Výchozí tým**

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/veracode-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/veracode-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/veracode-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/veracode-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-veracode-test-user"></a>Vytvoření zkušebního uživatele Veracode
Chcete-li povolit uživatele Azure AD k přihlášení do Veracode, musí být poskytnuty do Veracode. V případě Veracode zřizování se automatická úloha. Neexistuje žádná položka akce za vás. Uživatelé se automaticky vytvoří v případě potřeby během první jednotné přihlašování pokus.

> [!NOTE]
> Můžete použít jakékoli jiné Veracode uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Veracode zřízení uživatelských účtů služby Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Veracode použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Veracode Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Veracode**.

    ![Odkaz Veracode v seznamu aplikací](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Veracode na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Veracode.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

