---
title: 'Kurz: Integrace Azure Active Directory s Onit | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Onit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: ae6cc738baac5a9f828b8a0d4d3e0a6509b4f1b4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162561"
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Kurz: Integrace Azure Active Directory s Onit

V tomto kurzu se dozvíte, jak integrovat Onit s Azure Active Directory (Azure AD).

Onit integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Onit.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Onit (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Onit, potřebujete následující položky:

- Předplatné Azure AD
- Onit jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Onit z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-onit-from-the-gallery"></a>Přidání Onit z Galerie
Konfigurace integrace Onit do služby Azure AD, budete muset přidat Onit z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Onit z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Onit**vyberte **Onit** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Onit v seznamu výsledků](./media/onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Onit podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Onit je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Onit potřeba navázat.

V Onit, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Onit, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele Onit](#create-an-onit-test-user)**  – Pokud chcete mít protějšek Britta Simon Onit, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Onit.

**Ke konfiguraci Azure AD jednotné přihlašování s Onit, proveďte následující kroky:**

1. Na webu Azure Portal na **Onit** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/onit-tutorial/tutorial_onit_samlbase.png)

1. Na **Onit domény a adresy URL** části, proveďte následující kroky:

    ![Onit domény a adresy URL jednotného přihlašování – informace](./media/onit-tutorial/tutorial_onit_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<sub-domain>.onit.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Onit klienta](https://www.onit.com/support) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/onit-tutorial/tutorial_onit_certificate.png) 

1. Onit aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **"Atrribute"** kartu aplikace. Následující snímek obrazovky ukazuje příklad pro tuto. 

    ![Konfigurace jednotného přihlašování](./media/onit-tutorial/tutorial_onit_attribute.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | e-mail | user.mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/onit-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/onit-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/onit-tutorial/tutorial_general_400.png)

1. Na **Onit konfigurace** klikněte na tlačítko **nakonfigurovat Onit** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování, SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Onit](./media/onit-tutorial/tutorial_onit_configure.png)

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Onit.

1. V nabídce v horní části klikněte na tlačítko **správu**.
   
   ![Správa](./media/onit-tutorial/IC791174.png "správy")
1. Klikněte na tlačítko **úpravy Corporation**.
   
   ![Upravit Corporation](./media/onit-tutorial/IC791175.png "úpravy Corporation")
   
1. Klikněte na tlačítko **zabezpečení** kartu.
    
    ![Informace o společnosti úpravy](./media/onit-tutorial/IC791176.png "informace o společnosti úpravy")

1. Na **zabezpečení** kartu, proveďte následující kroky:

    ![Jednotné přihlašování](./media/onit-tutorial/IC791177.png "jednotného přihlašování")

    a. Jako **strategie ověření**vyberte **jednotného přihlašování a heslo**.
    
    b. V **cílová adresa URL zprostředkovatele identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **odhlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. V **otisk certifikátu zprostředkovatele identity (SHA1)** vložit do textového pole **kryptografický otisk** hodnota certifikátu, který jste zkopírovali z portálu Azure portal.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/onit-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/onit-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/onit-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/onit-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-onit-test-user"></a>Vytvořit testovacího uživatele Onit

Chcete-li povolit uživatele Azure AD k přihlášení do Onit, musí být poskytnuty do Onit.  

V případě Onit zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Onit** společnosti serveru jako správce.
1. Klikněte na tlačítko **přidat uživatele**.
   
   ![Správa](./media/onit-tutorial/IC791180.png "správy")
1. Na **přidat uživatele** dialogového okna stránky, proveďte následující kroky:
   
   ![Přidání uživatele](./media/onit-tutorial/IC791181.png "přidat uživatele")
   
  1. Typ **název** a **e-mailovou adresu** platný Azure AD účet ke zřízení do související textových polí.
  1. Klikněte na možnost **Vytvořit**.    
   
 > [!NOTE]
 > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Onit použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Onit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Onit**.

    ![Odkaz Onit v seznamu aplikací](./media/onit-tutorial/tutorial_onit_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Onit na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Onit.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onit-tutorial/tutorial_general_01.png
[2]: ./media/onit-tutorial/tutorial_general_02.png
[3]: ./media/onit-tutorial/tutorial_general_03.png
[4]: ./media/onit-tutorial/tutorial_general_04.png

[100]: ./media/onit-tutorial/tutorial_general_100.png

[200]: ./media/onit-tutorial/tutorial_general_200.png
[201]: ./media/onit-tutorial/tutorial_general_201.png
[202]: ./media/onit-tutorial/tutorial_general_202.png
[203]: ./media/onit-tutorial/tutorial_general_203.png

