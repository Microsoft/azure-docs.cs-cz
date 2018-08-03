---
title: 'Kurz: Integrace Azure Active Directory se službou Coupa | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: 4bf40f76f5a8f788305b4dc9f91523f53fb59acf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448080"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Kurz: Integrace Azure Active Directory se službou Coupa

V tomto kurzu se dozvíte, jak integrovat Coupa s Azure Active Directory (Azure AD).

Coupa integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Coupa.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Coupa (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Coupa, potřebujete následující položky:

- S předplatným služby Azure AD
- Coupa jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Coupa z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-coupa-from-the-gallery"></a>Přidání Coupa z Galerie
Konfigurace integrace Coupa do služby Azure AD, budete muset přidat Coupa z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Coupa z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Coupa**vyberte **Coupa** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Coupa v seznamu výsledků](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Coupa podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Coupa je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Coupa potřeba navázat.

V Coupa, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Coupa, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Coupa](#create-a-coupa-test-user)**  – Pokud chcete mít protějšek Britta Simon Coupa, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Coupa.

**Ke konfiguraci Azure AD jednotné přihlašování s Coupa, proveďte následující kroky:**

1. Na webu Azure Portal na **Coupa** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

1. Na **Coupa domény a adresy URL** části, proveďte následující kroky:

    ![Coupa domény a adresy URL jednotného přihlašování – informace](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Coupa klienta](https://success.coupa.com/Support/Contact_Us?) tuto výhodu získáte.

    b. V **identifikátor** textového pole zadejte adresu URL:

    | Prostředí  | zprostředkovatele identity |
    |:-------------|----|
    | Izolovaný prostor | `devsso35.coupahost.com`|
    | Výroba | `prdsso40.coupahost.com`|
    | | |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL:

    | Prostředí | zprostředkovatele identity |
    |------------- |----|
    | Izolovaný prostor | `https://devsso35.coupahost.com/sp/ACS.saml2`|
    | Výroba | `https://prdsso40.coupahost.com/sp/ACS.saml2`|
    | | |

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/coupa-tutorial/tutorial_general_400.png)

1. Přihlaste se k webu společnosti Coupa jako správce.

1. Přejděte na **nastavení \> řízení zabezpečení**.

   ![Kontrolní mechanismy zabezpečení](./media/coupa-tutorial/ic791900.png "kontrolních mechanismů pro zabezpečení")

1. V **přihlásit pomocí přihlašovacích údajů Coupa** části, proveďte následující kroky:

    ![Metadata Coupa SP](./media/coupa-tutorial/ic791901.png "Coupa SP metadat")

    a. Vyberte **Přihlaste se pomocí SAML**.

    b. Klikněte na tlačítko **Procházet** nahrát metadat stáhnout z webu Azure portal.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/coupa-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/coupa-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/coupa-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/coupa-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-coupa-test-user"></a>Vytvoření zkušebního uživatele Coupa

Chcete-li povolit uživatele Azure AD k přihlášení do Coupa, musí být poskytnuty do Coupa.  

* V případě Coupa zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Coupa** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **uživatelé**.

   ![Uživatelé](./media/coupa-tutorial/ic791908.png "uživatelů")

1. Klikněte na možnost **Vytvořit**.

   ![Vytvoření uživatelů](./media/coupa-tutorial/ic791909.png "vytvořit uživatele")

1. V **uživatel vytvořit** části, proveďte následující kroky:

   ![Podrobnosti o uživateli](./media/coupa-tutorial/ic791910.png "podrobnosti o uživateli")

   a. Typ **přihlášení**, **křestní jméno**, **příjmení**, **ID přihlášení**, **e-mailu** atributy platný účet služby Azure Active Directory, které chcete zřídit do související textových polí.

   b. Klikněte na možnost **Vytvořit**.

   >[!NOTE]
   >Držitel účtu Azure Active Directory se pošle e-mail s odkazem pro potvrzení účtu, pak se změní na aktivní.
   >

>[!NOTE]
>Můžete použít jakékoli jiné Coupa uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Coupa uživatelským účtům, zřídit AAD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Coupa použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Coupa Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **Coupa**.

    ![Odkaz Coupa v seznamu aplikací](./media/coupa-tutorial/tutorial_coupa_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Coupa na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Coupa.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/coupa-tutorial/tutorial_general_01.png
[2]: ./media/coupa-tutorial/tutorial_general_02.png
[3]: ./media/coupa-tutorial/tutorial_general_03.png
[4]: ./media/coupa-tutorial/tutorial_general_04.png

[100]: ./media/coupa-tutorial/tutorial_general_100.png

[200]: ./media/coupa-tutorial/tutorial_general_200.png
[201]: ./media/coupa-tutorial/tutorial_general_201.png
[202]: ./media/coupa-tutorial/tutorial_general_202.png
[203]: ./media/coupa-tutorial/tutorial_general_203.png
