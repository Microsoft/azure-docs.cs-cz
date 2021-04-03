---
title: 'Kurz: Azure Active Directory integrace s prostředky infrastruktury zabezpečení cloudu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a prostředky infrastruktury zabezpečení cloudu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.openlocfilehash: 7025b948615efcf6ace3ca0fb6a2daecdd75c702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456033"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Kurz: integrace prostředků infrastruktury cloudového zabezpečení s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat infrastruktury zabezpečení cloudu s Azure Active Directory (Azure AD). Při integraci prostředků infrastruktury cloudového zabezpečení s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k prostředkům infrastruktury zabezpečení cloudu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k prostředkům infrastruktury cloudového zabezpečení pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povolený jednotného přihlašování (SSO) Cloud Security Fabric.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Prostředky infrastruktury zabezpečení cloudu podporují jednotné přihlašování v rámci **SP**

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Přidání prostředků infrastruktury cloudového zabezpečení z Galerie

Ke konfiguraci integrace prostředků infrastruktury cloudového zabezpečení do služby Azure AD je nutné přidat prostředky infrastruktury Cloud Security z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **prostředky infrastruktury Cloud Security** .
1. Vyberte **prostředek Cloud Security Fabric** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí prostředků infrastruktury zabezpečení cloudu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v prostředcích infrastruktury zabezpečení cloudu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí prostředků infrastruktury zabezpečení cloudu, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování infrastruktury cloudového zabezpečení](#configure-the-cloud-security-fabric-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele prostředků infrastruktury cloudového zabezpečení](#create-the-cloud-security-fabric-test-user)** , abyste měli protějšek B. Simon v prostředcích infrastruktury zabezpečení cloudu, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Fabric Security Fabric** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL:

      ```http
      https://platform.cloudlock.com
      https://app.cloudlock.com
      ```

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

      ```http
      https://platform.cloudlock.com/gate/saml/sso/<subdomain>
      https://app.cloudlock.com/gate/saml/sso/<subdomain>
      ```

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizujte hodnotu skutečným identifikátorem. Pokud chcete získat hodnotu, obraťte [se na tým podpory pro klienta podpory Cloud Security Fabric](mailto:support@cloudlock.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

5. Chcete-li upravit možnosti **podepisování** podle vašich požadavků, klikněte na tlačítko **Upravit** a otevřete dialogové okno **podpisový certifikát SAML** .

    ![Odpověď SAML](./media/ciscocloudlock-tutorial/saml.png)

    a. Pro **možnost podepisování** vyberte možnost **podepsat odpověď SAML a kontrolní výraz** .

    b. Pro **podpisový algoritmus** vyberte možnost **SHA-256** .

    c. Klikněte na **Uložit**.  

6. V části **Nastavení prostředky infrastruktury cloudového zabezpečení** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>Konfigurace jednotného přihlašování prostředků infrastruktury cloudového zabezpečení

Pokud chcete nakonfigurovat jednotné přihlašování na straně **prostředků infrastruktury zabezpečení cloudu** , je potřeba odeslat stažený **kód XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory pro cloudový Security Fabric](mailto:support@cloudlock.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.
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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k prostředkům infrastruktury Cloud Security.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **prostředky infrastruktury zabezpečení cloudu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-the-cloud-security-fabric-test-user"></a>Vytvoření testovacího uživatele prostředků infrastruktury cloudového zabezpečení

V této části vytvoříte uživatele s názvem B. Simon v prostředcích infrastruktury zabezpečení cloudu. Pokud chcete přidat uživatele na platformě Cloud Security Fabric, pracujte s [týmem podpory infrastruktury zabezpečení cloudu](mailto:support@cloudlock.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici prostředky Cloud Security Fabric na přístupovém panelu byste se měli automaticky přihlášeni k prostředkům infrastruktury cloudového zabezpečení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)