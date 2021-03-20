---
title: 'Kurz: Azure Active Directory integrace s Coupa | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652976"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Kurz: Azure Active Directory integrace s Coupa

V tomto kurzu se dozvíte, jak integrovat Coupa s Azure Active Directory (Azure AD). Když integrujete Coupa s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Coupa.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Coupa svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Coupa odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Coupa podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-coupa-from-the-gallery"></a>Přidání Coupa z Galerie

Pokud chcete nakonfigurovat integraci Coupa do služby Azure AD, musíte přidat Coupa z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Coupa** .
1. Na panelu výsledků vyberte **Coupa** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Coupa

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Coupa pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Coupa.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Coupa postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte COUPA SSO](#configure-coupa-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Coupa testovacího uživatele](#create-coupa-test-user)** – abyste měli protějšek B. Simon inCoupa, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Coupa** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta Coupa](https://success.coupa.com/Support/Contact_Us?) .

    b. Do pole **identifikátor** zadejte adresu URL:

    | Prostředí  | URL |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Výroba | `sso-prd1.coupahost.com`|
    | | |

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL:

    | Prostředí | URL |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Výroba | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Coupa** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Coupa.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Coupa**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-coupa-sso"></a>Konfigurace jednotného přihlašování Coupa

1. Přihlaste se k webu Coupa společnosti jako správce.

2. Přejít k **\> ovládacímu prvku zabezpečení instalace**.

    ![Ovládací prvky zabezpečení](./media/coupa-tutorial/setup.png "Zabezpečovací mechanismy")

3. V části **přihlášení pomocí přihlašovacích údajů Coupa** proveďte následující kroky:

    ![Metadata Coupa SP](./media/coupa-tutorial/login.png "Metadata Coupa SP")

    a. Vyberte možnost **Přihlásit se pomocí SAML**.

    b. Kliknutím na tlačítko **Procházet** nahrajte metadata stažená z Azure Portal.

    c. Klikněte na **Uložit**.

### <a name="create-coupa-test-user"></a>Vytvořit testovacího uživatele Coupa

Aby se uživatelé Azure AD mohli přihlašovat k Coupa, musí se zřídit v Coupa.  

* V případě Coupa je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu společnosti **Coupa** jako správce.

2. V nabídce v horní části klikněte na **Nastavení** a potom klikněte na **Uživatelé**.

    ![Uživatelé](./media/coupa-tutorial/user.png "Uživatelé")

3. Klikněte na **Vytvořit**.

    ![Vytvoření uživatelů](./media/coupa-tutorial/create.png "Vytváření uživatelů")

4. V části **Vytvoření uživatele** proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/coupa-tutorial/details.png "Podrobnosti o uživateli")

    a. Do příslušných textových polí zadejte **přihlašovací** jméno, **jméno**, **příjmení, jméno** a **jedno Sign-On**, atributy **e-mailu** platného Azure Active Directory účtu, který chcete zřídit.

    b. Klikněte na **Vytvořit**.

    >[!NOTE]
    >Držitel účtu Azure Active Directory získá e-mail s odkazem na potvrzení účtu před jeho aktivním použitím.
    >

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Coupa nebo rozhraní API poskytovaná Coupa.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Coupa, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Coupa přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Coupa v okně moje aplikace, měli byste se automaticky přihlásit k Coupa, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Coupa můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)