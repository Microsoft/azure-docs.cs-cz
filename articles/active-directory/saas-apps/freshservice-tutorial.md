---
title: 'Kurz: Integrace Azure Active Directory se službou Freshservice | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.openlocfilehash: eb848ede258d8d25d4734664bd500235f34359e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440656"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Kurz: Integrace Azure Active Directory se službou Freshservice

V tomto kurzu se dozvíte, jak integrovat Freshservice s Azure Active Directory (Azure AD).

Freshservice integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Freshservice
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Freshservice (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Freshservice, potřebujete následující položky:

- S předplatným služby Azure AD
- Freshservice jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Freshservice z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-freshservice-from-the-gallery"></a>Přidání Freshservice z Galerie
Konfigurace integrace Freshservice do služby Azure AD, budete muset přidat Freshservice z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Freshservice z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Freshservice**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshservice-tutorial/tutorial_freshservice_search.png)

1. Na panelu výsledků vyberte **Freshservice**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Freshservice podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Freshservice je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Freshservice.

V Freshservice, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Freshservice, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Freshservice](#creating-a-freshservice-test-user)**  – Pokud chcete mít protějšek Britta Simon Freshservice, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Freshservice.

**Ke konfiguraci Azure AD jednotné přihlašování s Freshservice, proveďte následující kroky:**

1. Na webu Azure Portal na **Freshservice** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/freshservice-tutorial/tutorial_freshservice_samlbase.png)

1. Na **Freshservice domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/freshservice-tutorial/tutorial_freshservice_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<democompany>.freshservice.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Freshservice klienta](https://support.freshservice.com/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurace jednotného přihlašování](./media/freshservice-tutorial/tutorial_freshservice_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/freshservice-tutorial/tutorial_general_400.png)

1. Na **Freshservice konfigurace** klikněte na tlačítko **nakonfigurovat Freshservice** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/freshservice-tutorial/tutorial_freshservice_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Freshservice jako správce.

1. V nabídce v horní části klikněte na tlačítko **správce**.
   
    ![Správce](./media/freshservice-tutorial/ic790814.png "správce")

1. V **zákaznického portálu služeb**, klikněte na tlačítko **zabezpečení**.
   
    ![Zabezpečení](./media/freshservice-tutorial/ic790815.png "zabezpečení")

1. V **zabezpečení** části, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/freshservice-tutorial/ic790816.png "jednotné přihlašování")
   
    a. Přepínač **jednotné přihlašování**.

    b. Vyberte **SAML SSO**.

    c. V **SAML přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    e. V **otisku certifikátu zabezpečení** vložit do textového pole **kryptografický OTISK** hodnota certifikátu, který jste zkopírovali z portálu Azure portal.

    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshservice-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/freshservice-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/freshservice-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/freshservice-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-freshservice-test-user"></a>Vytvoření zkušebního uživatele Freshservice

Přihlaste se k FreshService Azure AD uživatelům umožnit, musí být poskytnuty do FreshService. V případě FreshService zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **FreshService** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **správce**.
   
    ![Správce](./media/freshservice-tutorial/ic790814.png "správce")

1. V **Správa uživatelů** klikněte na tlačítko **žadatelů o deklaraci**.
   
    ![Žadatelů o deklaraci](./media/freshservice-tutorial/ic790818.png "žadatele")

1. Klikněte na tlačítko **nový žadatel**.
   
    ![Nové žadatelů o deklaraci](./media/freshservice-tutorial/ic790819.png "nové žadatele")

1. V **nový žadatel** části, proveďte následující kroky:
   
    ![Nový žadatel](./media/freshservice-tutorial/ic790820.png "nový žadatel")   

    a. Zadejte **křestní jméno** a **e-mailu** atributy platný účet služby Azure Active Directory ke zřízení do související textových polí.

    b. Klikněte na **Uložit**.
   
    >[!NOTE]
    >Držitel účtu Azure Active Directory získá e-mailu včetně odkaz pro potvrzení účtu, pak se změní na aktivní
    >  

>[!NOTE]
>Můžete použít jakékoli jiné FreshService uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných FreshService uživatelským účtům, zřídit AAD.
>  

![Přiřadit uživatele][200] 

**Přiřadit Freshservice Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Freshservice**.

    ![Konfigurace jednotného přihlašování](./media/freshservice-tutorial/tutorial_freshservice_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Freshservice na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Freshservice.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshservice-tutorial/tutorial_general_01.png
[2]: ./media/freshservice-tutorial/tutorial_general_02.png
[3]: ./media/freshservice-tutorial/tutorial_general_03.png
[4]: ./media/freshservice-tutorial/tutorial_general_04.png

[100]: ./media/freshservice-tutorial/tutorial_general_100.png

[200]: ./media/freshservice-tutorial/tutorial_general_200.png
[201]: ./media/freshservice-tutorial/tutorial_general_201.png
[202]: ./media/freshservice-tutorial/tutorial_general_202.png
[203]: ./media/freshservice-tutorial/tutorial_general_203.png

