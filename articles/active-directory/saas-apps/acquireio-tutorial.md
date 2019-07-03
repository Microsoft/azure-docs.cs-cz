---
title: 'Kurz: Integrace Azure Active Directory s AcquireIO | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75a92e78e7293316cad6e567ae49c4998299415c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544267"
---
# <a name="tutorial-integrate-acquireio-with-azure-active-directory"></a>Kurz: AcquireIO integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat AcquireIO s Azure Active Directory (Azure AD). Když integrujete AcquireIO s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k AcquireIO.
* Aby uživatelé mohli být automaticky přihlášeni k AcquireIO pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AcquireIO jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje AcquireIO **IDP** jednotné přihlašování zahájené.

## <a name="adding-acquireio-from-the-gallery"></a>Přidání AcquireIO z Galerie

Konfigurace integrace AcquireIO do služby Azure AD, budete muset přidat AcquireIO z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **AcquireIO** do vyhledávacího pole.
1. Vyberte **AcquireIO** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s AcquireIO pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v AcquireIO.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s AcquireIO, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace AcquireIO](#configure-acquireio)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele AcquireIO](#create-acquireio-test-user)**  mít protějšek B.Simon AcquireIO, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **AcquireIO** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, postupujte následovně:

    V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Hodnota není skutečný. Zobrazí se skutečná adresa URL odpovědi, který je vysvětlen později v **nakonfigurovat AcquireIO** části kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení AcquireIO** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-acquireio"></a>Konfigurace AcquireIO

1. V okně jiné webové prohlížeče Přihlaste se k AcquireIO jako správce.

2. V levé nabídce klikněte na **App Store**.

     ![Konfigurace AcquireIO](./media/acquireio-tutorial/config01.png)

3. Přejděte dolů až **služby Active Directory** a klikněte na **nainstalovat**.

    ![Konfigurace AcquireIO](./media/acquireio-tutorial/config02.png)

4. V místní službě Active Directory proveďte následující kroky:

    ![Konfigurace AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Klikněte na tlačítko **kopírování** příslušnou odpovědní adresu URL pro vaši instanci zkopírujte a vložte ji **adresy URL odpovědi** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    b. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. V poznámkovém bloku otevřete certifikát kódovaný v Base64, zkopírujte jeho obsah a vložte ji **certifikát X.509** textového pole.

    d. Klikněte na tlačítko **připojit**.

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

V této části povolíte B.Simon k udělení přístupu k AcquireIO použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **AcquireIO**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-acquireio-test-user"></a>Vytvoření AcquireIO testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k AcquireIO, musí být poskytnuty do AcquireIO. Zřizování v AcquireIO, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. V okně jiné webové prohlížeče Přihlaste se k AcquireIO jako správce.

2. V levé nabídce klikněte na **profily** a přejděte do **přidat profil**.

     ![Konfigurace AcquireIO](./media/acquireio-tutorial/config04.png)

3. Na **odběratele přidat** rozbalovací, proveďte následující kroky:

    ![Konfigurace AcquireIO](./media/acquireio-tutorial/config05.png)

    a. V **název** textové pole, zadejte jméno uživatele, jako je **B.simon**.

    b. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **B.simon@contoso.com** .

    c. Klikněte na **Submit** (Odeslat).

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice AcquireIO na přístupovém panelu, můžete by měl být automaticky přihlášeni k AcquireIO, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)