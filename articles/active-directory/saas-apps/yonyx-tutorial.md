---
title: 'Kurz: Integrace Azure Active Directory s interaktivní vodítka Yonyx | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Yonyx interaktivní průvodce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d08b1f3889136fadee68b3aa95d879b0495bddb1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198403"
---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Kurz: Integrace Azure Active Directory s Yonyx interaktivní vodítka

V tomto kurzu se dozvíte, jak integrovat Yonyx interaktivní Průvodce službou Azure Active Directory (Azure AD).

Interaktivní Průvodce Yonyx integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k Yonyx interaktivní vodítka
- Uživatele, aby automaticky získat přihlášení k interaktivní vodítka Yonyx (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Yonyx interaktivní vodítka, potřebujete následující položky:

- Předplatné Azure AD
- Interaktivní Průvodce Yonyx jedno přihlášení povolený předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Yonyx interaktivní vodítek z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Přidání Yonyx interaktivní vodítek z Galerie
Ke konfiguraci integrace interaktivní vodítka Yonyx do služby Azure AD, budete muset přidat z Galerie Yonyx interaktivní Průvodce pro váš seznam spravovaných aplikací SaaS.

**Přidat vodítka interaktivní Yonyx z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **interaktivní vodítka Yonyx**vyberte **interaktivní vodítka Yonyx** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Interaktivní Průvodce Yonyx v seznamu výsledků](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí interaktivního vodítka Yonyx podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v příručkách pro interaktivní Yonyx je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v příručkách pro interaktivní Yonyx potřeba navázat.

V příručkách pro interaktivní Yonyx, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Yonyx interaktivní vodítka, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Yonyx interaktivní vodítka](#create-a-yonyx-interactive-guides-test-user)**  – Pokud chcete mít protějšek Britta Simon Yonyx interaktivní průvodce, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Yonyx interaktivní vodítka.

**Ke konfiguraci Azure AD jednotné přihlašování s Yonyx interaktivní vodítka, proveďte následující kroky:**

1. Na webu Azure Portal na **interaktivní vodítka Yonyx** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_samlbase.png)

1. Na **Yonyx interaktivní vodítka domény a adresy URL** části, proveďte následující kroky:

    ![Yonyx interaktivní vodítka domény a adresy URL jednotného přihlašování – informace](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.yonyx.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Yonyx interaktivní příručky klienta](mailto:support@yonyx.com) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/yonyx-tutorial/tutorial_general_400.png)

1. Na **Yonyx interaktivní Průvodce konfigurací** klikněte na tlačítko **konfigurace interaktivní vodítka Yonyx** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Yonyx interaktivní vodítka](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Yonyx interaktivní průvodce** straně, je nutné odeslat na stažený **Certificate(Base64)**, **odhlašování URL**, **jeden SAML Přihlašovací adresa URL služby** **SAML Entity ID** k [tým podpory Yonyx interaktivní vodítka](mailto:support@yonyx.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

  ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/yonyx-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/yonyx-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Tlačítko Přidat](./media/yonyx-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/yonyx-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-yonyx-interactive-guides-test-user"></a>Vytvoření zkušebního uživatele Yonyx interaktivní vodítka

Cílem této části je vytvořte uživatele Britta Simon v Yonyx interaktivní vodítka. Interaktivní Průvodce Yonyx podporuje just-in-time zřizování, což je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Yonyx interaktivní vodítka, pokud ještě neexistuje.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat tým podpory interaktivní vodítka Yonyx prostřednictvím <mailto:support@yonyx.com>. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Yonyx interaktivní vodítka.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Yonyx interaktivní vodítka, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **interaktivní vodítka Yonyx**.

    ![Interaktivní Průvodce Yonyx odkaz v seznamu aplikací](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Yonyx interaktivní vodítka na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Yonyx interaktivní vodítka.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yonyx-tutorial/tutorial_general_01.png
[2]: ./media/yonyx-tutorial/tutorial_general_02.png
[3]: ./media/yonyx-tutorial/tutorial_general_03.png
[4]: ./media/yonyx-tutorial/tutorial_general_04.png

[100]: ./media/yonyx-tutorial/tutorial_general_100.png

[200]: ./media/yonyx-tutorial/tutorial_general_200.png
[201]: ./media/yonyx-tutorial/tutorial_general_201.png
[202]: ./media/yonyx-tutorial/tutorial_general_202.png
[203]: ./media/yonyx-tutorial/tutorial_general_203.png

