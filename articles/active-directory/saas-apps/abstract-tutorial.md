---
title: 'Kurz: Integrace Azure Active Directory s abstraktním | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a abstrakcí.
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
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302591"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Kurz: Integrace abstrakce s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat abstrakci s Azure Active Directory (Azure AD). Když integruje abstrakci s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k abstraktnímu.
* Umožněte uživatelům, aby se automaticky přihlásili k abstrakci pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr aktivovaného jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Abstraktní podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování

## <a name="adding-abstract-from-the-gallery"></a>Přidání abstraktu z Galerie

Pokud chcete nakonfigurovat integraci abstraktu do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat abstrakt z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **abstract** .
1. Z panelu výsledků vyberte **abstract** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím testů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v abstraktním.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s abstraktním, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Konfigurace abstraktního jednotného přihlašování](#configure-abstract-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte abstraktního testovacího uživatele](#create-abstract-test-user)** , který bude mít protějšek Britta Simon v abstraktním, který je propojený s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **abstraktní** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:`https://app.abstract.com/signin`

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurace abstraktního jednotného přihlašování

Ujistěte se, že jste `App Federation Metadata Url` načetli své `Azure AD Identifier` a z Azure Portal, jak je budete potřebovat ke konfiguraci jednotného přihlašování pro abstrakt.

Tyto informace najdete na stránce **nastavit jednotné přihlašování pomocí SAML** :

* Je umístěn v části **podpisový certifikát SAML.** `App Federation Metadata Url`
* Je umístěn v části **set abstract.** `Azure AD Identifier`


Nyní jste připraveni nakonfigurovat jednotné přihlašování pro abstraktní:

>[!Note]
>Pro přístup k nastavení jednotného přihlašování na abstraktu se budete muset ověřit pomocí účtu správce organizace.

1. Otevřete [abstraktní webovou aplikaci](https://app.abstract.com/).
2. Přejít na stránku **oprávnění** na levém panelu.
3. V části **Konfigurace jednotného přihlašování** zadejte **adresu URL metadat** a **ID entity**.
4. Zadejte jakékoli ruční výjimky, které byste mohli mít. E-maily uvedené v části Ruční výjimky budou obcházet SSO a budou se moct přihlásit pomocí e-mailu a hesla. 
5. Klikněte na tlačítko **uložit změny**.

>[!Note] 
>V seznamu ručních výjimek budete muset použít primární e-mailové adresy. Aktivace jednotného přihlašování se nezdaří, pokud je e-mailem, který si vypíšete, sekundární e-mail uživatele. V takovém případě se zobrazí chybová zpráva s primárním e-mailem pro účet, u kterého došlo k selhání. Tento primární e-mail přidejte do seznamu ručních výjimek, až ověříte, že znáte uživatele.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k abstraktnímu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **abstract**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-abstract-test-user"></a>Vytvořit abstraktního testovacího uživatele

Postup při testování jednotného přihlašování na abstraktních počítačích:

1. Otevřete [abstraktní webovou aplikaci](https://app.abstract.com/).
2. Přejít na stránku **oprávnění** na levém panelu.
3. Klikněte na **test s mým účtem**. Pokud se test nezdařil, [obraťte se na náš tým podpory](https://www.abstract.com/help/contact/).

>[!Note]
>Pro přístup k nastavení jednotného přihlašování na abstraktu se budete muset ověřit pomocí účtu správce organizace.
Tento účet správce organizace bude muset být přiřazený k abstraktnímu na Azure Portal.

### <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici abstraktu na přístupovém panelu byste měli být automaticky přihlášeni k abstrakci, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

