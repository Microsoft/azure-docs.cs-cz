---
title: 'Kurz: Integrace Azure Active Directory s Proxyclick | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52d3e058c7e604a31493bf3b1972b39c9922fd90
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163511"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Kurz: Integrace Azure Active Directory s Proxyclick

V tomto kurzu se dozvíte, jak integrovat Proxyclick s Azure Active Directory (Azure AD).

Proxyclick integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Proxyclick.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Proxyclick (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Proxyclick, potřebujete následující položky:

- Předplatné Azure AD
- Proxyclick jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Proxyclick z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie
Konfigurace integrace Proxyclick do služby Azure AD, budete muset přidat Proxyclick z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Proxyclick z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Proxyclick**vyberte **Proxyclick** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Proxyclick v seznamu výsledků](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Proxyclick podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Proxyclick je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Proxyclick potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Proxyclick, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Proxyclick](#create-a-proxyclick-test-user)**  – Pokud chcete mít protějšek Britta Simon Proxyclick, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Proxyclick.

**Ke konfiguraci Azure AD jednotné přihlašování s Proxyclick, proveďte následující kroky:**

1. Na webu Azure Portal na **Proxyclick** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. Na **Proxyclick domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Proxyclick domény a adresy URL jednotného přihlašování – informace](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://saml.proxyclick.com/init/<companyId>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://saml.proxyclick.com/consume/<companyId>`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Proxyclick domény a adresy URL jednotného přihlašování – informace](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/proxyclick-tutorial/tutorial_general_400.png)

1. Na **Proxyclick konfigurace** klikněte na tlačítko **nakonfigurovat Proxyclick** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Proxyclick jako správce.

1. Vyberte **účet a nastavení**.

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure1.png)

1. Přejděte dolů k položce **integrace** a vyberte **SAML**.

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure2.png)

1. V **SAML** části, proveďte následující kroky:

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Kopírování **adresu URL příjemce SAML** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **Proxyclick domény a adresy URL** části na webu Azure portal.

    b. Kopírování **přesměrovat adresu URL jednotného přihlašování SAML** hodnotu a vložte ho do **přihlašovací adresa URL** a **identifikátor** textová pole v **Proxyclick domény a adresy URL** oddílu na portálu Azure portal.

    c. Vyberte **metoda požadavku SAML** jako **přesměrování protokolu HTTP**.

    d. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **URL koncového bodu služby SAML 2.0** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanými z webu Azure portal.

    f. Otevřete soubor stažený certifikát z webu Azure portal v programu Poznámkový blok a vložte jej do **certifikát** textového pole.

    g. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/proxyclick-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-proxyclick-test-user"></a>Vytvoření zkušebního uživatele Proxyclick

Přihlaste se k Proxyclick Azure AD uživatelům umožnit, musí být poskytnuty do Proxyclick. V případě Proxyclick zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Proxyclick jako správce.

1. Klikněte na tlačítko **kolegy** z horního navigačního panelu.

    ![Přidat zaměstnance](./media/proxyclick-tutorial/user1.png)

1. Klikněte na tlačítko **Přidat kolegy**

    ![Přidat zaměstnance](./media/proxyclick-tutorial/user2.png)

1. V **přidat kolegu** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/proxyclick-tutorial/user3.png)

    a. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele **brittasimon@contoso.com**.

    b. V **křestní jméno** textového pole Název typu prvního uživatele, jako je Britta.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    d. Klikněte na tlačítko **přidat uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Proxyclick použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Proxyclick Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Proxyclick**.

    ![Odkaz Proxyclick v seznamu aplikací](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Proxyclick na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Proxyclick.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

