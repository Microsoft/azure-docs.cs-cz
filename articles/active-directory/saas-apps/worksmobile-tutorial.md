---
title: 'Kurz: Integrace Azure Active Directory s řádku FUNGUJE | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a řádku PRACUJE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jeedes
ms.openlocfilehash: 907cec2784b4ad22555f6b29efb6d670ce7d48d0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443643"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Kurz: Integrace Azure Active Directory s řádku FUNGUJE

V tomto kurzu se dozvíte, jak integrovat řádku FUNGUJE s Azure Active Directory (Azure AD).

Integrace řádku FUNGUJE s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k řádku FUNGUJE.
- Uživatele, aby automaticky získat přihlášení k řádku FUNGUJE (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s řádku FUNGUJE, potřebujete následující položky:

- S předplatným služby Azure AD
- ŘÁDKU FUNGUJE jednotné přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání řádku FUNGUJE z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-line-works-from-the-gallery"></a>Přidání řádku FUNGUJE z Galerie
Konfigurace integrace FUNGUJE řádku do služby Azure AD, budete muset přidat řádek FUNGUJE z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat řádek FUNGUJE z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **řádku FUNGUJE**vyberte **řádku FUNGUJE** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ŘÁDEK FUNGUJE v seznamu výsledků](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí řádku FUNGUJE na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v řádku FUNGUJE je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v řádku FUNGUJE.

V řádku FUNGUJE, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Ke konfiguraci a testování Azure AD jednotné přihlašování FUNGUJE řádku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele řádku FUNGUJE](#create-a-line-works-test-user)**  – Pokud chcete mít protějšek Britta Simon v řádku FUNGUJE, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci FUNGUJE řádku.

**Ke konfiguraci Azure AD jednotné přihlašování s řádku FUNGUJE, proveďte následující kroky:**

1. Na webu Azure Portal na **řádku FUNGUJE** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/worksmobile-tutorial/tutorial_lineworks_samlbase.png)

1. Na **řádku FUNGUJE domény a adresy URL** části, proveďte následující kroky:

    ![ŘÁDEK FUNGUJE domény a adresy URL jednotného přihlašování – informace](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://auth.worksmobile.com/d/login/{domain}/?userId={ID@domain}`

    b. V **identifikátor** textového pole zadejte hodnotu: `worksmobile.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta FUNGUJE řádku](mailto:dl_ssoinfo@worksmobile.com) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/worksmobile-tutorial/tutorial_lineworks_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/worksmobile-tutorial/tutorial_general_400.png)

1. Na **konfigurace FUNGUJE řádku** klikněte na tlačítko **konfigurace FUNGUJE řádku** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace FUNGUJE řádku](./media/worksmobile-tutorial/tutorial_lineworks_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **řádku FUNGUJE** straně, je nutné odeslat na stažený **certifikátů, soubor, adresa URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [podporují řádku FUNGUJE tým](mailto:dl_ssoinfo@worksmobile.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/worksmobile-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/worksmobile-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/worksmobile-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/worksmobile-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-line-works-test-user"></a>Vytvoření zkušebního uživatele řádku FUNGUJE

V této části vytvoříte uživatele v řádku FUNGUJE jako Britta Simon. Spojte se prosím s [řádku PRACUJE tým podpory](mailto:dl_ssoinfo@worksmobile.com) přidat uživatele na platformě FUNGUJE řádku.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k řádku FUNGUJE.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit řádku FUNGUJE, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **řádku FUNGUJE**.

    ![Odkaz FUNGUJE řádek v seznamu aplikací](./media/worksmobile-tutorial/tutorial_lineworks_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici řádku FUNGUJE na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci řádku FUNGUJE.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/lineworks-tutorial/tutorial_general_01.png
[2]: ./media/lineworks-tutorial/tutorial_general_02.png
[3]: ./media/lineworks-tutorial/tutorial_general_03.png
[4]: ./media/lineworks-tutorial/tutorial_general_04.png

[100]: ./media/lineworks-tutorial/tutorial_general_100.png

[200]: ./media/lineworks-tutorial/tutorial_general_200.png
[201]: ./media/lineworks-tutorial/tutorial_general_201.png
[202]: ./media/lineworks-tutorial/tutorial_general_202.png
[203]: ./media/lineworks-tutorial/tutorial_general_203.png

