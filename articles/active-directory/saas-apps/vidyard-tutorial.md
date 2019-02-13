---
title: 'Kurz: Integrace Azure Active Directory s Vidyard | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d25520f88869025f7e262d81295ee64f8e12d21
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163590"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Kurz: Integrace Azure Active Directory s Vidyard

V tomto kurzu se dozvíte, jak integrovat Vidyard s Azure Active Directory (Azure AD).

Vidyard integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Vidyard.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Vidyard (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Vidyard, potřebujete následující položky:

- Předplatné Azure AD
- Vidyard jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Vidyard z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-vidyard-from-the-gallery"></a>Přidání Vidyard z Galerie
Konfigurace integrace Vidyard do služby Azure AD, budete muset přidat Vidyard z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Vidyard z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Vidyard**vyberte **Vidyard** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Vidyard v seznamu výsledků](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Vidyard podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Vidyard je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Vidyard potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vidyard, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Vidyard](#create-a-vidyard-test-user)**  – Pokud chcete mít protějšek Britta Simon Vidyard, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Vidyard.

**Ke konfiguraci Azure AD jednotné přihlašování s Vidyard, proveďte následující kroky:**

1. Na webu Azure Portal na **Vidyard** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Na **Vidyard domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Vidyard domény a adresy URL jednotného přihlašování – informace](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Vidyard domény a adresy URL jednotného přihlašování – informace](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, který je vysvětlen později v tomto kurzu

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/vidyard-tutorial/tutorial_general_400.png)

1. Na **Vidyard konfigurace** klikněte na tlačítko **nakonfigurovat Vidyard** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Vidyard softwaru jako správce.

1. Na řídicím panelu Vidyard vyberte **skupiny** > **zabezpečení**

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure1.png)

1. Klikněte na tlačítko **nový profil** kartu.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure2.png)

1. V **konfigurace SAML** části, proveďte následující kroky:

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Zadejte název profilu Obecné v **název profilu** textového pole.

    b. Kopírování **jednotného přihlašování uživatele přihlašovací stránku** hodnotu a vložte ho do **přihlašovací adresa URL** textového pole v **Vidyard domény a adresy URL části** na portálu Azure portal.

    c. Kopírování **ACS URL** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **Vidyard domény a adresy URL části** na portálu Azure portal.

    d. Kopírování **URL vystavitele nebo Metadata** hodnotu a vložte ho do **identifikátor** textového pole v **Vidyard domény a adresy URL části** na portálu Azure portal.

    e. Otevřete soubor stažený certifikát z webu Azure portal v programu Poznámkový blok a vložte jej do **certifikát X.509** textového pole.

    f. V **adresu URL koncového bodu SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanými z webu Azure portal.

    g. Klikněte na **Confirm** (Potvrdit).

1. Na kartě Single Sign On, vyberte **přiřadit** vedle existujícího profilu

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Jakmile vytvoříte profil jednotného přihlašování, ji přiřadíte do žádné skupiny, pro kterou budou uživatelé vyžadují přístup prostřednictvím Azure. Pokud uživatel neexistuje v rámci skupiny, ke kterému byla přiřazena, Vidyard automaticky vytvořit účet uživatele a přiřadit jejich role v reálném čase.

1. Vyberte skupiny organizace, který se zobrazí v **skupiny k dispozici k přiřazení**.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure5.png)

1. Přiřazené skupiny v části můžete zobrazit **aktuálně přiřazené skupiny**. Vyberte roli pro skupinu podle vaší organizace a klikněte na **potvrdit**.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Další informace najdete v tématu [tohoto dokumentu](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/vidyard-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/vidyard-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/vidyard-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/vidyard-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-vidyard-test-user"></a>Vytvoření zkušebního uživatele Vidyard

Cílem této části je vytvořte uživatele Britta Simon v Vidyard. Vidyard podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Vidyard, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Vidyard použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Vidyard Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Vidyard**.

    ![Odkaz Vidyard v seznamu aplikací](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Vidyard na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Vidyard.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

