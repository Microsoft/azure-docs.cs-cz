---
title: 'Kurz: Integrace Azure Active Directory s Scout Nápověda | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pomáhají Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 1ea28b150b94b4f1ecc1bdb6095170ce6f409f27
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165604"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Kurz: Integrace Azure Active Directory s pomáhají Scout

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) pomáhají Scout.

Integrace s Azure AD pomoct Scout poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k nápovědě Scout.
- Uživatele, aby automaticky získat přihlášení k nápovědě Scout (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s pomáhají Scout, potřebujete následující položky:

- Předplatné Azure AD
- A pomáhají Scout jednotného přihlašování povolená předplatného

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání pomáhají Scout z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-help-scout-from-the-gallery"></a>Přidání pomáhají Scout z Galerie
Pokud chcete nakonfigurovat integrace pomůže Scout do služby Azure AD, potřebujete přidat pomáhají Scout z Galerie na váš seznam spravovaných aplikací SaaS.

**Nápověda Scout přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **pomáhají Scout**vyberte **pomáhají Scout** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Nápověda Scout v seznamu výsledků](./media/helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování služby Azure AD jednotného přihlašování pomůžou Scout podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v nápovědě Scout je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v nápovědě Scout je potřeba navázat.

Nápovědy Scout používá e-mailové adresy pro přihlášení, takže a tím vytvoří vztah odkazu, použijte stejný **e-mailová adresa** jako **uživatelské jméno** ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Scout nápovědy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele pomáhají Scout](#create-a-help-scout-test-user)**  – Pokud chcete mít protějšek Britta Simon pomáhají Scout, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vaší aplikaci pomůžou Scout.

**Ke konfiguraci Azure AD jednotné přihlašování s pomáhají Scout, proveďte následující kroky:**

1. Na webu Azure Portal na **pomáhají Scout** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/helpscout-tutorial/tutorial_helpscout_samlbase.png)

1. Na **pomáhají Scout domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Nápověda Scout domény a adresy URL jednotné přihlašování](./media/helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identifikátor** je **"Cílovou skupinu identifikátoru URI (ID Entity poskytovatele služeb)"** začíná od pomáhají Scout `urn:`

    b. **Adresa URL pro odpověď** je **"Po back URL (adresa URL služby příjemce kontrolního výrazu)"** začíná od pomáhají Scout `https://` 

    > [!NOTE] 
    > Hodnoty v těchto adres URL jsou pouze ukázku. Je potřeba aktualizovat tyto hodnoty z skutečnou odpověď URL a identifikátor. Získání těchto hodnot z **Single Sign-On** kartu v části ověřování, který je vysvětlen později v tomto kurzu.

1. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, kontrola **zobrazit pokročilé nastavení URL** a postupujte následovně:

    ![Nápověda Scout domény a adresy URL jednotné přihlašování](./media/helpscout-tutorial/tutorial_helpscout_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://secure.helpscout.net/members/login/`
     
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/helpscout-tutorial/tutorial_helpscout_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/helpscout-tutorial/tutorial_general_400.png)


1. Na **pomáhají konfigurace Scout** klikněte na tlačítko **konfigurace pomoci Scout** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka části**.

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/config.png) 

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti pomůžou Scout jako správce.

1. Po přihlášení klikněte **"Manage"** v horní nabídce a pak vyberte **"Společnost"** z rozevírací nabídky.

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings1.png) 
 
1. Vyberte **"Ověřování"** z nabídky na levé straně. 

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings2.png) 

1. Tím přejdete do části Nastavení SAML a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings3.png) 
 
    a. Kopírovat **po adresy URL (adresa URL služby příjemce kontrolního výrazu)** hodnotu a vložte tuto hodnotu v **adresy URL odpovědi** pole na webu Azure Portal, v části Nápověda Scout **domény a adresy URL** oddílu.
    
    b. Kopírovat **identifikátor URI cílové skupiny (ID Entity poskytovatele služeb)** hodnotu a vložte tuto hodnotu v **identifikátor** pole na webu Azure Portal, v části Nápověda Scout **domény a adresy URL** oddílu.

1. Přepnout **povolit SAML** na a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings4.png) 
 
    a. V **přihlašovací adresu URL jednotného** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
    
    b. Klikněte na tlačítko **nahrát certifikát** k nahrání **Certificate(Base64)** stáhnout z webu Azure portal.

    c. Zadejte vaše organizace e-mailových domén např - `contoso.com` v **e-mailových domén** textového pole. Více domén můžete oddělit čárkou. Kdykoli pomáhají Scout uživatel nebo správce, který zadá na tuto konkrétní doménu [pomáhají Scout přihlašovací stránka](https://secure.helpscout.net/members/login/) přesměruje se ke zprostředkovateli Identity kvůli ověření pomocí svých přihlašovacích údajů.

    d. A konečně, můžete přepínat **přihlašování SAML platnost** , aby uživatelé přihlašovat pouze k nápovědě Scout prostřednictvím prostřednictvím této metody. Pokud stále chcete ponechte možnost pro ně se přihlásit pomocí svých přihlašovacích údajů pomůžou Scout, můžete nechat ji přepínat vypnout. I v případě, že je tato možnost povolena, vlastníka účtu vždy bude moct přihlásit k nápovědě Scout s své heslo účtu.

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/helpscout-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/helpscout-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/helpscout-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/helpscout-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-help-scout-test-user"></a>Vytvoření zkušebního uživatele pomáhají Scout

Cílem této části je vytvořte uživatele Britta Simon v nápovědě Scout. Nápověda Scout podporuje just-in-time zřizování, což je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v nápovědě Scout, se vytvoří nový, při pokusu o přístup k nápovědě Scout.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k nápovědě Scout.

![Přiřazení role uživatele][200] 

**Chcete pomoci Scout přiřadit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **pomáhají Scout**.

    ![Na odkaz Nápověda Scout v seznamu aplikací](./media/helpscout-tutorial/tutorial_helpscout_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Nápověda Scout na přístupovém panelu, vám by měl získat automaticky přihlášení k nápovědě Scout application.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/helpscout-tutorial/tutorial_general_01.png
[2]: ./media/helpscout-tutorial/tutorial_general_02.png
[3]: ./media/helpscout-tutorial/tutorial_general_03.png
[4]: ./media/helpscout-tutorial/tutorial_general_04.png

[100]: ./media/helpscout-tutorial/tutorial_general_100.png

[200]: ./media/helpscout-tutorial/tutorial_general_200.png
[201]: ./media/helpscout-tutorial/tutorial_general_201.png
[202]: ./media/helpscout-tutorial/tutorial_general_202.png
[203]: ./media/helpscout-tutorial/tutorial_general_203.png

