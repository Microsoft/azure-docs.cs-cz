---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s využitím nálady | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a náladou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.openlocfilehash: b1f8fb4c6586281352b6da05978c6ca4dac23ce5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92504792"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím nálady

V tomto kurzu se dozvíte, jak integrovat náladu s Azure Active Directory (Azure AD). Když integrujete s Azure AD s jednou náladou, můžete:

* Řízení ve službě Azure AD, která má přístup k náladě.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k náladě s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povolenou jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Po náladě podporujeme **aktualizace SP a IDP** .
* Nálada podporuje zřizování uživatelů **jenom včas** .

## <a name="adding-supermood-from-the-gallery"></a>Přidání nálady z Galerie

Pokud chcete nakonfigurovat integraci nálady do služby Azure AD, musíte přidat do seznamu spravovaných aplikací SaaS další náladu z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** **Zadejte do** vyhledávacího pole text.
1. Na panelu výsledků vyberte **nálada** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Konfigurace a testování jednotného přihlašování Azure AD pro náladu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím nálady pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci nálady.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím nálady, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování (SSO](#configure-supermood-sso)** ) – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte si testovacího uživatele](#create-supermood-test-user)** s ochranou nálady, abyste měli protějšek B. Simon v rámci nálady, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací na základě **nálady** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Ověřte **nastavení dalších adres URL**.
    
    b. Do textového pole **stav přenosu** zadejte adresu URL: `https://supermood.co/auth/sso/saml20`

1. Klikněte na **nastavit další adresy URL** a proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://supermood.co/app/#!/loginv2`

1. Klikněte na **Uložit**.

1. Aplikace s funkcí $ nálady očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše uvedeného platí, že aplikace s větší náladou očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ---------------| ------|
    | firstName | User. křestní jméno |
    | lastName | User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k náladě.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **nálada**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-supermood-sso"></a>Konfigurace jednotného přihlašování SSO

1. Jako správce zabezpečení otevřete panel pro správu Supermood.co.

1. Klikněte na **můj účet** (vlevo dole) a **jednotné přihlašování (SSO)**.

    ![Certifikát Single](./media/supermood-tutorial/tutorial_supermood_single.png)

1. V **konfiguracích saml 2,0** klikněte na **přidat konfiguraci SAML 2,0 pro e-mailovou doménu**.

    ![Certifikát – přidání](./media/supermood-tutorial/tutorial_supermood_add.png)

1. V případě **Přidání konfigurace SAML 2,0 pro e-mailovou doménu**. v části proveďte následující kroky:

    ![Certifikát SAML](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Do textového pole **doména v e-mailové doméně pro tohoto zprostředkovatele identity** zadejte svoji doménu.

    b. Do textového pole **použít adresu URL metadat** vložte **adresu URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **Přidat**.

### <a name="create-supermood-test-user"></a>Vytvořit testovacího uživatele s více náladami

V této části se uživatel s názvem Britta Simon vytvoří v rámci nálady. Tato nálada podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povoleno. V této části není žádná položka akce. Pokud uživatel už v jedné náladě neexistuje, vytvoří se po ověření nový. Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory s více náladami](mailto:hello@supermood.fr).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici o náladě na přístupovém panelu, měli byste být automaticky přihlášeni k náladě, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si náladu s Azure AD](https://aad.portal.azure.com/)