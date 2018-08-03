---
title: 'Kurz: Integrace Azure Active Directory s TINFOIL SECURITY | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 4f25768cc7e4f5865c6cfa96ebfe3b0df97deeb6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421368"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Kurz: Integrace Azure Active Directory s TINFOIL SECURITY

V tomto kurzu se dozvíte, jak integrovat TINFOIL SECURITY s Azure Active Directory (Azure AD).

Integrace TINFOIL SECURITY s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TINFOIL SECURITY
- Uživatele, aby automaticky získat přihlášení k TINFOIL SECURITY (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TINFOIL SECURITY, potřebujete následující položky:

- S předplatným služby Azure AD
- TINFOIL SECURITY jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TINFOIL SECURITY z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-tinfoil-security-from-the-gallery"></a>Přidání TINFOIL SECURITY z Galerie
Konfigurace integrace TINFOIL SECURITY do služby Azure AD, budete muset přidat TINFOIL SECURITY z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TINFOIL SECURITY z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **TINFOIL SECURITY**vyberte **TINFOIL SECURITY** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![TINFOIL SECURITY z Galerie](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí TINFOIL SECURITY na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v TINFOIL SECURITY je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TINFOIL SECURITY potřeba navázat.

V TINFOIL SECURITY přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TINFOIL SECURITY, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  – Pokud chcete mít protějšek Britta Simon TINFOIL SECURITY, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování aplikace TINFOIL SECURITY.

**Ke konfiguraci Azure AD jednotné přihlašování s TINFOIL SECURITY, proveďte následující kroky:**

1. Na webu Azure Portal na **TINFOIL SECURITY** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Přihlašování založené na SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

1. Na **TINFOIL SECURITY domény a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Konfigurace jednotného přihlašování](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnotu.

    ![Části podpisový certifikát SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

1. Chcete-li přidat mapování požadovaný atribut, postupujte následovně:
    
    ![Atributy](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "atributy")
    
    | Název atributu    |   Hodnota atributu |
    | ------------------- | -------------------- |
    | ID účtu | UXXXXXXXXXXXXX |
    
    a. Klikněte na tlačítko **přidat atribut uživatele**.
    
    ![Přidat atribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "atributy")
    
    ![Přidat atribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "atributy")
    
    b. V **název atributu** textové pole, typ **accountid**.
    
    c. V **hodnota atributu** textové pole, vložte ID účtu hodnotu, která se zobrazí později v kurzu.
    
    d. Klikněte na tlačítko **OK**.    

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/tinfoil-security-tutorial/tutorial_general_400.png)

1. Na **TINFOIL SECURITY Configuration** klikněte na tlačítko **konfigurace TINFOIL SECURITY** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace aplikace TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti TINFOIL SECURITY.

1. Na panelu nástrojů v horní části klikněte na tlačítko **Můj účet**.
   
    ![Řídicí panel](./media/tinfoil-security-tutorial/ic798971.png "řídicího panelu")

1. Klikněte na tlačítko **zabezpečení**.
   
    ![Zabezpečení](./media/tinfoil-security-tutorial/ic798972.png "zabezpečení")

1. Na **Single Sign-On** konfigurační stránce, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/tinfoil-security-tutorial/ic798973.png "jednotného přihlašování")
   
    a. Vyberte **povolit SAML**.
   
    b. Klikněte na tlačítko **ruční konfigurace**.
   
    c. V **SAML Post URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal
   
    d. V **otisku certifikátu SAML** textového pole vložte hodnotu **kryptografický otisk** zkopírovanou z **podpisový certifikát SAML** oddílu.
  
    e. Kopírování **ID vašeho účtu** hodnotu a vložte tuto hodnotu v **hodnota atributu** textového pole pod **přidat atribut** části webu Azure Portal.
   
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Všichni uživatelé -> uživatele a skupiny ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Uživatel](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Vytvoření zkušebního uživatele TINFOIL SECURITY

Chcete-li povolit uživatele Azure AD k přihlášení do aplikace TINFOIL SECURITY, musí být poskytnuty do TINFOIL SECURITY. V případě TINFOIL SECURITY zřizování je ruční úloha.

**K získání uživatele zřízené, proveďte následující kroky:**

1. Pokud uživatel je součástí účet organizace, budete muset [obraťte se na tým podpory TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) získat uživatelský účet vytvořený.

1. Pokud uživatel je běžný uživatel TINFOIL SECURITY SaaS, pak může uživatel přidat spolupracovníka k některému z webů uživatele. Tím se spustí proces odeslání pozvánky k zadané e-mailu k vytvoření nového uživatelského účtu TINFOIL SECURITY.

> [!NOTE]
> Další nástroje pro tvorbu účtu TINFOIL SECURITY uživatele nebo rozhraní API poskytovaných TINFOIL SECURITY můžete použít ke zřízení uživatelských účtů služby Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k TINFOIL SECURITY.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit TINFOIL SECURITY, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **TINFOIL SECURITY**.

    ![Vyberte TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TINFOIL SECURITY na přístupovém panelu, které by měl získat automaticky přihlášení k aplikace TINFOIL SECURITY. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

