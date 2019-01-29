---
title: 'Kurz: Integrace Azure Active Directory s přední | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Front.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 95b6565885435aeb964dd37bd49ae5a05c06be94
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175464"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Kurz: Integrace Azure Active Directory pomocí Front

V tomto kurzu se dozvíte, jak integrovat Front služby Azure Active Directory (Azure AD).

Přední integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do popředí.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného do popředí (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Front, potřebujete následující položky:

- Předplatné Azure AD
- Přední jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Front z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-front-from-the-gallery"></a>Přidání Front z Galerie
Konfigurace integrace Front do služby Azure AD, budete muset přidat Front z Galerie na váš seznam spravovaných aplikací SaaS.

**Přední přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **přední**vyberte **front-** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Přední v seznamu výsledků](./media/front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Front na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co vpředu protějšku uživatele je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v popředí.

V popředí, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Front, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele přední](#create-a-front-test-user)**  – Pokud chcete mít protějšek Britta Simon v popředí, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Front.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Front, proveďte následující kroky:**

1. Na webu Azure Portal na **přední** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/front-tutorial/tutorial_front_samlbase.png)

1. Na **přední domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/front-tutorial/tutorial_front_url1.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.frontapp.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.frontapp.com/sso/saml/callback`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat skutečné identifikátor a adresa URL odpovědi, které jsou vysvětlené později v kurzu nebo se obraťte na [tým podpory přední klienta](mailto:support@frontapp.com) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/front-tutorial/tutorial_front_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/front-tutorial/tutorial_general_400.png)
    
1. Na **front-konfigurace** klikněte na tlačítko **konfigurace front-** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/front-tutorial/tutorial_front_configure.png) 

1. Přihlašování k vašemu tenantovi přední jako správce.

1. Přejděte na **nastavení (ikonu ozubeného kola v dolní části na levém bočním panelu) > Předvolby**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/front-tutorial/tutorial_front_000.png)

1. Klikněte na tlačítko **Single Sign On** odkaz.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/front-tutorial/tutorial_front_001.png)

1. Vyberte **SAML** v rozevíracím seznamu **Single Sign On**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/front-tutorial/tutorial_front_002.png)

1. V **vstupní bod** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby** z Průvodce konfigurací aplikace Azure AD.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/front-tutorial/tutorial_front_003.png)

1. Otevřete váš stažené **Certificate(Base64)** v poznámkovém bloku soubor, zkopírujte obsah ho do schránky a vložte ho do **podpisového certifikátu** textového pole.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/front-tutorial/tutorial_front_004.png)

1. Na **nastavení poskytovatele služby** části, proveďte následující kroky:

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/front-tutorial/tutorial_front_005.png)

    a. Zkopírujte hodnotu **Entity ID** a vložte ho do **identifikátor** textového pole v **přední domény a adresy URL** části webu Azure Portal.

    b. Zkopírujte hodnotu **ACS URL** a vložte ho do **adresy URL odpovědi** textového pole v **přední domény a adresy URL** části webu Azure Portal.
    
1. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/front-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/front-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/front-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/front-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-front-test-user"></a>Vytvoření zkušebního uživatele Front

V této části vytvoříte uživateli Britta Simon v popředí. Práce s [tým podpory přední klienta](mailto:support@frontapp.com) přidat uživatele na platformě Front. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu Front.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon do popředí, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **přední**.

    ![Přední odkaz v seznamu aplikací](./media/front-tutorial/tutorial_front_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Cílem této části je test vaší Azure AD SSOconfiguration pomocí přístupového panelu.

Po kliknutí na dlaždici Front na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Front. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/front-tutorial/tutorial_general_01.png
[2]: ./media/front-tutorial/tutorial_general_02.png
[3]: ./media/front-tutorial/tutorial_general_03.png
[4]: ./media/front-tutorial/tutorial_general_04.png

[100]: ./media/front-tutorial/tutorial_general_100.png

[200]: ./media/front-tutorial/tutorial_general_200.png
[201]: ./media/front-tutorial/tutorial_general_201.png
[202]: ./media/front-tutorial/tutorial_general_202.png
[203]: ./media/front-tutorial/tutorial_general_203.png

