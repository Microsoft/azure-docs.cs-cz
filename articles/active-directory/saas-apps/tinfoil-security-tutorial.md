---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k zabezpečení TINFOIL | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ZABEZPEČENÍm TINFOIL.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 5c2ac2c7bb1b60c87075a1bc62241edab2fc310e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516286"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k zabezpečení TINFOIL

V tomto kurzu se dozvíte, jak integrovat zabezpečení TINFOIL s Azure Active Directory (Azure AD). Když integrujete zabezpečení TINFOIL s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k zabezpečení TINFOIL.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TINFOIL zabezpečení pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TINFOIL předplatného jednotného přihlašování (SSO) s povoleným ZABEZPEČENÍm.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TINFOIL SECURITY podporuje **IDP** iniciované jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Přidání zabezpečení TINFOIL z Galerie

Pokud chcete nakonfigurovat integraci zabezpečení TINFOIL do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zabezpečení TINFOIL z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TINFOIL Security** .
1. Z panelu výsledků vyberte **TINFOIL Security** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Konfigurace a testování jednotného přihlašování Azure AD pro zabezpečení TINFOIL

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s TINFOIL SECURITY pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v zabezpečení TINFOIL.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s TINFOIL SECURITY, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[zabezpečení TINFOIL](#configure-tinfoil-security-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte TINFOIL test Security User](#create-tinfoil-security-test-user)** -to, abyste měli protějšek B. Simon v TINFOIL zabezpečení, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **zabezpečení TINFOIL** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Navštěvovaná aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace v této části očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut |
    | ------------------- | -------------|
    | accountId | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Hodnota ID účtu se vysvětluje později v tomto kurzu.

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **hodnotu kryptografického otisku** a uložte ji do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **nastavení zabezpečení TINFOIL** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k zabezpečení TINFOIL.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **zabezpečení TINFOIL**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-tinfoil-security-sso"></a>Konfigurace jednotného přihlašování k TINFOIL zabezpečení

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti TINFOIL SECURITY jako správce.

1. Na panelu nástrojů v horní části klikněte na **můj účet**.

    ![Řídicí panel](./media/tinfoil-security-tutorial/ic798971.png "Řídicí panel")

1. Klikněte na **zabezpečení**.

    ![Zabezpečení](./media/tinfoil-security-tutorial/ic798972.png "Zabezpečení")

1. Na stránce konfigurace **jednotného přihlašování** proveďte následující kroky:

    ![Jednotné přihlašování](./media/tinfoil-security-tutorial/ic798973.png "Jednotné přihlašování")

    a. Vyberte **Povolit SAML**.

    b. Klikněte na **Ruční konfigurace**.

    c. Do textového pole **Adresa URL příspěvku SAML** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal

    d. Do textového pole **otisku certifikátu SAML** vložte hodnotu **kryptografického otisku** , který jste zkopírovali z části **podpisový certifikát SAML** .
  
    e. Zkopírujte hodnotu **ID účtu** a vložte hodnotu do textového pole **atribut zdroje** v části **atributy uživatele & deklarace identity** v Azure Portal.

    f. Klikněte na **Uložit**.

### <a name="create-tinfoil-security-test-user"></a>Vytvořit uživatele testu zabezpečení TINFOIL

Aby se uživatelé Azure AD mohli přihlašovat k zabezpečení TINFOIL, musí se zřídit do zabezpečení TINFOIL. V případě zabezpečení TINFOIL je zřizování ručním úkolem.

**Pokud chcete získat zřízeného uživatele, proveďte následující kroky:**

1. Pokud je uživatel součástí podnikového účtu, budete se muset [obrátit na tým podpory TINFOIL Security Support](https://www.tinfoilsecurity.com/contact) , který získá vytvořený uživatelský účet.

1. Pokud je uživatel běžným uživatelem TINFOIL SECURITY SaaS, může uživatele přidat spolupracovníka do libovolného z webů uživatele. Tím se aktivuje proces pro odeslání pozvánky k zadanému e-mailu, aby se vytvořil nový účet uživatele TINFOIL SECURITY.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů a rozhraní API pro TINFOIL zabezpečení, které poskytuje zabezpečení TINFOIL.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici zabezpečení TINFOIL na přístupovém panelu, měli byste se automaticky přihlásit k TINFOIL zabezpečení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si TINFOIL zabezpečení pomocí Azure AD](https://aad.portal.azure.com/)