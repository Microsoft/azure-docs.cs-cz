---
title: 'Kurz: Integrace Azure Active Directory s QPrism | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 286af8b927f488acaf2877e753f6e4689c4b000f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199455"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Kurz: Integrace Azure Active Directory s QPrism

V tomto kurzu se dozvíte, jak integrovat QPrism s Azure Active Directory (Azure AD).

QPrism integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k QPrism.
- Můžete povolit uživatelům, aby automaticky získat přihlášený k QPrism (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s QPrism, potřebujete následující položky:

- Předplatné Azure AD
- QPrism jednotného přihlašování povolená předplatného

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání QPrism z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-qprism-from-the-gallery"></a>Přidání QPrism z Galerie
Konfigurace integrace QPrism do služby Azure AD, budete muset přidat QPrism z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat QPrism z galerie:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, v horní části dialogového okna, **novou aplikaci**.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **QPrism**a vyberte **QPrism** z panelu výsledek. Pak klikněte na tlačítko **přidat** pro přidání aplikace.

    ![QPrism v seznamu výsledků](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s QPrism podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo tento uživatel protějšky v QPrism je pro uživatele ve službě Azure AD. Jinými slovy musí být propojená vztah mezi uživatele služby Azure AD a souvisejících uživatelem v QPrism.

K navázání tohoto vztahu v QPrism, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno**.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s QPrism, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvoření zkušebního uživatele QPrism](#create-a-qprism-test-user) mít protějšek Britta Simon QPrism, který je spojen s Azure AD reprezentace uživatele.
1. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#test-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci QPrism.

1. Na webu Azure Portal na **QPrism** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogu **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

1. V **QPrism domény a adresy URL** části, postupujte takto:

    ![QPrism domény a adresy URL jednotného přihlašování – informace](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, která používá následující vzorec: `https://<customer domain>.qmyzone.com/login`

    b. V **identifikátor** textové pole, zadejte adresu URL, která používá následující vzorec: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL přihlašování. Kontakt [tým podpory QPrism klienta](mailto:qsupport-ce@quatrro.com) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

     ![Odkaz ke stažení certifikátu](./media/qprism-tutorial/tutorial_qprism_certificate.png)

1. Vyberte **Uložit**.

    ![Konfigurace jednotného přihlašování tlačítko Uložit](./media/qprism-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **QPrism** straně, je nutné odeslat **adresa Url federačních metadat aplikace** k [tým podpory QPrism](mailto:qsupport-ce@quatrro.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD:**

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/qprism-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/qprism-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, v horní části **všichni uživatelé** dialogu **přidat**.

    ![Tlačítko Přidat](./media/qprism-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, postupujte takto:

    ![Dialogové okno uživatele](./media/qprism-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-qprism-test-user"></a>Vytvoření zkušebního uživatele QPrism

V této části vytvoříte uživatele v QPrism jako Britta Simon. Práce s [tým podpory QPrism](mailto:qsupport-ce@quatrro.com) přidat uživatele na platformě QPrism. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k QPrism použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Chcete-li přiřadit Britta Simon QPrism:**

1. Na webu Azure Portal otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **QPrism**.

    ![Odkaz QPrism v seznamu aplikací](./media/qprism-tutorial/tutorial_qprism_app.png)  

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **Přidat**. Potom v části **přidat přiřazení**vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu.

1. Na **uživatelů a skupin** dialogu **vyberte**.

1. V části **přidat přiřazení**vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Na přístupovém panelu vyberete dlaždici QPrism vám by měl získat automaticky přihlášený k QPrism aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

