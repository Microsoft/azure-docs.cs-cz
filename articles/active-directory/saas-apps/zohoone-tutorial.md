---
title: 'Kurz: Integrace Azure Active Directory s Zoho jeden | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f17a297d3099d51b3a58a6654560a67f9a4192a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208312"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Kurz: Integrace Azure Active Directory s Zoho jeden

V tomto kurzu se dozvíte, jak integrovat Zoho jeden s Azure Active Directory (Azure AD).

Zoho jeden integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k jednomu Zoho.
- Uživatele, aby automaticky získat přihlášení k jedné Zoho (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jeden Zoho, potřebujete následující položky:

- Předplatné Azure AD
- Jeden Zoho jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zoho jeden z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zoho-one-from-the-gallery"></a>Přidání Zoho jeden z Galerie
Konfigurace integrace Zoho jeden do služby Azure AD, budete muset přidat Zoho jeden z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zoho jeden z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Zoho jeden**vyberte **Zoho jeden** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zoho jeden v seznamu výsledků](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Zoho jeden podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zoho jeden je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatel v jedné Zoho potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednoho Zoho, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Zoho jeden](#create-a-zoho-one-test-user)**  – Pokud chcete mít protějšek Britta Simon Zoho jeden, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Zoho jednu aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí jednoho Zoho, proveďte následující kroky:**

1. Na webu Azure Portal na **Zoho jeden** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. Na **Zoho jedné domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Zoho jedné domény a adresy URL jednotného přihlašování – informace](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. V **identifikátor (Entity ID)** textového pole zadejte adresu URL: `one.zoho.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Zkontrolujte **zobrazit pokročilé nastavení URL**.

    d. V **stav přenosu** textového pole zadejte adresu URL:`https://one.zoho.com`

1. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu postupujte následovně:

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Předchozí **adresy URL odpovědi** a **přihlašovací adresa URL** není skutečnou hodnotu. Skutečná adresa URL odpovědi a přihlašovací adresa URL, která je vysvětlen později v tomto kurzu jste se aktualizuje hodnotu. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. Na **Zoho jednu konfiguraci** klikněte na tlačítko **nakonfigurovat jeden Zoho** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Zoho jedna konfigurace](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. V jiných webových okno prohlížeče Přihlaste se k webu Zoho jeden společnosti jako správce.

1. Na **organizace** kartu, klikněte na tlačítko **nastavení** pod **ověřování SAML**.

    ![Zoho jedné organizaci](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. Na stránce rozbalovací proveďte následující kroky:

    ![Zoho jeden podpis](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    b. V **odhlašování URL** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    c. Klikněte na tlačítko **Procházet** k nahrání **certifikát (Base64)** který jste si stáhli z webu Azure portal.

    d. Klikněte na **Uložit**.

1. Po uložení nastavení ověřování SAML, zkopírujte **SAML Identfier** hodnotu a použít tuto hodnotu v **adresy URL odpovědi** na webu Azure Portal, v části **Zoho jedné domény a adresy URL** oddíl.

    ![Zoho jeden saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Přejděte **domén** kartu a potom klikněte na tlačítko **přidat doménu**.

    ![Zoho jednu doménu](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. Na **přidat doménu** stránce, proveďte následující kroky:

    ![Zoho jeden přidat doménu](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. V **název domény** textové pole, typ domény jako **contoso.com**.

    b. Klikněte na tlačítko **Add** (Přidat).

    >[!Note]
    >Po přidání domény postupujte [tyto](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroky k ověření vaší domény. Jakmile doménu verfified, použít název domény v **přihlašovací adresa URL** v **Zoho jedné domény a adresy URL** části webu Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/zohoone-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zohoone-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zohoone-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zoho-one-test-user"></a>Vytvořte jeden Zoho testovacího uživatele

Pokud chcete povolit Azure AD uživatelům se přihlásit k jednomu Zoho, musí být poskytnuty do Zoho jeden. V jedné Zoho zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Zoho jeden jako správce zabezpečení.

1. Na **uživatelé** kartu, klepněte na **uživatele logo**.

    ![Zoho jeden uživatel](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. Na **přidat uživatele** stránce, proveďte následující kroky:

    ![Zoho jeden přidat uživatele](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. V **název** textové pole, zadejte jméno uživatele, jako je **Britta simon**.
    
    b. V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    >[!Note]
    >Vyberte ověřenou doménu v seznamu domény.

    c. Klikněte na tlačítko **Add** (Přidat).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k jednomu Zoho.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit jednomu Zoho, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Zoho jeden**.

    ![Zoho jedno propojení v seznamu aplikací](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Zoho jednu dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zoho jeden.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

