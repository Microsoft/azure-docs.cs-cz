---
title: 'Kurz: Integrace Azure Active Directory s Druva | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a platformy Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf359f38bd7032b4de48a17b91ba1c10b165c91
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807775"
---
# <a name="tutorial-integrate-druva-with-azure-active-directory"></a>Kurz: Integrace platformy Druva s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat platformy Druva s Azure Active Directory (Azure AD). Pokud je Druva integrovat s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Druva.
* Aby uživatelé mohli být automaticky přihlášeni k Druva pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Platformy Druva jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje platformy Druva **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-druva-from-the-gallery"></a>Přidání Druva z Galerie

Konfigurace integrace platformy Druva do služby Azure AD, budete muset přidat Druva z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **platformy Druva** do vyhledávacího pole.
1. Vyberte **platformy Druva** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Druva pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Druva.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí platformy Druva, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování Druva](#configure-druva-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Druva](#create-druva-test-user)**  – Pokud chcete mít protějšek Britta Simon Druva, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **platformy Druva** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrované se službou Azure.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://login.druva.com/login`

1. Aplikace platformy Druva očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

1. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Name | Zdrojový atribut|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Zadejte token vygenerovanou hodnotu |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení platformy Druva** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-druva-sso"></a>Konfigurace platformy Druva jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Druva jako správce.

1. Přejděte na **spravovat \> nastavení**.

    ![Nastavení](./media/druva-tutorial/ic795091.png "nastavení")

1. V dialogovém okně Nastavení jednotného přihlašování proveďte následující kroky:

    ![Jednotné přihlašování – nastavení](./media/druva-tutorial/ic795092.png "jednotné přihlašování – nastavení")

    a. V **přihlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. V **odhlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal

    c. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **ID zprostředkovatele certifikátu** textové pole

    d. Chcete-li otevřít **nastavení** klikněte na **Uložit**.

1. Na **nastavení** klikněte na **vygenerovat Token jednotného přihlašování**.

    ![Nastavení](./media/druva-tutorial/ic795093.png "nastavení")

1. Na **jednotné přihlašování ověřovací Token** dialogového okna, proveďte následující kroky:

    ![Token jednotného přihlašování](./media/druva-tutorial/ic795094.png "Token jednotného přihlašování")

    a. Klikněte na tlačítko **kopírování**, vložit zkopírovat hodnotu v **hodnotu** textového pole v **přidat atribut** části webu Azure Portal.

    b. Klikněte na **Zavřít**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá Britta Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu platformy Druva.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **platformy Druva**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-druva-test-user"></a>Vytvoření platformy Druva testovacího uživatele

Chcete-li povolit Azure AD uživatelům umožní přihlásit k Druva, musí být poskytnuty do platformy Druva. V případě platformy Druva zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **platformy Druva** společnosti serveru jako správce.

1. Přejděte na **spravovat \> uživatelé**.

    ![Správa uživatelů](./media/druva-tutorial/ic795097.png "Správa uživatelů")

1. Klikněte na tlačítko **vytvořit nový**.

    ![Správa uživatelů](./media/druva-tutorial/ic795098.png "Správa uživatelů")

1. V dialogovém okně Vytvořit nové uživatele proveďte následující kroky:

    ![Create NewUser](./media/druva-tutorial/ic795099.png "Create NewUser")

    a. V **e-mailová adresa** textového pole zadejte e-mailu uživatele, jako je **brittasimon\@contoso.com**.

    b. V **název** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.

    c. Klikněte na tlačítko **vytvořit uživatele**.

> [!NOTE]
> Můžete použít jakékoli jiné platformy Druva uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Druva zřízení uživatelských účtů služby Azure AD.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Druva na přístupovém panelu, můžete by měl být automaticky přihlášeni do platformy Druva, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
