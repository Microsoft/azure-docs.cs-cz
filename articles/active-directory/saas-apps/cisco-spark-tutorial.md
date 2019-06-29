---
title: 'Kurz: Integrace Azure Active Directory s Cisco Webex | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc537a631cd083da0f902fb4fcd44d47756eeba
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471777"
---
# <a name="tutorial-integrate-cisco-webex-with-azure-active-directory"></a>Kurz: Integrace Cisco Webex s Azure Active Directory

V tomto kurzu se dozvíte, jak zajistit integraci s Azure Active Directory (Azure AD) Cisco Webex. Při integraci Cisco Webex s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Cisco Webex.
* Aby uživatelé mohli být automaticky přihlášeni k Cisco Webex pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Cisco Webex jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Cisco Webex **SP a zprostředkovatele identity** iniciované jednotné přihlašování a podporuje **automatizovaná** zřizování uživatelů.

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie

Konfigurace integrace Cisco Webex do služby Azure AD, budete muset přidat Cisco Webex z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Cisco Webex** do vyhledávacího pole.
1. Vyberte **Cisco Webex** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Cisco Webex pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Cisco Webex.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Cisco Webex, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Cisco Webex](#configure-cisco-webex)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Cisco Webex](#create-cisco-webex-test-user)**  mít protějšek B.Simon Cisco Webex, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Cisco Webex** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části tím, že nahrajete na stažený **metadata poskytovatele služeb** souboru a nakonfigurovat aplikaci tak, že provedete následující kroky:

    >[!Note]
    >Zobrazí se soubor metadat zprostředkovatelů služby z **Cisco Webex** portálu. 

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném dokončení nahrávání souboru metadat poskytovatele služeb **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části:

    V **přihlašovací adresa URL** textového pole vložte hodnotu **adresy URL odpovědi**, která se získá autofilled SP za načtení souboru metadat.

5. Aplikaci Webex Cisco očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na **upravit** ikona přidat atributy.

    ![image](common/edit-attribute.png)

6. Kromě toho výše aplikaci Cisco Webex očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:
    
    | Název |  Zdrojový atribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **kód XML metadat federace** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Na **nastavení Cisco Webex** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-cisco-webex"></a>Konfigurace Cisco Webex

1. Přihlaste se k [správu spolupráci cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

2. Vyberte **nastavení** a v části **ověřování** klikněte na tlačítko **změnit**.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Vyberte **integrovat zprostředkovatele identity 3. stran. (Rozšířené)**  a přejít na další obrazovce.

4. Na **Import metadat zprostředkovatele identity** stránka, buď přetažením a soubor metadat služby Azure AD na stránku nebo pomocí možnosti prohlížeč souborů najděte a nahrajte soubor metadat služby Azure AD. Vyberte **vyžadovat certifikát podepsaný certifikační autoritou v metadatech (s vyšším zabezpečením)** a klikněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Vyberte **Test jednotného přihlašování k připojení**a když se otevře na nové kartě prohlížeče, proveďte ověření pomocí služby Azure AD přihlášením.

6. Zpět **správu spolupráci cloudu Cisco** záložce prohlížeče. Pokud byl test úspěšný, vyberte **tento test proběhl úspěšně. Možnost jednotného přihlašování** a klikněte na tlačítko **Další**.

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

V této části povolíte B.Simon používat jednotné přihlašování Azure díky udělení přístupu k Cisco Webex.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Cisco Webex**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-cisco-webex-test-user"></a>Vytvořit testovacího uživatele Cisco Webex

V této části vytvořte uživatele Britta Simon v Cisco Webex. V této části vytvořte uživatele Britta Simon v Cisco Webex.

1. Přejděte [správu spolupráci cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

2. Klikněte na tlačítko **uživatelé** a potom **spravovat uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. V **spravovat uživatele** okně **ručně přidávat nebo upravovat uživatele** a klikněte na tlačítko **Další**.

4. Vyberte **jména a e-mailová adresa**. Potom zadejte textového pole následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **B**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

    c. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele b.simon@contoso.com.

5. Klikněte symbol plus přidejte Britta Simon. Pak klikněte na **Další**.

6. V **přidat služby pro uživatele** okna, klikněte na tlačítko **Uložit** a potom **Dokončit**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Cisco Webex na přístupovém panelu můžete by měl být automaticky přihlášeni k Webex Cisco, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)