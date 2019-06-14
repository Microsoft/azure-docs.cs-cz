---
title: 'Kurz: Integrace Azure Active Directory s Syncplicity | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: c823b1b5d009d836ba3f134623a67cab0d38c180
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050320"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Kurz: Syncplicity integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Syncplicity s Azure Active Directory (Azure AD). Když integrujete Syncplicity s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Syncplicity.
* Aby uživatelé mohli být automaticky přihlášeni k Syncplicity pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Syncplicity jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Syncplicity **SP** jednotné přihlašování zahájené.

## <a name="adding-syncplicity-from-the-gallery"></a>Přidání Syncplicity z Galerie

Konfigurace integrace Syncplicity do služby Azure AD, budete muset přidat Syncplicity z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Syncplicity** do vyhledávacího pole.
1. Vyberte **Syncplicity** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD

Konfigurace a otestování jednotného přihlašování k Azure AD s Syncplicity pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Syncplicity.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Syncplicity, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování Syncplicity](#configure-syncplicity-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Syncplicity](#create-syncplicity-test-user)**  – Pokud chcete mít protějšek B.Simon Syncplicity, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Syncplicity** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.syncplicity.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Syncplicity klienta](https://www.syncplicity.com/contact-us) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení Syncplicity** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurace jednotného přihlašování Syncplicity

1. Přihlaste se k vaší **Syncplicity** tenanta.

1. V nabídce v horní části klikněte na tlačítko **správce**vyberte **nastavení**a potom klikněte na tlačítko **vlastní domény a jednotné přihlašování**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na **jednotné přihlašování (SSO)** dialogového okna stránky, proveďte následující kroky:

    ![Jednotné přihlašování \(jednotného přihlašování\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. V **vlastní domény** textového pole zadejte název vaší domény.
  
    b. Vyberte **povolené** jako **jednotné přihlašování – stav**.

    c. V **Entity Id** vložit do textového pole **identifikátor (Entity ID)** hodnotu, kterou jste použili v **základní konfiguraci SAML** na webu Azure Portal.

    d. V **přihlašovací adresa URL stránky** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    e. V **adresy URL odhlašovací stránky** vložit do textového pole **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    f. V **certifikát poskytovatele Identity**, klikněte na tlačítko **zvolte soubor**a pak nahrajte certifikát, který jste si stáhli z portálu Azure portal.

    g. Klikněte na tlačítko **ULOŽTE změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon k udělení přístupu k Syncplicity použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Syncplicity**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-syncplicity-test-user"></a>Vytvoření Syncplicity testovacího uživatele

Pro uživatele AAD, bude moct přihlásit musí být zřízená Syncplicity aplikace. Tato část popisuje, jak v Syncplicity vytvořte uživatelské účty AAD.

**Ke zřízení uživatelského účtu pro Syncplicity, proveďte následující kroky:**

1. Přihlaste se k vaší **Syncplicity** tenanta (například: `https://company.Syncplicity.com`).

1. Klikněte na tlačítko **správce** a vyberte **uživatelské účty** a potom klikněte na tlačítko **uživatele přidat**.

    ![Správa uživatelů](./media/syncplicity-tutorial/ic769764.png "Správa uživatelů")

1. Typ **e-mailové adresy** účtu služby Azure AD, které chcete zřídit, vyberte **uživatele** jako **Role**a potom klikněte na tlačítko **Další**.

    ![Informace o účtu](./media/syncplicity-tutorial/ic769765.png "informace o účtu")

    > [!NOTE]
    > Držitel účtu AAD získá e-mailu včetně odkazu na potvrzení a aktivovat účet.

1. Vyberte skupinu ve vaší společnosti, nový uživatel by měl stane členem, a pak klikněte na **Další**.

    ![Členství ve skupině](./media/syncplicity-tutorial/ic769772.png "členství ve skupině")

    > [!NOTE]
    > Pokud nejsou k dispozici žádné skupiny uvedeny, klikněte na tlačítko **Další**.

1. Vyberte složky, které chcete umístit pod kontrolou společnosti Syncplicity na počítači uživatele a potom klikněte na **Další**.

    ![Složky Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity složek")

> [!NOTE]
> Můžete použít jakékoli jiné Syncplicity uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Syncplicity uživatelským účtům, zřídit AAD.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Syncplicity na přístupovém panelu, můžete by měl být automaticky přihlášeni k Syncplicity, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)