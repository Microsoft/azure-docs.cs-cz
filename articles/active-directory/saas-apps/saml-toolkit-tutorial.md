---
title: 'Kurz: Integrace Azure Active Directory s Azure AD SAML Toolkit | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091940"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Kurz: Integrace nástrojů Azure AD SAML s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Azure AD SAML Toolkit s Azure Active Directory (Azure AD). Při integraci Azure AD SAML Toolkit s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Azure AD SAML Toolkit.
* Aby uživatelé mohli být automaticky přihlášeni k Azure AD SAML Toolkit pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Azure AD SAML Toolkit jednotného přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Azure AD SAML Toolkit **SP** jednotné přihlašování zahájené.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Přidání z Galerie Azure AD SAML Toolkit

Konfigurace integrace Azure AD SAML Toolkit do služby Azure AD, budete muset přidat z Galerie Azure AD SAML Toolkit na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Azure AD SAML Toolkit** do vyhledávacího pole.
1. Vyberte **Azure AD SAML Toolkit** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Azure AD SAML Toolkit pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v sadě nástrojů Azure AD SAML.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD se sadou nástrojů Azure AD SAML, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace nástrojů Azure AD SAML SSO](#configure-azure-ad-saml-toolkit-sso)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)**  mít protějšek B.Simon v sadě nástrojů Azure AD SAML, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Azure AD SAML Toolkit** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    1. V **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://samltoolkit.azurewebsites.net/`

    1. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL: `https://samltoolkit.azurewebsites.net`

    1. V **adresy URL odpovědi** textové pole, zadejte adresu URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Na **nastavení Azure AD SAML Toolkit** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurace jednotného přihlašování k Azure AD SAML Toolkit

1. Otevřít novém okně webového prohlížeče, pokud jste se ještě nezaregistrovali na webu Azure AD SAML Toolkit nejprve zaregistrovat po kliknutí na **zaregistrovat**. Pokud jste už zaregistrovali, přihlášení do Azure AD SAML Toolkit webu společnosti pomocí registrovaná přihlášení přihlašovací údaje.

    ![Registrace Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/register.png)

1. Klikněte na **konfigurace SAML**.

    ![Konfigurace Azure AD SAML Toolkit SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klikněte na možnost **Vytvořit**.

    ![Vytvořit jednotné přihlašování Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Na **Konfigurace jednotného přihlašování SAML** stránce, proveďte následující kroky:

    ![Vytvořit jednotné přihlašování Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/fill-details.png)

    1. V **přihlašovací adresa URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V **Azure AD identifikátor** vložit do textového pole **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. Klikněte na tlačítko **zvolit soubor** a nahrajte **kód XML metadat federace** soubor, který jste si stáhli z portálu Azure portal.

    1. Klikněte na možnost **Vytvořit**.

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

V této části povolíte B.Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Azure AD SAML Toolkit.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Azure AD SAML Toolkit**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Vytvořit testovacího uživatele Azure AD SAML Toolkit

V této části se uživateli B.Simon vytvoří v Azure AD SAML Toolkit. Sadu nástrojů Azure AD SAML podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v sadě nástrojů Azure AD SAML, se po ověření vytvoří nový.

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Azure AD SAML Toolkit na přístupovém panelu, by měly být automaticky přihlásíte k Azure AD SAML Toolkit, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
