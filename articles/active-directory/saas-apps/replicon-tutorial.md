---
title: 'Kurz: Integrace Azure Active Directory s Replicon | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092769"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Kurz: Replicon integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Replicon s Azure Active Directory (Azure AD). Když integrujete Replicon s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Replicon.
* Aby uživatelé mohli být automaticky přihlášeni k Replicon pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Replicon jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje replicon **SP** jednotné přihlašování zahájené.

## <a name="adding-replicon-from-the-gallery"></a>Přidání Replicon z Galerie

Konfigurace integrace Replicon do služby Azure AD, budete muset přidat Replicon z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Replicon** do vyhledávacího pole.
1. Vyberte **Replicon** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Replicon pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Replicon.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Replicon, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování Replicon](#configure-replicon-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Replicon](#create-replicon-test-user)**  – Pokud chcete mít protějšek B.Simon Replicon, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Replicon** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    1. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://global.replicon.com/!/saml2/<client name>`

    1. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory Replicon klienta](https://www.replicon.com/customerzone/contact-support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Klikněte na ikonu úprav/pera **podpisový certifikát SAML** můžete upravit nastavení.

    ![Podpisový algoritmus](common/signing-algorithm.png)

    1. Vyberte **kontrolní výraz SAML přihlašování** jako **podepisování možnost**.

    1. Vyberte **SHA-256** jako **podpisový algoritmus**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **kód XML metadat federace** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurace Replicon jednotného přihlašování

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Replicon.

2. Konfigurace SAML 2.0, proveďte následující kroky:

    ![Povolit ověřování SAML](./media/replicon-tutorial/ic777805.png "ověřování povolit SAML")

    a. Chcete-li zobrazit **EnableSAML Authentication2** dialogového okna, připojte k adrese URL, po vašeho klíče společnosti: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Následuje schéma úplnou adresu URL: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klikněte na tlačítko **+** rozšířit **v20Configuration** oddílu.

   c. Klikněte na tlačítko **+** rozšířit **metaDataConfiguration** oddílu.

   d. Vyberte **SHA256** pro xmlSignatureAlgorithm

   e. Klikněte na tlačítko **zvolit soubor**, a vyberte soubor XML metadat zprostředkovatele identity, klikněte na tlačítko **odeslat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon k udělení přístupu k Replicon použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Replicon**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-replicon-test-user"></a>Vytvoření Replicon testovacího uživatele

Cílem této části je vytvořte uživatele B.Simon v Replicon.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. V okně webového prohlížeče přihlaste jako správce serveru vaší společnosti Replicon.

2. Přejděte na **správu \> uživatelé**.

    ![Uživatelé](./media/replicon-tutorial/ic777806.png "uživatelů")

3. Klikněte na tlačítko **+ přidat uživatele**.

    ![Přidání uživatele](./media/replicon-tutorial/ic777807.png "přidat uživatele")

4. V **profilu uživatele** části, proveďte následující kroky:

    ![Profil uživatele](./media/replicon-tutorial/ic777808.png "profilu uživatele")

    a. V **přihlašovací jméno** textové pole, typ služby Azure AD e-mailová adresa uživatele Azure AD, které chcete zřídit jako `B.Simon@contoso.com`.

    > [!NOTE]
    > Přihlašovací jméno musí odpovídat e-mailovou adresu uživatele ve službě Azure AD

    b. Jako **typ ověřování**vyberte **jednotného přihlašování**.

    c. ID sady ověřování na stejnou hodnotu jako přihlašovací jméno (e-mailovou adresu služby Azure AD uživatele)

    d. V **oddělení** textového pole zadejte oddělení uživatele.

    e. Jako **typ zaměstnance**vyberte **správce**.

    f. Klikněte na tlačítko **profil uživatele uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné Replicon uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Replicon zřízení uživatelských účtů služby Azure AD.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Replicon na přístupovém panelu, můžete by měl být automaticky přihlášeni k Replicon, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)