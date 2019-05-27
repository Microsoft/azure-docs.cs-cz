---
title: 'Kurz: Integrace s Azure Active Directory se Zscalerem privátní přístup (ZPA) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem privátní přístup (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984498a2b9d4d72ee6bb6b9f0a9e62636bf870bf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226443"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory Zscalerem privátní přístup (ZPA)

V tomto kurzu se dozvíte, jak integrovat Zscalerem privátní přístup (ZPA) se službou Azure Active Directory (Azure AD). Když integrujete Zscalerem privátní přístup (ZPA) s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Zscalerem privátní přístup (ZPA).
* Aby uživatelé mohli být automaticky přihlášeni k Zscalerem privátní přístup (ZPA) s jejich účty Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zscalerem privátní přístup (ZPA) jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Zscalerem privátní přístup (ZPA) podporuje **SP** jednotné přihlašování zahájené.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Přidání Zscalerem privátní přístup (ZPA) z Galerie

Konfigurace integrace nástroje Zscalerem privátní přístup (ZPA) do služby Azure AD, budete muset přidat Zscalerem privátní přístup (ZPA) z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Zscalerem privátní přístup (ZPA)** do vyhledávacího pole.
1. Vyberte **Zscalerem privátní přístup (ZPA)** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD se Zscalerem privátní přístup (ZPA) pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Zscalerem privátní přístup (ZPA).

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD se Zscalerem privátní přístup (ZPA), proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zscalerem privátní přístup (ZPA)](#configure-zscaler-private-access-zpa)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Zscalerem privátní přístup (ZPA)](#create-zscaler-private-access-zpa-test-user)**  mít protějšek Britta Simon v Zscalerem privátní přístup (ZPA), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Zscalerem privátní přístup (ZPA)** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    1. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > **Přihlašovací adresa URL** není skutečnou hodnotu. Skutečná adresa URL přihlašování zaktualizujte příslušnou hodnotu. Kontakt [tým podpory Zscalerem privátní přístup (ZPA) klienta](https://help.zscaler.com/zpa-submit-ticket) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **kód XML metadat federace** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Na **nastavit až Zscalerem privátní přístup (ZPA)** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurace přístupu Zscalerem privátní (ZPA)

1. K automatizaci konfigurace v rámci Zscalerem privátní přístup (ZPA), je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Zscalerem privátní přístup (ZPA)** nasměruje na Zscalerem privátní přístup (ZPA) aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do Zscalerem privátní přístup (ZPA). Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 6.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit Zscalerem privátní přístup (ZPA) ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Zscalerem privátní přístup (ZPA) jako správce a proveďte následující kroky:

4. V levé nabídce klikněte na **správu** a přejděte do **ověřování** klikněte na **konfigurace zprostředkovatele identity**.

    ![Správa Zscalerem privátní přístup správce](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. V pravém horním rohu, klikněte na tlačítko **přidat konfigurace zprostředkovatele identity**. 

    ![Privátní přístup správce idp Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Na **přidat konfigurace zprostředkovatele identity** stránku, proveďte následující kroky:
 
    ![Vyberte Zscalerem privátní přístup správce](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klikněte na tlačítko **vybrat soubor** nahrát stažený soubor metadat z Azure AD **nahrát soubor metadat zprostředkovatele identity** pole.

    b. Načte **metadat zprostředkovatele identity** ze služby Azure AD a naplní všechny informace o polích, jak je znázorněno níže.

    ![Konfigurace Zscalerem privátní přístup správce](./media/zscalerprivateaccess-tutorial/config.png)

    c. Zadejte název domény z **domén** pole.
    
    d. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem privátní přístup (ZPA).

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Zscalerem privátní přístup (ZPA)** .
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Vytvořit testovacího uživatele Zscalerem privátní přístup (ZPA)

V této části vytvoříte uživateli Britta Simon v Zscalerem privátní přístup (ZPA). Spojte se prosím s [tým podpory Zscalerem privátní přístup (ZPA)](https://help.zscaler.com/zpa-submit-ticket) přidat uživatele na platformě Zscalerem privátní přístup (ZPA).

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Zscalerem privátní přístup (ZPA) na přístupovém panelu, vám by měl být automaticky přihlášeni k Zscalerem privátní přístup (ZPA) u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)