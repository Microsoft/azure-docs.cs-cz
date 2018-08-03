---
title: 'Kurz: Integrace Azure Active Directory se službou Bonusly | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 175c00d36491fbf43149aef9a590219b330581c1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422973"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Kurz: Integrace Azure Active Directory se službou Bonusly

V tomto kurzu se dozvíte, jak integrovat Bonusly s Azure Active Directory (Azure AD).

Bonusly integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Bonusly
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Bonusly (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Bonusly, potřebujete následující položky:

- S předplatným služby Azure AD
- Bonusly jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Bonusly z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bonusly-from-the-gallery"></a>Přidání Bonusly z Galerie
Konfigurace integrace Bonusly do služby Azure AD, budete muset přidat Bonusly z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Bonusly z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Bonusly**vyberte **Bonusly** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Bonusly v seznamu výsledků](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Bonusly podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Bonusly je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Bonusly potřeba navázat.

V Bonusly, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bonusly, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořte Bonusly testovacího uživatele](#create-a-bonusly-test-user)**  – Pokud chcete mít protějšek Britta Simon Bonusly, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Bonusly.

**Ke konfiguraci Azure AD jednotné přihlašování s Bonusly, proveďte následující kroky:**

1. Na webu Azure Portal na **Bonusly** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. Na **Bonusly domény a adresy URL** části, proveďte následující kroky:

    ![Bonusly domény a adresy URL jednotného přihlašování – informace](./media/bonus-tutorial/tutorial_bonusly_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Hodnota není skutečný. Skutečná adresa URL odpovědi zaktualizujte příslušnou hodnotu. Kontakt [tým podpory Bonusly](https://bonus.ly/contact) má být získána hodnota.
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnotu z certifikátu.

    ![Odkaz ke stažení certifikátu](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/bonus-tutorial/tutorial_general_400.png)

1. Na **Bonusly konfigurace** klikněte na tlačítko **nakonfigurovat Bonusly** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Bonusly konfigurace](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. V jiném okně prohlížeče, přihlaste se k vaší **Bonusly** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a pak vyberte **integrace a aplikace**.
   
    ![Bonusly sociální části](./media/bonus-tutorial/ic773686.png "Bonusly")
1. V části **Single Sign-On**vyberte **SAML**.

1. Na **SAML** dialogového okna stránky, proveďte následující kroky:
   
    ![Bonusly Saml dialogového okna stránky](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. V **cílová adresa URL zprostředkovatele identity SSO** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
   
    b. V **Vystavitel zprostředkovatele identity** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 

    c. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    d. Vložit **kryptografický otisk** hodnota zkopírována z webu Azure portal do **otisků prstů certifikátů** textového pole.
   
1. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/bonus-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/bonus-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Tlačítko Přidat](./media/bonus-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/bonus-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-bonusly-test-user"></a>Vytvořte Bonusly testovacího uživatele

Chcete-li povolit uživatele Azure AD se přihlaste k Bonusly, musí být poskytnuty do Bonusly. V případě Bonusly zřizování se ruční úlohy.

>[!NOTE]
>Můžete použít jakékoli další Bonusly uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Bonusly uživatelským účtům, zřídit AAD.
>  

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. V okně webového prohlížeče Přihlaste se k tenantovi Bonusly.

1. Klikněte na tlačítko **nastavení**.
 
    ![Nastavení](./media/bonus-tutorial/ic781041.png "nastavení")

1. Klikněte na tlačítko **uživatelů a bonusy** kartu.
   
    ![Uživatelé a bonusy](./media/bonus-tutorial/ic781042.png "uživatelů a bonusy")

1. Klikněte na tlačítko **spravovat uživatele**.
   
    ![Správa uživatelů](./media/bonus-tutorial/ic781043.png "Správa uživatelů")

1. Klikněte na tlačítko **přidat uživatele**.
   
    ![Přidání uživatele](./media/bonus-tutorial/ic781044.png "přidat uživatele")

1. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
   
    ![Přidání uživatele](./media/bonus-tutorial/ic781045.png "přidat uživatele")  

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.
 
    c. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    d. Klikněte na **Uložit**.
   
     >[!NOTE]
     >Držitel účtu Azure AD obdrží e-mailu, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Bonusly použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Bonusly Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Bonusly**.

    ![Odkaz Bonusly v seznamu aplikací](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Když kliknete na dlaždici Bonusly na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Bonusly.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

