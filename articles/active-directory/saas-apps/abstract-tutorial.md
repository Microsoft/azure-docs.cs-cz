---
title: 'Kurz: Integrace Azure Active Directory s abstraktní | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Abstract současně.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8038784c9da30a42541688536169576cd9a92e9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235008"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory abstraktní

V tomto kurzu se dozvíte, jak zajistit integraci s Azure Active Directory (Azure AD) abstraktní. Když abstraktní můžete integrovat s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k abstraktní.
* Aby uživatelé mohli být automaticky přihlášeni k abstraktní pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Abstraktní jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí.

* Abstraktní podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-abstract-from-the-gallery"></a>Přidání abstraktu z Galerie

Konfigurace integrace abstraktní do služby Azure AD, budete muset přidat abstraktní z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **abstraktní** do vyhledávacího pole.
1. Vyberte **abstraktní** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s pomocí testovacího uživatele volat abstraktní **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v abstraktní.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Abstract, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování k abstraktní](#configure-abstract-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit abstraktní testovacího uživatele](#create-abstract-test-user)**  – Pokud chcete mít protějšek Britta Simon v abstraktní, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **abstraktní** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** aplikace je nakonfigurovaná v části **IDP** iniciované režimu a potřebné adresy URL se už předem vyplněné s Azure. Uživatel musí uložte konfiguraci kliknutím **Uložit** tlačítko.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://app.abstract.com/signin`

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurace jednotného přihlašování k abstraktní

Ujistěte se, že se načíst vaše `App Federation Metadata Url` a `Azure AD Identifier` z portálu Azure portal, jako je bude nutné do konfigurace jednotného přihlašování na abstraktní.

Tyto informace najdete v **nastavte si jednotné přihlašování pomocí SAML** stránky:

* `App Federation Metadata Url` Se nachází v **podpisový certifikát SAML** oddílu.
* `Azure AD Identifier` Se nachází v **nastavení abstraktní** oddílu.


Nyní jste připraveni ke konfiguraci jednotného přihlašování na abstraktní:

>[!Note]
>Budete potřebovat k ověření pomocí účtu správce pro přístup k nastavení jednotného přihlašování na abstraktní organizace.

1. Otevřít [abstraktní webové aplikace](https://app.abstract.com/).
2. Přejděte **oprávnění** stránky v levém bočním panelu.
3. V **nakonfigurovat jednotné přihlašování** části, zadejte vaše **adresa URL metadat** a **Entity ID**.
4. Zadejte, může být ruční výjimky. E-mailů uvedených v části Ruční výjimky budou obejít jednotného přihlašování a nebudou moct připojovat se pomocí e-mailu a hesla. 
5. Klikněte na tlačítko **uložit změny**.

>[!Note] 
>Budete muset použít primární e-mailové adresy v seznamu výjimek ruční. Aktivace jednotného přihlašování se nezdaří, pokud je e-mail, který uvedete sekundárního e-mailu uživatele. Pokud k tomu dojde, zobrazí se vám chybová zpráva s primární e-mailu pro účet selhání. Po ověření, že víte, že uživatel, přidejte do seznamu výjimek ruční primární e-mailovou.

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

V této části povolíte B.Simon používat jednotné přihlašování Azure tím, že udělíte přístup k abstraktní.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **abstraktní**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-abstract-test-user"></a>Vytvořit abstraktní testovacího uživatele

Otestovat jednotné přihlašování na abstraktní:

1. Otevřít [abstraktní webové aplikace](https://app.abstract.com/).
2. Přejděte **oprávnění** stránky v levém bočním panelu.
3. Klikněte na tlačítko **testů s účtem**. Pokud se test nezdaří, zkontrolujte [obraťte se na náš tým podpory](https://www.abstract.com/help/contact/).

>[!Note]
>Budete potřebovat k ověření pomocí účtu správce pro přístup k nastavení jednotného přihlašování na abstraktní organizace.
Tento účet správce organizace muset abstraktní přiřadit na portálu Azure portal.

### <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na abstraktní dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni k abstraktní, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

