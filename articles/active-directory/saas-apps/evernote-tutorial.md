---
title: 'Kurz: Integrace Azure Active Directory s Evernote | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 8ba80e113de8ea6754d8d2d6446fb26498904e12
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816802"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Kurz: Integrace Azure Active Directory s Evernote

V tomto kurzu se dozvíte, jak integrovat Evernote s Azure Active Directory (Azure AD).

Evernote integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Evernote.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Evernote (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Evernote, potřebujete následující položky:

- Předplatné Azure AD
- Evernote jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Evernote z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-evernote-from-the-gallery"></a>Přidání Evernote z Galerie
Konfigurace integrace Evernote do služby Azure AD, budete muset přidat Evernote z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Evernote z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Evernote**vyberte **Evernote** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Evernote v seznamu výsledků](./media/evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Evernote podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Evernote je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Evernote potřeba navázat.

V Evernote, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Evernote, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele Evernote](#create-an-evernote-test-user)**  – Pokud chcete mít protějšek Britta Simon Evernote, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Evernote.

**Ke konfiguraci Azure AD jednotné přihlašování s Evernote, proveďte následující kroky:**

1. Na webu Azure Portal na **Evernote** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/evernote-tutorial/tutorial_evernote_samlbase.png)

1. Na **Evernote domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![Evernote domény a adresy URL jednotného přihlašování – informace](./media/evernote-tutorial/tutorial_evernote_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://www.evernote.com/saml2`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Evernote domény a adresy URL jednotného přihlašování – informace](./media/evernote-tutorial/tutorial_evernote_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://www.evernote.com/Login.action`   

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/evernote-tutorial/tutorial_evernote_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/evernote-tutorial/tutorial_general_400.png)

1. Na **Evernote konfigurace** klikněte na tlačítko **nakonfigurovat Evernote** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Evernote](./media/evernote-tutorial/tutorial_evernote_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Evernote.

1. Přejděte na **"konzoly pro správu.**

    ![Konzoly pro správu](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Z **'Konzoly pro správu'**, přejděte na stránku **"Zabezpečení"** a vyberte **"Single Sign-On"**

    ![Nastavení jednotného přihlašování](./media/evernote-tutorial/tutorial_evernote_sso.png)

1. Nakonfigurujte následující hodnoty:

    ![Nastavení certifikátu.](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Povolení jednotného přihlašování:** Ve výchozím nastavení je povoleno jednotné přihlašování (klikněte na tlačítko **zakázat Single Sign-on** odstraníte požadavek jednotné přihlašování)

    b. Vložit **SAML jednotného přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal do **URL žádosti HTTP SAML** textového pole.

    c. Otevřete stažený certifikát ze služby Azure AD v Poznámkový blok a zkopírujte obsah, včetně "BEGIN" a "END osvědčení" a vložte ho do **certifikát X.509** textového pole. 

    d.Click **uložit změny**

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/evernote-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/evernote-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/evernote-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/evernote-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-evernote-test-user"></a>Vytvořit testovacího uživatele Evernote

Chcete-li povolit uživatele Azure AD k přihlášení do Evernote, musí být poskytnuty do Evernote.  
V případě Evernote zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se na web společnosti Evernote jako správce.

1. Klikněte na tlačítko **'Konzoly pro správu'**.

    ![Konzoly pro správu](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Z **'Konzoly pro správu'**, přejděte na stránku **'Přidat uživatele'**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

1. **Přidat členy týmu** v **e-mailu** textového pole zadejte e-mailovou adresu uživatelského účtu a klikněte na tlačítko **pozvat.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
1. Po odeslání pozvánky držitel účtu Azure Active Directory obdrží e-mailu k přijetí pozvánky.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Evernote použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Evernote Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Evernote**.

    ![Odkaz Evernote v seznamu aplikací](./media/evernote-tutorial/tutorial_evernote_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Evernote na přístupovém panelu, vám by měl získat přihlášení k aplikaci Evernote. Budete být přihlášení jako účet, ale pak nutné se přihlásit pomocí osobního účtu organizace. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/evernote-tutorial/tutorial_general_01.png
[2]: ./media/evernote-tutorial/tutorial_general_02.png
[3]: ./media/evernote-tutorial/tutorial_general_03.png
[4]: ./media/evernote-tutorial/tutorial_general_04.png

[100]: ./media/evernote-tutorial/tutorial_general_100.png

[200]: ./media/evernote-tutorial/tutorial_general_200.png
[201]: ./media/evernote-tutorial/tutorial_general_201.png
[202]: ./media/evernote-tutorial/tutorial_general_202.png
[203]: ./media/evernote-tutorial/tutorial_general_203.png

