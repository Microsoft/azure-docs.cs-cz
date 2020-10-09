---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s iProva | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iProva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 187ec5c42c733837689de95c8111b557519f8b7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552832"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s iProva

V tomto kurzu se dozvíte, jak integrovat iProva s Azure Active Directory (Azure AD). Když integrujete iProva s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k iProva.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k iProva svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* iProva odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* iProva podporuje jednotné přihlašování iniciované v **SP**

* Po nakonfigurování iProva můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Přidání iProva z Galerie

Pokud chcete nakonfigurovat integraci iProva do služby Azure AD, musíte přidat iProva z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **iProva** .
1. Na panelu výsledků vyberte **iProva** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Konfigurace a testování jednotného přihlašování Azure AD pro iProva

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí iProva pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v iProva.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí iProva, dokončete následující stavební bloky:

1. **[Načtěte informace o konfiguraci z iProva](#retrieve-configuration-information-from-iprova)** – jako přípravu pro další kroky.
1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Vytvořte iProva Test User](#create-iprova-test-user)** -to, abyste měli protějšek B. Simon v iProva, která je propojená s reprezentací uživatele v Azure AD.
1. **[Nakonfigurujte IPROVA SSO](#configure-iprova-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="retrieve-configuration-information-from-iprova"></a>Načíst informace o konfiguraci z iProva

V této části načtete informace z iProva ke konfiguraci jednotného přihlašování Azure AD.

1. Otevřete webový prohlížeč a na stránce **typu Saml2 informace** v iProva použijte následující vzor adresy URL:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![Zobrazit informační stránku iProva typu Saml2](media/iprova-tutorial/iprova-saml2-info.png)

1. Ponechte kartu prohlížeče otevřenou a pokračujte dalšími kroky na jiné kartě prohlížeče.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **IProva** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do pole **Adresa URL pro přihlášení** zadejte hodnotu, která se zobrazuje za **přihlašovací adresou URL** popisku na stránce **iProva typu Saml2 info** . Tato stránka je stále otevřená na jiné kartě prohlížeče.

    b. Vyplňte pole **identifikátor** hodnotou, která se zobrazuje za jmenovkou **EntityId** na stránce **iProva typu Saml2 info** . Tato stránka je stále otevřená na jiné kartě prohlížeče.

    c. Do pole **Adresa URL odpovědi** zadejte hodnotu, která se zobrazuje za **adresou URL odpovědi** na štítku na stránce **informace o iProva typu Saml2** . Tato stránka je stále otevřená na jiné kartě prohlížeče.

1. iProva aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace iProva několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut| Obor názvů  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

## <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k iProva.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **iProva**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="create-iprova-test-user"></a>Vytvořit testovacího uživatele iProva

1. Přihlaste se k iProva pomocí účtu **správce** .

2. Otevřete nabídku **Přejít na** .

3. Vyberte možnost **Správa aplikací**.

4. Na panelu **Uživatelé a skupiny uživatelů** vyberte **Uživatelé** .

5. Vyberte **Přidat**.

6. Do pole **uživatelské jméno** zadejte uživatelské jméno, jako třeba `B.Simon@contoso.com` .

7. Do pole **jméno a příjmení** zadejte celé jméno uživatele (například **B. Simon**).

8. Vyberte možnost **bez hesla (použít jednotné přihlašování)** .

9. Do pole **e-mailová adresa** zadejte e-mailovou adresu uživatele `B.Simon@contoso.com` .

10. Přejděte dolů na konec stránky a vyberte **Dokončit**.

## <a name="configure-iprova-sso"></a>Konfigurace jednotného přihlašování iProva

1. Přihlaste se k iProva pomocí účtu **správce** .

2. Otevřete nabídku **Přejít na** .

3. Vyberte možnost **Správa aplikací**.

4. Na panelu **nastavení systému** vyberte **Obecné** .

5. Vyberte **Upravit**.

6. Přejděte dolů na **řízení přístupu**.

    ![nastavení řízení přístupu iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Vyhledejte nastavení **Uživatelé se automaticky přihlásí pomocí jejich síťových účtů**a změňte je na **Ano, ověřování prostřednictvím SAML**. Nyní se zobrazí další možnosti.

8. Vyberte možnost **Nastavit**.

9. Vyberte **Další**.

10. iProva se zeptá, jestli chcete stáhnout federační data z adresy URL nebo odeslat ze souboru. Vyberte možnost **z adresy URL** .

    ![Stažení metadat Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Vložte adresu URL metadat, kterou jste uložili v posledním kroku oddílu "konfigurace jednotného přihlašování Azure AD".

12. Vyberte tlačítko se tvarem šipky a Stáhněte si metadata z Azure AD.

13. Po dokončení stahování se zobrazí zpráva s potvrzením, že se **stáhl platný soubor federačních dat** .

14. Vyberte **Další**.

15. Pro nyní vynechejte možnost **testovat přihlášení** a vyberte možnost **Další**.

16. V rozevíracím seznamu **deklarace k použití** vyberte **atributech uživatelů windowsaccountname**.

17. Vyberte **Dokončit**.

18. Nyní se vrátíte na obrazovku **Upravit obecné nastavení** . Posuňte se dolů k dolní části stránky a kliknutím na **OK** uložte svou konfiguraci.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici iProva, měli byste se automaticky přihlásit k iProva, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si iProva s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit iProva pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)