---
title: 'Kurz: Azure Active Directory integrace s osobním portálem Mimecast | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a osobním portálem Mimecast.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: c14a519be112e61ed7055debe7a1a454e465bd9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549088"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí osobního portálu Mimecast

V tomto kurzu se dozvíte, jak integrovat osobní portál Mimecast s Azure Active Directory (Azure AD). Při integraci osobního portálu Mimecast s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k osobnímu portálu Mimecast.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Mimecast osobním portálu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Mimecast osobního portálu s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mimecast Personal Portal podporuje jednotné přihlašování (SSO) **a IDP** .
* Po nakonfigurování osobního portálu Mimecast můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Přidání osobního portálu Mimecast z Galerie

Pokud chcete nakonfigurovat integraci osobního portálu Mimecast do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat osobní portál Mimecast z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Mimecast Personal Portal** .
1. Z panelu výsledků vyberte **osobní portál Mimecast** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>Konfigurace a testování jednotného přihlašování Azure AD pro osobní portál Mimecast

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí osobního portálu Mimecast pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem na osobním portálu Mimecast.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí osobního portálu Mimecast, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování osobního portálu Mimecast](#configure-mimecast-personal-portal-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele osobního portálu Mimecast](#create-mimecast-personal-portal-test-user)** , abyste měli protějšek B. Simon na osobním portálu Mimecast, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **osobního portálu Mimecast** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované IDP, proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Oblast  |  Hodnota | 
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | USA   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Jižní Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrálie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Prací        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Tato hodnota se nachází `accountcode` na osobním portálu Mimecast v části **Account**  >  **Settings**  >  **Kód účtu**nastavení účtu. Připojí `accountcode` k identifikátoru.

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL:

    | Oblast  |  Hodnota |
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Prací        | `https://jer-api.mimecast.com/login/saml`|

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:

    | Oblast  |  Hodnota |
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Jižní Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Prací        | `https://jer-api.mimecast.com/login/saml`|

1. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k osobnímu portálu Mimecast.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **osobní portál Mimecast**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-mimecast-personal-portal-sso"></a>Konfigurace jednotného přihlašování k osobním portálu Mimecast

1. V jiném okně webového prohlížeče se přihlaste ke konzole pro správu Mimecast.

1. Přejděte na **aplikace pro správu**  >  **Services**  >  **Applications**.

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/services.png)

1. Klikněte na kartu **profily ověřování** .
    
    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Klikněte na **Nová karta profil ověřování** .

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Do textového pole **Popis** zadejte platný popis a zaškrtněte políčko **vyhovět ověřování SAML pro Mimecast osobního portálu** .

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Na stránce **Konfigurace SAML pro osobní portál Mimecast** proveďte následující kroky:

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. V rozevíracím seznamu **poskytovatel**vyberte **Azure Active Directory** .

    b. Do textového pole **Adresa URL metadat** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **importovat**. Po importu adresy URL metadat budou pole naplněna automaticky, takže v těchto polích není nutné provádět žádné akce.

    d. Ujistěte se, že jste zaškrtli políčko **použít kontext chráněný heslem** a **použijete kontexty integrovaného ověřování** .

    e. Klikněte na **Uložit**.

### <a name="create-mimecast-personal-portal-test-user"></a>Vytvořit testovacího uživatele osobního portálu Mimecast

1. V jiném okně webového prohlížeče se přihlaste ke konzole pro správu Mimecast.

1. Přejděte do **složky pro správu**  >  **Directories**  >  **interní adresáře**.

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Pokud je doména uvedená níže, vyberte v doméně. v opačném případě vytvořte novou doménu kliknutím na **novou doménu**.

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Klikněte na kartu **Nová adresa** .

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Zadejte požadované informace o uživateli na následující stránce:

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele, jako je `B.Simon@yourdomainname.com` .

    b. Do textového pole **globální název** zadejte jméno a **příjmení** uživatele.

    c. Do textových polí **heslo** a **potvrzení hesla** zadejte heslo uživatele.

    d. Zaškrtněte políčko **Vynutit změnu při přihlášení** .

    e. Klikněte na **Uložit**.

    f. Pokud chcete přiřadit role uživateli, klikněte na **Upravit roli** a přiřaďte požadovanou roli uživateli podle požadavků vaší organizace.

    ![Konfigurace osobního portálu Mimecast](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici osobního portálu Mimecast na přístupovém panelu byste měli být automaticky přihlášeni k osobnímu portálu Mimecast, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si osobní portál Mimecast s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Ochrana osobního portálu Mimecast pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
