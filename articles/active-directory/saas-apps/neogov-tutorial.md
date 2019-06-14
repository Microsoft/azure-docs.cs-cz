---
title: 'Kurz: Integrace Azure Active Directory s NEOGOV | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NEOGOV.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6e4481af-54f1-4689-80d0-bd02a749ef53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8e208c8e5f1136ef5029e03b59a2f09e113dc3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096347"
---
# <a name="tutorial-integrate-neogov-with-azure-active-directory"></a>Kurz: NEOGOV integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat NEOGOV s Azure Active Directory (Azure AD). Když integrujete NEOGOV s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k NEOGOV.
* Aby uživatelé mohli být automaticky přihlášeni k NEOGOV pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* NEOGOV jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje NEOGOV **IDP** jednotné přihlašování zahájené.

## <a name="adding-neogov-from-the-gallery"></a>Přidání NEOGOV z Galerie

Konfigurace integrace NEOGOV do služby Azure AD, budete muset přidat NEOGOV z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **NEOGOV** do vyhledávacího pole.
1. Vyberte **NEOGOV** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s NEOGOV pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v NEOGOV.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s NEOGOV, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování NEOGOV](#configure-neogov-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s B. Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit B. Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele NEOGOV](#create-neogov-test-user)**  – Pokud chcete mít protějšek B. Simon NEOGOV, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **NEOGOV** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    
    | Prostředí | Vzor adresy URL |
    | -- | -- |
    | Výroba | `https://www.neogov.com/` |
    | Sandbox | `https://www.uat.neogov.net/` |
    | | |

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    | Prostředí | Vzor adresy URL |
    | -- | -- |
    | Výroba | `https://login.neogov.com/authentication/saml/consumer` |
    | Sandbox | `https://login.uat.neogov.net/authentication/saml/consumer` |
    | | |

1. NEOGOV aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace NEOGOV **nameidentifier** namapovat s **user.objectid**, takže budete muset upravit mapování atributů po kliknutí na **upravit** ikonu a změňte mapování atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho výše NEOGOV aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V části deklarace identity uživatelů v dialogovém okně atributy uživatele, proveďte následující kroky přidat atribut tokenu SAML, jak je znázorněno následující tabulka:

    | Name |  Zdrojový atribut|
    | -------|--------- |
    | mail | user.mail |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-neogov-sso"></a>Konfigurace jednotného přihlašování NEOGOV

Ke konfiguraci jednotného přihlašování na **NEOGOV** straně, je nutné odeslat **adresa Url federačních metadat aplikace** k [tým podpory NEOGOV](mailto:itops@neogov.net). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B. Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon k udělení přístupu k NEOGOV použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **NEOGOV**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-neogov-test-user"></a>Vytvoření NEOGOV testovacího uživatele

V této části vytvořte uživatele B. Simon v NEOGOV. Práce s [tým podpory NEOGOV](mailto:itops@neogov.net) přidat uživatele na platformě NEOGOV. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice NEOGOV na přístupovém panelu, můžete by měl být automaticky přihlášeni k NEOGOV, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)