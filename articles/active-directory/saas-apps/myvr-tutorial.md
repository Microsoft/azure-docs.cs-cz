---
title: 'Kurz: Integrace Azure Active Directory s MyVR | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MyVR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b1b4bbc7-cdac-447c-83a3-bcb869159255
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10473c820cd0dbb7c7db3f33de203ca4d636414b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096494"
---
# <a name="tutorial-integrate-myvr-with-azure-active-directory"></a>Kurz: MyVR integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat MyVR s Azure Active Directory (Azure AD). Když integrujete MyVR s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k MyVR.
* Aby uživatelé mohli být automaticky přihlášeni k MyVR pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* MyVR jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. 

* Podporuje MyVR **SP a zprostředkovatele identity** jednotné přihlašování zahájené.
* Podporuje MyVR **JIT** zřizování uživatelů.

## <a name="adding-myvr-from-the-gallery"></a>Přidání MyVR z Galerie

Konfigurace integrace MyVR do služby Azure AD, budete muset přidat MyVR z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **MyVR** do vyhledávacího pole.
1. Vyberte **MyVR** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s MyVR pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v MyVR.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s MyVR, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace jednotného přihlašování MyVR](#configure-myvr-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s B. Simon.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit B. Simon používat Azure AD jednotného přihlašování.
1. **[Vytvořit testovacího uživatele MyVR](#create-myvr-test-user)**  – Pokud chcete mít protějšek B. Simon MyVR, který je propojený s Azure AD reprezentace uživatele.
1. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **MyVR** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud uživatele, aby konfigurace aplikace v **IDP** iniciované režimu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrované se službou Azure.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

   V **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://ess.virtualroster.net/ess/login.aspx`

1. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

   | Název | Zdrojový atribut|
   | ---------------| --------------- |
   | givenName | user.givenname |
   | Příjmení | user.surname |
   | emailaddress | user.mail |
   | EmployeeID | user.employeeid |

   1. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.
   1. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
   1. Nechte **Namespace** prázdné.
   1. Vyberte zdroj jako **atribut**.
   1. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
   1. Klikněte na tlačítko **OK**.
   1. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení MyVR** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-myvr-sso"></a>Konfigurace jednotného přihlašování MyVR

Ke konfiguraci jednotného přihlašování na **MyVR** straně, je nutné odeslat na stažený **certifikát (Base64)** a vhodné zkopírovaný adresy URL z webu Azure portal [tým podpory MyVR](mailto:arno.vandenberg@Kronos.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části povolíte B. Simon k udělení přístupu k MyVR použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **MyVR**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-myvr-test-user"></a>Vytvoření MyVR testovacího uživatele

V této části se vytvoří uživatele služby serveru B. Simon ve MyVR. MyVR podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi MyVR, vytvoří se nový po ověření.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice MyVR na přístupovém panelu, můžete by měl být automaticky přihlášeni k MyVR, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
