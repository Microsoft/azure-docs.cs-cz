---
title: 'Kurz: Integrace Azure Active Directory s zkontrolujte bodu CloudGuard Dome9 oblouk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zkontrolujte bodu CloudGuard Dome9 oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdaaab8257d3a79130902e1ba0466f9cf15484f4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147141"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Kurz: Kontrola bod CloudGuard Dome9 oblouk integrovat Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat zkontrolujte bodu CloudGuard Dome9 oblouk s Azure Active Directory (Azure AD). Při integraci zkontrolujte bodu CloudGuard Dome9 oblouk s Azure AD, můžete:

* V Azure AD, který má přístup k zkontrolujte oblouk Dome9 CloudGuard bod ovládacím prvku.
* Aby uživatelé mohli být automaticky přihlášeni zkontrolujte bodu CloudGuard Dome9 oblouk s jejich účty Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zkontrolujte, že bod CloudGuard Dome9 oblouk jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Zkontrolujte oblouk Dome9 CloudGuard bod podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Přidání zkontrolujte bodu CloudGuard Dome9 oblouk z Galerie

Pokud chcete nakonfigurovat integraci zkontrolujte bodu CloudGuard Dome9 oblouku do služby Azure AD, budete muset přidat z Galerie zkontrolujte bodu CloudGuard Dome9 oblouk na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **zkontrolujte bodu CloudGuard Dome9 oblouk** do vyhledávacího pole.
1. Vyberte **zkontrolujte bodu CloudGuard Dome9 oblouk** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s zkontrolujte bodu CloudGuard Dome9 oblouk pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel v zkontrolovat bodu CloudGuard Dome9 oblouk.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s zkontrolujte bodu CloudGuard Dome9 oblouk, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace zkontrolujte bodu CloudGuard Dome9 oblouk](#configure-check-point-cloudguard-dome9-arc)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele zkontrolujte bodu CloudGuard Dome9 oblouk](#create-check-point-cloudguard-dome9-arc-test-user)**  mít protějšek B.Simon zkontrolujte bodu CloudGuard Dome9 Arc, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **zkontrolujte bodu CloudGuard Dome9 oblouk** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování** .
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://secure.dome9.com/`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Hodnota názvu vaší společnosti vybere v dome9 portál pro správu, který je vysvětlen později v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL adresy URL odpovědi a přihlašování. Kontakt [tým podpory zkontrolujte bodu CloudGuard Dome9 oblouk klienta](mailto:Dome9@checkpoint.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Aplikace Check Point CloudGuard Dome9 oblouk očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

7. Kromě toho výše zkontrolujte bodu CloudGuard Dome9 oblouk aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka: 

    | Name |  Zdrojový atribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení zkontrolujte bodu CloudGuard Dome9 oblouk** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Nakonfigurovat zaškrtnutí bodu CloudGuard Dome9 oblouk

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti zkontrolujte bodu CloudGuard Dome9 oblouk.

2. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení účtu**. 

    ![Zkontrolujte konfiguraci bodu CloudGuard Dome9 oblouk](./media/dome9arc-tutorial/configure1.png)

3. Přejděte do **jednotného přihlašování** a potom klikněte na tlačítko **povolit**.

    ![Zkontrolujte konfiguraci bodu CloudGuard Dome9 oblouk](./media/dome9arc-tutorial/configure2.png)

4. V části Konfigurace jednotného přihlašování proveďte následující kroky:

    ![Zkontrolujte konfiguraci bodu CloudGuard Dome9 oblouk](./media/dome9arc-tutorial/configure3.png)

    a. Zadejte název společnosti **ID účtu** textového pole. Tato hodnota má být použita v adrese URL odpovědi uvedených na webu Azure Portal **základní konfiguraci SAML** oddílu.

    b. V **vystavitele** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z webu Azure Portal.

    c. V **adresu url koncového bodu Idp** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z webu Azure Portal.

    d. Otevřete váš stažený certifikát kódovaný v Base64 v poznámkovém bloku, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.

    e. Klikněte na **Uložit**.

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

V této části povolíte B.Simon používat jednotné přihlašování Azure díky udělení přístupu zkontrolujte bodu CloudGuard Dome9 oblouk.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **zkontrolujte bodu CloudGuard Dome9 oblouk**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Vytvořit testovacího uživatele zkontrolujte bodu CloudGuard Dome9 oblouk

Povolit Azure AD uživatelům umožní přihlásit zkontrolujte oblouk Dome9 CloudGuard bod, musí být poskytnuty do aplikace. Zkontrolujte oblouk Dome9 CloudGuard bod podporuje just-in-time zřizování, ale to fungovalo správně, uživatel muset vybrat konkrétní **Role** a přiřaďte jiný uživatel.

   >[!Note]
   >Pro **Role** vytváření a další podrobnosti o kontaktu [tým podpory zkontrolujte bodu CloudGuard Dome9 oblouk klienta](mailto:Dome9@checkpoint.com).

**Zřídit účet uživatele ručně, proveďte následující kroky:**

1. Přihlaste se k webu společnosti zkontrolujte bodu CloudGuard Dome9 oblouk jako správce.

2. Klikněte na **uživatelů a rolí** a potom klikněte na tlačítko **uživatelé**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user2.png)

4. V **vytvořit uživatele** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user3.png)

    a. V **e-mailu** , jako je textové pole, typ e-mailu uživatele B.Simon@contoso.com.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako jsou služby serveru B.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    d. Ujistěte se, **jednotného přihlašování uživatele** jako **na**.

    e. Klikněte na tlačítko **vytvořit**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice zkontrolujte bodu CloudGuard Dome9 oblouk na přístupovém panelu, vám by měl být automaticky přihlášeni ke zkontrolujte bodu CloudGuard Dome9 oblouku u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)