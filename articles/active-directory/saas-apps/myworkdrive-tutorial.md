---
title: 'Kurz: Integrace Azure Active Directory s MyWorkDrive | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 61173d21c52d061f0d02ab02eb2f1083507983c2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991679"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Kurz: MyWorkDrive integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat MyWorkDrive s Azure Active Directory (Azure AD). Když integrujete MyWorkDrive v Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k MyWorkDrive.
* Aby uživatelé mohli být automaticky přihlášeni k MyWorkDrive pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* MyWorkDrive jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje MyWorkDrive **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-myworkdrive-from-the-gallery"></a>Přidání MyWorkDrive z Galerie

Konfigurace integrace MyWorkDrive do služby Azure AD, budete muset přidat MyWorkDrive v galerii na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **MyWorkDrive** do vyhledávacího pole.
1. Vyberte **MyWorkDrive** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s MyWorkDrive pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v MyWorkDrive.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s MyWorkDrive, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování MyWorkDrive](#configure-myworkdrive-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele MyWorkDrive](#create-myworkdrive-test-user)**  – Pokud chcete mít protějšek Britta Simon MyWorkDrive, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **MyWorkDrive** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, zadejte hodnoty pro následující pole:

    V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Zadejte name:e.g hostitele MyWorkDrive serveru vaší společnosti.
    > 
    > Adresa URL odpovědi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Přihlašovací adresa URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kontakt [tým podpory MyWorkDrive](mailto:support@myworkdrive.com) Pokud si nejste jistí, jak nastavit vlastní název hostitele a certifikát SSL pro tyto hodnoty.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** do schránky.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Konfigurace jednotného přihlašování MyWorkDrive

1. V okně jiné webové prohlížeče Přihlaste se k MyWorkDrive jako správce zabezpečení.

2. Na serveru MyWorkDrive v panelu Správce, klikněte na **ENTERPRISE** a proveďte následující kroky:

    ![Správce](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Povolit **jednotného přihlašování SAML/ADFS**.

    b. Vyberte **SAML - Azure AD**

    c. V **adresa Url federačních metadat aplikace Azure** textového pole vložte hodnotu **adresa Url federačních metadat aplikace** který jste zkopírovali z portálu Azure portal.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Pro další informace najdete [článek podpory MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

V této části se budou moci používat jednotné přihlašování Azure díky udělení přístupu k MyWorkDrive Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **MyWorkDrive**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-myworkdrive-test-user"></a>Vytvoření MyWorkDrive testovacího uživatele

V této části vytvoříte uživatele v MyWorkDrive jako Britta Simon. Práce s [tým podpory MyWorkDrive](mailto:support@myworkdrive.com) přidat uživatele na platformě MyWorkDrive. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice MyWorkDrive na přístupovém panelu, můžete by měl být automaticky přihlášeni k MyWorkDrive, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)