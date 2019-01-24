---
title: 'Kurz: Integrace Azure Active Directory s Getabstract | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Getabstract.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b63d048-b529-4fad-9e90-f244323409dd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 11515f74ae95cc317fe27de323da2de6c46c163c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825098"
---
# <a name="tutorial-azure-active-directory-integration-with-getabstract"></a>Kurz: Integrace Azure Active Directory s Getabstract

V tomto kurzu se dozvíte, jak integrovat Getabstract s Azure Active Directory (Azure AD).

Getabstract integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Getabstract.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Getabstract (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Getabstract, potřebujete následující položky:

- Předplatné Azure AD
- Getabstract jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Getabstract z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-getabstract-from-the-gallery"></a>Přidání Getabstract z Galerie
Konfigurace integrace Getabstract do služby Azure AD, budete muset přidat Getabstract z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Getabstract z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Getabstract**vyberte **Getabstract** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Getabstract v seznamu výsledků](./media/getabstract-tutorial/tutorial_getabstract_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Getabstract podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Getabstract je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Getabstract potřeba navázat.

V Getabstract, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Getabstract, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Getabstract](#create-a-getabstract-test-user)**  – Pokud chcete mít protějšek Britta Simon Getabstract, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Getabstract.

**Ke konfiguraci Azure AD jednotné přihlašování s Getabstract, proveďte následující kroky:**

1. Na webu Azure Portal na **Getabstract** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/getabstract-tutorial/tutorial_getabstract_samlbase.png)

1. Na **Getabstract domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Getabstract domény a adresy URL jednotného přihlašování – informace](./media/getabstract-tutorial/tutorial_getabstract_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL:

    Pro fázi/pre_production: `https://int.getabstract.com`

    Pro produkční prostředí: `https://www.getabstract.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL:
    
    Pro fázi/pre_production: `https://int.getabstract.com/ACS.do`
    
    Pro produkční prostředí: `https://www.getabstract.com/ACS.do`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Getabstract domény a adresy URL jednotného přihlašování – informace](./media/getabstract-tutorial/tutorial_getabstract_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    
    Pro fázi/pre_production: `https://int.getabstract.com/portal/<org_username>`
    
    Pro produkční prostředí: `https://www.getabstract.com/portal/<org_username>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Getabstract klienta](https://www.getabstract.com/en/contact) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/getabstract-tutorial/tutorial_getabstract_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/getabstract-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **Getabstract** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Getabstract](https://www.getabstract.com/en/contact). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/getabstract-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/getabstract-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/getabstract-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/getabstract-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-getabstract-test-user"></a>Vytvoření zkušebního uživatele Getabstract

Cílem této části je vytvořte uživatele Britta Simon v Getabstract. Getabstract podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Getabstract, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [Getabstract tým podpory](https://www.getabstract.com/en/contact)


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Getabstract použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Getabstract Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Getabstract**.

    ![Odkaz Getabstract v seznamu aplikací](./media/getabstract-tutorial/tutorial_getabstract_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Getabstract na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Getabstract.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/getabstract-tutorial/tutorial_general_01.png
[2]: ./media/getabstract-tutorial/tutorial_general_02.png
[3]: ./media/getabstract-tutorial/tutorial_general_03.png
[4]: ./media/getabstract-tutorial/tutorial_general_04.png

[100]: ./media/getabstract-tutorial/tutorial_general_100.png

[200]: ./media/getabstract-tutorial/tutorial_general_200.png
[201]: ./media/getabstract-tutorial/tutorial_general_201.png
[202]: ./media/getabstract-tutorial/tutorial_general_202.png
[203]: ./media/getabstract-tutorial/tutorial_general_203.png

