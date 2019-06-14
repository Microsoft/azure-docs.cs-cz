---
title: 'Kurz: Integrace Azure Active Directory s RingCentral | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092860"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Kurz: RingCentral integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat RingCentral s Azure Active Directory (Azure AD). Při integraci RingCentral s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k RingCentral.
* Aby uživatelé mohli být automaticky přihlášeni k RingCentral pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* RingCentral jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje RingCentral **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-ringcentral-from-the-gallery"></a>Přidání RingCentral z Galerie

Konfigurace integrace RingCentral do služby Azure AD, budete muset přidat RingCentral z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **RingCentral** do vyhledávacího pole.
1. Vyberte **RingCentral** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s RingCentral pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v RingCentral.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s RingCentral, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování RingCentral](#configure-ringcentral-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele RingCentral](#create-ringcentral-test-user)**  – Pokud chcete mít protějšek Britta Simon RingCentral, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **RingCentral** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    1. Klikněte na tlačítko **nahrát soubor metadat**.
    1. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.
    1. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** oddílu.

    > [!Note]
    > Můžete získat **soubor metadat poskytovatele služeb** na stránce Konfigurace jednotného přihlašování RingCentral, který je vysvětlen později v tomto kurzu.

1. Pokud nemáte **soubor metadat poskytovatele služeb**, zadejte hodnoty pro následující pole:

    a. V **identifikátor** textového pole zadejte adresu URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Konfigurace jednotného přihlašování RingCentral

1. V okně jiné webové prohlížeče Přihlaste se k RingCentral jako správce zabezpečení.

1. V horní části klikněte na **nástroje**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Přejděte do **jednotného přihlašování**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na **Single Sign-on** stránce v části **Konfigurace jednotného přihlašování** části z **kroku 1** klikněte na tlačítko **upravit** a proveďte následující kroky:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na **nastavit jednotné přihlašování** stránce, proveďte následující kroky:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klikněte na tlačítko **Procházet** nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    b. Po nahrání metadat získat hodnoty automaticky dosadí **jednotného přihlašování obecné informace o** oddílu.

    c. V části **mapování atributů** vyberte **mapu e-mailu atributu** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klikněte na **Uložit**.

    e. Z **kroku 2** klikněte na tlačítko **Stáhnout** ke stažení **soubor metadat poskytovatele služeb** a nahrajte ho v **základní konfiguraci SAML** oddílu Chcete-li automaticky vyplnit pole **identifikátor** a **adresy URL odpovědi** hodnoty na webu Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Přejděte na stejné stránce **povolit jednotné přihlašování** části a proveďte následující kroky:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Vyberte **povolení jednotného přihlašování služby**.

    * Vyberte **umožňují uživatelům přihlásit se prostřednictvím přihlašovacích údajů pro jednotné přihlašování nebo RingCentral**.

    * Klikněte na **Uložit**.

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

V této části se budou moci používat jednotné přihlašování Azure díky udělení přístupu k RingCentral Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **RingCentral**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-ringcentral-test-user"></a>Vytvoření RingCentral testovacího uživatele

V této části vytvoříte uživatele v RingCentral jako Britta Simon. Práce s [tým podpory RingCentral klienta](https://success.ringcentral.com/RCContactSupp) přidat uživatele na platformě RingCentral. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice RingCentral na přístupovém panelu, můžete by měl být automaticky přihlášeni k RingCentral, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
