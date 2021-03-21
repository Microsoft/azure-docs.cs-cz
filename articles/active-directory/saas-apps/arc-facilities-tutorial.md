---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí zařízení ARC | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directorymi a OBLOUKOVÉmi zařízeními.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 2ca26d9730c335023864c071657522270880f74a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735423"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-facilities"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí zařízení ARC

V tomto kurzu se dozvíte, jak integrovat zařízení ARC pomocí Azure Active Directory (Azure AD). Při integraci funkcí ARC s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k zařízením ARC.
* Umožněte uživatelům, aby se do zařízení ARC automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr zařízení s podporou jednotného přihlašování (SSO) s podporou ARC

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zařízení ARC podporují jednotné přihlašování (SSO) iniciované **IDP**

* Zařízení ARC podporují zřizování uživatelů **jenom v čase**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-arc-facilities-from-the-gallery"></a>Přidávání zařízení ARC z Galerie

Pokud chcete nakonfigurovat integraci zařízení ARC do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zařízení ARC z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte funkce **ARC** .
1. Vyberte možnost **zařízení ARC** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-arc-facilities"></a>Konfigurace a testování jednotného přihlašování Azure AD pro zařízení ARC

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí zařízení ARC pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v zařízeních ARC.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zařízení ARC, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování zařízení ARC](#configure-arc-facilities-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele pro zařízení ARC](#create-arc-facilities-test-user)** – bude mít protějšek B. Simon v zařízeních ARC, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikací **zařízení ARC** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Aplikace zařízení ARC očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![Snímek obrazovky se zobrazí dialogové okno atributy uživatele s ikonou pro úpravy s názvem.](common/edit-attribute.png)

1. Kromě výše očekává aplikace ARC v odpovědi SAML několik atributů, které se mají zpětně předat. V dialogovém okně deklarace identity v části **atributy uživatele & deklarace** v dialogu **deklarace skupiny (Preview)** proveďte následující kroky:

    a. Klikněte na **pero** vedle **skupin vrácených v deklaraci identity**.

    ![Snímek obrazovky ukazuje atributy uživatele & deklarace identity pomocí pera vedle skupin vrácených v deklaraci identity.](./media/arc-facilities-tutorial/config01.png)

    ![Snímek obrazovky zobrazuje deklarace skupin se všemi vybranými skupinami a vybranými skupinami a tlačítkem Uložit.](./media/arc-facilities-tutorial/config02.png)

    b. V seznamu přepínačů vyberte **všechny skupiny** .

    c. Vyberte **zdrojový atribut** **ID skupiny**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Zařízení ARC očekává role pro uživatele přiřazené k aplikaci. Nastavte prosím tyto role ve službě Azure AD, aby bylo možné uživatelům přiřadit příslušné role. Informace o tom, jak nakonfigurovat role v Azure AD, najdete [tady](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení zařízení ARC** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k zařízením ARC.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **zařízení ARC**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-arc-facilities-sso"></a>Nakonfigurovat jednotné přihlašování zařízení ARC

Chcete-li nakonfigurovat jednotné přihlašování na straně **ARC** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory zařízení ARC](mailto:support@arcfacilities.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-arc-facilities-test-user"></a>Vytvořit testovacího uživatele pro zařízení ARC

V této části se v zařízeních ARC vytvoří uživatel s názvem Britta Simon. Zařízení ARC podporují zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v zařízeních ARC neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k zařízením ARC, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici zařízení ARC v okně moje aplikace byste měli být automaticky přihlášeni k zařízením ARC, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po konfiguraci zařízení ARC můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
