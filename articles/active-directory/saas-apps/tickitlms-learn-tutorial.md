---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s využitím TickitLMSe | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TickitLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b37a5332e7c8f21d4309102b6b111953c1765ea6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tickitlms-learn"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s TickitLMS

V tomto kurzu se dozvíte, jak integrovat TickitLMS s využitím Azure Active Directory (Azure AD). Při integraci TickitLMSch informací s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k TickitLMS.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TickitLMSi s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TickitLMS se dozvědět předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TickitLMS s podporou pro jednotné přihlašování **a IDP**

## <a name="adding-tickitlms-learn-from-the-gallery"></a>Přidávání TickitLMSch informací z Galerie

Pokud chcete nakonfigurovat integraci TickitLMSch informací do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat TickitLMS informace z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte **TickitLMS informace** do vyhledávacího pole.
1. Na panelu výsledků vyberte **TickitLMS informace** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-tickitlms-learn"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro TickitLMS

Konfigurace a testování jednotného přihlašování Azure AD pomocí TickitLMS s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TickitLMS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TickitLMS, postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace TickitLMS](#configure-tickitlms-learn-sso)** s použitím jednotného přihlašování – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytváření TickitLMSch testovacích uživatelů](#create-tickitlms-learn-test-user)** – Pokud chcete mít protějšek B. Simon v TickitLMS, který se odkazuje na reprezentaci uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce TickitLMS – **informace** o integraci aplikace najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https:/learn.tickitlms.com/sso/login`

1. Klikněte na **Uložit**.

1. Aplikace TickitLMS učí očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše uvedeného aplikace TickitLMS učí očekává, že se v odpovědi SAML vrátí jenom několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name | Zdrojový atribut|
    | ---- | --------------- |
    | samlaccount | User. samlaccount |
    | zaměstnance | User. ČísloZaměstnance |
    | role | uživatel. role |
    | Oddělení | User. Department |
    | reportsto | User. reportsto |

    > [!NOTE]
    > TickitLMS se očekává, že role pro uživatele přiřazené k aplikaci. Nastavte prosím tyto role ve službě Azure AD, aby bylo možné uživatelům přiřadit příslušné role. Informace o tom, jak nakonfigurovat role v Azure AD, najdete [tady](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)
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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TickitLMS.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TickitLMS informace**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-tickitlms-learn-sso"></a>Konfigurace TickitLMS – seznámit se SSO

Pokud chcete nakonfigurovat jednotné přihlašování na **TickitLMS** , musíte odeslat **adresu URL federačních metadat aplikace** , aby se mohl [TickitLMS naučit tým podpory](mailto:support@tickitlms.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-tickitlms-learn-test-user"></a>Vytvořit TickitLMS s testovacím uživatelem

V této části vytvoříte ve TickitLMS učení uživatele s názvem Britta Simon. Pokud chcete přidat uživatele na platformě TickitLMS učení, pracujte s [týmem podpory TickitLMS](mailto:support@tickitlms.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na TickitLMS, kde se dozvíte, na adrese URL, kde můžete spustit tok přihlášení.  

* Přejít na TickitLMS získat přímo přihlašovací adresu URL a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlásit k TickitLMSům, pro které jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici TickitLMS informace v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k TickitLMS, pro které jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování TickitLMS se dozvíte, jak vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
