---
title: 'Kurz: Integrace Azure Active Directory s Thoughtworks Mingle | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 7652f0179725dbe04c3245028491fae5f8c4dac7
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813249"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Kurz: Integrace Azure Active Directory s Thoughtworks Mingle

V tomto kurzu se dozvíte, jak integrovat Thoughtworks Mingle s Azure Active Directory (Azure AD).

Integrace s Azure AD Thoughtworks Mingle poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Thoughtworks Mingle
- Uživatele, aby automaticky získat přihlášení k Thoughtworks Mingle (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Thoughtworks Mingle, potřebujete následující položky:

- Předplatné Azure AD
- Thoughtworks Mingle jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Thoughtworks Mingle z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Přidání Thoughtworks Mingle z Galerie
Pokud chcete nakonfigurovat integraci Thoughtworks Mingle do služby Azure AD, musíte doplnit Thoughtworks Mingle z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Thoughtworks Mingle z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Thoughtworks Mingle**vyberte **Thoughtworks Mingle** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Thoughtworks Mingle v seznamu výsledků](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Thoughtworks Mingle podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Thoughtworks Mingle je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Thoughtworks Mingle potřeba navázat.

V Thoughtworks Mingle přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Thoughtworks Mingle, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  – Pokud chcete mít protějšek Britta Simon Thoughtworks Mingle, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Thoughtworks Mingle.

**Ke konfiguraci Azure AD jednotné přihlašování s Thoughtworks Mingle, proveďte následující kroky:**

1. Na webu Azure Portal na **Thoughtworks Mingle** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

1. Na **Thoughtworks Mingle domény a adresy URL** části, proveďte následující kroky:

    ![Thoughtworks Mingle domény a adresy URL jednotného přihlašování – informace](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Thoughtworks Mingle klienta](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) má být získána hodnota. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

1. Přihlaste se k vaší **Thoughtworks Mingle** společnosti serveru jako správce.

1. Klikněte na tlačítko **správce** kartu a potom klikněte na **Konfigurace jednotného přihlašování**.
   
    ![Karta správce](./media/thoughtworks-mingle-tutorial/ic785157.png "Konfigurace jednotného přihlašování")

1. V **Konfigurace jednotného přihlašování** části, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/thoughtworks-mingle-tutorial/ic785158.png "Konfigurace jednotného přihlašování")
    
    a. Pokud chcete nahrát soubor metadat, klikněte na tlačítko **zvolte soubor**. 

    b. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Tlačítko Přidat](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Vytvoření zkušebního uživatele Thoughtworks Mingle

Pro uživatele Azure AD bude moct přihlásit musí být zřízená Thoughtworks Mingle aplikace pomocí jejich uživatelská jména Azure Active Directory. V případě Thoughtworks Mingle zřizování je ruční úloha.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se na váš web společnosti Thoughtworks Mingle jako správce.

1. Klikněte na tlačítko **profilu**.
   
    ![Svůj první projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "svůj první projekt")

1. Klikněte na tlačítko **správce** kartu a potom klikněte na tlačítko **uživatelé**.
   
    ![Uživatelé](./media/thoughtworks-mingle-tutorial/ic785161.png "uživatelů")

1. Klikněte na tlačítko **nového uživatele**.
   
    ![Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785162.png "nového uživatele")

1. Na **nového uživatele** dialogového okna stránky, proveďte následující kroky:
   
    ![Dialogové okno Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785163.png "nového uživatele")  
 
    a. Typ **přihlašovacího jména**, **zobrazovaný název**, **zvolit heslo**, **potvrzení hesla** platný Azure chcete zřídit účet AD do související textových polí. 

    b. Jako **typ uživatele**vyberte **úplné uživatelské**.

    c. Klikněte na tlačítko **profil vytvořit**.

>[!NOTE]
>Můžete použít jakékoli jiné Thoughtworks Mingle uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Thoughtworks Mingle uživatelským účtům, zřídit AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Thoughtworks Mingle.

![Přiřazení role uživatele][200] 

**Chcete-li přiřadit Thoughtworks Mingle Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Thoughtworks Mingle**.

    ![Odkaz Thoughtworks Mingle v seznamu aplikací](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Thoughtworks Mingle na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Thoughtworks Mingle.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

