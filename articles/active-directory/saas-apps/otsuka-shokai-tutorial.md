---
title: 'Kurz: Integrace Azure Active Directory s Otsuka Shokai | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Otsuka Shokai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c04bb636-a3c7-4940-9b36-810425b855d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bec4cb17f2d25cd00ef115a78736c95eaf26d95f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752464"
---
# <a name="tutorial-integrate-otsuka-shokai-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory Otsuka Shokai

V tomto kurzu se dozvíte, jak integrovat Otsuka Shokai s Azure Active Directory (Azure AD). Když integrujete Otsuka Shokai s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Otsuka Shokai.
* Aby uživatelé mohli být automaticky přihlášeni k Otsuka Shokai pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Otsuka Shokai jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Otsuka Shokai **IDP** jednotné přihlašování zahájené.

## <a name="adding-otsuka-shokai-from-the-gallery"></a>Přidání Otsuka Shokai z Galerie

Konfigurace integrace Otsuka Shokai do služby Azure AD, budete muset přidat Otsuka Shokai z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Otsuka Shokai** do vyhledávacího pole.
1. Vyberte **Otsuka Shokai** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.
1. Klikněte na **vlastnosti** kartu, zkopírujte **ID aplikace** a uložte ho do počítače pro pozdější použití.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Otsuka Shokai pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Otsuka Shokai.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Otsuka Shokai, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Otsuka Shokai](#configure-otsuka-shokai)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Otsuka Shokai](#create-otsuka-shokai-test-user)**  mít protějšek B. Simon Otsuka Shokai, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Otsuka Shokai** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránka, aplikace je předem nakonfigurovaný a potřebné adresy URL se už předem vyplní s Azure. Uživatel musí uložte konfiguraci kliknutím **Uložit** tlačítko.

1. Otsuka Shokai aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace Otsuka Shokai **nameidentifier** namapovat s **user.objectid**, takže budete muset kliknout na Upravit mapování atributů **upravit**  ikonu a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Kromě výše Otsuka Shokai aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | ID aplikace | `<Application ID>` |

    >[!NOTE]
    >`<Application ID>` je hodnota, kterou jste zkopírovali z **vlastnosti** kartu z webu Azure portal.

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ho do vašeho programu Poznámkový blok .

   ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

1. Na **nastavení Otsuka Shokai** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-otsuka-shokai"></a>Konfigurace Otsuka Shokai

Ke konfiguraci jednotného přihlašování na **Otsuka Shokai** straně, je nutné odeslat **adresa Url federačních metadat aplikace** k [tým podpory Otsuka Shokai](mailto:Tatsuya.Satoh@otsuka-shokai.co.jp). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části povolíte B. Simon používat jednotné přihlašování Azure díky udělení přístupu k Otsuka Shokai.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Otsuka Shokai**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-otsuka-shokai-test-user"></a>Vytvoření Otsuka Shokai testovacího uživatele

V této části vytvořte uživatele B.Simon v Otsuka Shokai. Práce s [tým podpory Otsuka Shokai](mailto:Tatsuya.Satoh@otsuka-shokai.co.jp) přidat uživatele na platformě Otsuka Shokai. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Otsuka Shokai na přístupovém panelu, můžete by měl být automaticky přihlášeni ke Otsuka Shokai, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
