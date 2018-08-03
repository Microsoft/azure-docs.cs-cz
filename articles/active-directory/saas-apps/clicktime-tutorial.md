---
title: 'Kurz: Integrace Azure Active Directory se službou ClickTime | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: 065225bb6c206f980c19955c682fc2c8a5deb950
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441764"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Kurz: Integrace Azure Active Directory se službou ClickTime

V tomto kurzu se dozvíte, jak integrovat ClickTime s Azure Active Directory (Azure AD).

ClickTime integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ClickTime
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ClickTime (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ClickTime, potřebujete následující položky:

- S předplatným služby Azure AD
- ClickTime jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ClickTime z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-clicktime-from-the-gallery"></a>Přidání ClickTime z Galerie
Konfigurace integrace ClickTime do služby Azure AD, budete muset přidat ClickTime z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ClickTime z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **ClickTime**vyberte **ClickTime** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ClickTime v seznamu výsledků](./media/clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ClickTime podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ClickTime je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ClickTime potřeba navázat.

V ClickTime, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ClickTime, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele ClickTime](#create-a-clicktime-test-user)**  – Pokud chcete mít protějšek Britta Simon ClickTime, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ClickTime.

**Ke konfiguraci Azure AD jednotné přihlašování s ClickTime, proveďte následující kroky:**

1. Na webu Azure Portal na **ClickTime** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/clicktime-tutorial/tutorial_clicktime_samlbase.png)

1. Na **ClickTime domény a adresy URL** části, proveďte následující kroky:

    ![ClickTime domény a adresy URL jednotného přihlašování – informace](./media/clicktime-tutorial/tutorial_clicktime_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL jako: `https://app.clicktime.com/sp/`
    
    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujících vzorů: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/clicktime-tutorial/tutorial_clicktime_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/clicktime-tutorial/tutorial_general_400.png)

1. Na **ClickTime konfigurace** klikněte na tlačítko **nakonfigurovat ClickTime** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace ClickTime](./media/clicktime-tutorial/tutorial_clicktime_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti ClickTime.

1. Na panelu nástrojů v horní části klikněte na tlačítko **Předvolby**a potom klikněte na tlačítko **nastavení zabezpečení**.

1. V **předvolby přihlašování** konfigurační oddíl, proveďte následující kroky:
   
    ![Nastavení zabezpečení](./media/clicktime-tutorial/tic777280.png "nastavení zabezpečení")
   
    a.  Vyberte **povolit** přihlašovat pomocí jednotné přihlašování (SSO) s **Azure AD**.
   
    b. V **koncový bod poskytovatele Identity** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    c.  Otevřít **certifikáty s kódováním base-64** stáhnout z webu Azure portal v **Poznámkový blok**, zkopírujte obsah a vložte jej do **certifikát X.509** textového pole.
   
    d.  Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/clicktime-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/clicktime-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.
 
    ![Tlačítko Přidat](./media/clicktime-tutorial/create_aaduser_03.png) 

1. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/clicktime-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-clicktime-test-user"></a>Vytvoření zkušebního uživatele ClickTime

Chcete-li povolit uživatele Azure AD k přihlášení do ClickTime, musí být poskytnuty do ClickTime.  
V případě ClickTime zřizování se ruční úlohy.

> [!NOTE]
> Můžete použít jakékoli jiné ClickTime uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných ClickTime zřízení uživatelských účtů služby Azure AD.

**K poskytnutí uživatelského účtu, postupujte následovně:**
1. Přihlaste se k vaší **ClickTime** tenanta.
1. Na panelu nástrojů v horní části klikněte na tlačítko **společnosti**a potom klikněte na tlačítko **lidé**.
   
    ![Lidé](./media/clicktime-tutorial/tic777282.png "osoby")
1. Klikněte na tlačítko **přidat osobu**.
   
    ![Přidat osobu](./media/clicktime-tutorial/tic777283.png "přidat osoby")
1. V části nové osobě proveďte následující kroky:
   
    ![Lidé](./media/clicktime-tutorial/tic777284.png "osoby")
   
    a.  V **celý název** textové pole, typ celé jméno uživatele, jako je **Britta Simon**. 
  
    b.  V **e-mailová adresa** , jako je textové pole, typ e-mailu uživatele **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Pokud chcete, můžete nastavit další vlastnosti nového objektu osoba.
   
    c.  Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k ClickTime použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit ClickTime Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ClickTime**.

    ![ClickTimne odkaz v seznamu aplikací](./media/clicktime-tutorial/tutorial_clicktime_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ClickTime na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ClickTime.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clicktime-tutorial/tutorial_general_01.png
[2]: ./media/clicktime-tutorial/tutorial_general_02.png
[3]: ./media/clicktime-tutorial/tutorial_general_03.png
[4]: ./media/clicktime-tutorial/tutorial_general_04.png

[100]: ./media/clicktime-tutorial/tutorial_general_100.png

[200]: ./media/clicktime-tutorial/tutorial_general_200.png
[201]: ./media/clicktime-tutorial/tutorial_general_201.png
[202]: ./media/clicktime-tutorial/tutorial_general_202.png
[203]: ./media/clicktime-tutorial/tutorial_general_203.png

