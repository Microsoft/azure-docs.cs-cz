---
title: 'Kurz: Azure Active Directory Integration s abstraktním | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a abstrakcí.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: e1c3236c4c1957b4d0daee8d30c71f03fb8674dd
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587804"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Kurz: integrace abstrakce s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat abstrakci s Azure Active Directory (Azure AD). Když integruje abstrakci s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k abstraktnímu.
* Umožněte uživatelům, aby se automaticky přihlásili k abstrakci pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr aktivovaného jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Abstraktní podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování

## <a name="adding-abstract-from-the-gallery"></a>Přidání abstraktu z Galerie

Pokud chcete nakonfigurovat integraci abstraktu do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat abstrakt z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **abstract** .
1. Z panelu výsledků vyberte **abstract** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím testů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v abstraktním.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s abstraktním, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Konfigurace abstraktního jednotného přihlašování](#configure-abstract-sso)** – pro konfiguraci nastavení jednoho Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte abstraktního testovacího uživatele](#create-abstract-test-user)** , který bude mít protějšek Britta Simon v abstraktním, který je propojený s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **abstraktní** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.abstract.com/signin`

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurace abstraktního jednotného přihlašování

Ujistěte se, že jste načetli své `App Federation Metadata Url` a `Azure AD Identifier` z Azure Portal, jak je budete potřebovat ke konfiguraci jednotného přihlašování pro abstrakt.

Tyto informace najdete na stránce **nastavit jeden Sign-On se** stránkou SAML:

* `App Federation Metadata Url`Je umístěn v části **podpisový certifikát SAML** .
* `Azure AD Identifier`Je umístěn v části **set abstract** .


Nyní jste připraveni nakonfigurovat jednotné přihlašování pro abstraktní:

>[!Note]
>Pro přístup k nastavení jednotného přihlašování na abstraktu se budete muset ověřit pomocí účtu správce organizace.

1. Otevřete [abstraktní webovou aplikaci](https://app.abstract.com/).
2. Přejít na stránku **oprávnění** na levém panelu.
3. V části **Konfigurace jednotného přihlašování** zadejte **adresu URL metadat** a **ID entity**.
4. Zadejte jakékoli ruční výjimky, které byste mohli mít. E-maily uvedené v části Ruční výjimky budou obcházet SSO a budou se moct přihlásit pomocí e-mailu a hesla. 
5. Klikněte na **Save Changes** (Uložit změny).

>[!Note] 
>V seznamu ručních výjimek budete muset použít primární e-mailové adresy. Aktivace jednotného přihlašování se nezdaří, pokud je e-mailem, který si vypíšete, sekundární e-mail uživatele. V takovém případě se zobrazí chybová zpráva s primárním e-mailem pro účet, u kterého došlo k selhání. Tento primární e-mail přidejte do seznamu ručních výjimek, až ověříte, že znáte uživatele.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k abstraktnímu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **abstract**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-abstract-test-user"></a>Vytvořit abstraktního testovacího uživatele

Postup při testování jednotného přihlašování na abstraktních počítačích:

1. Otevřete [abstraktní webovou aplikaci](https://app.abstract.com/).
2. Přejít na stránku **oprávnění** na levém panelu.
3. Klikněte na **test s mým účtem**. Pokud se test nezdařil, [obraťte se na náš tým podpory](https://help.abstract.com/hc/).

>[!Note]
>Pro přístup k nastavení jednotného přihlašování na abstraktu se budete muset ověřit pomocí účtu správce organizace.
Tento účet správce organizace bude muset být přiřazený k abstraktnímu na Azure Portal.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici abstraktu na přístupovém panelu byste měli být automaticky přihlášeni k abstrakci, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)