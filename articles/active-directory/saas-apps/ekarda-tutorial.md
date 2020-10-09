---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s ekarda | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: cfd7a007c8f26a96d929026e231cb412cdc70279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440843"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ekarda

V tomto kurzu se dozvíte, jak integrovat ekarda s Azure Active Directory (Azure AD). Když integrujete ekarda s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ekarda.
* Umožněte uživatelům, aby se do ekarda automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je jednotné přihlašování (SSO)?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ekarda je povolené pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ekarda podporuje jednotné přihlašování iniciované v SP a IDP.
* ekarda podporuje zřizování uživatelů za běhu.
* Po nakonfigurování ekarda můžete vynutili řízení relace. Tato preventivní opatření chrání před exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšíří z Řízení podmíněného přístupu k aplikacím. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-ekarda-from-the-gallery"></a>Přidání ekarda z Galerie

Pokud chcete nakonfigurovat integraci ekarda do služby Azure AD, přidejte ekarda z Galerie do svého seznamu spravovaných aplikací pro SaaS:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ekarda** .
1. Na panelu výsledků vyberte **ekarda** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ekarda

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ekarda pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v ekarda.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ekarda, proveďte následující kroky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.

    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. Nakonfigurujte jednotné přihlašování [EKARDA SSO](#configure-ekarda-sso) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * [Vytvořte ekarda testovacího uživatele](#create-an-ekarda-test-user) , který bude mít protějšek B. Simon v ekarda, který je propojený s zastoupením uživatele Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování Azure AD, postupujte podle těchto kroků Azure Portal:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
1. Na stránce integrace aplikací **ekarda** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky a upravte základní nastavení **Konfigurace SAML** .

   ![Snímek obrazovky s nastavením jednoduchého Sign-On se stránkou SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud se v části **základní konfigurace SAML** zobrazuje **soubor metadat poskytovatele služby**, postupujte podle těchto kroků:
    1. Vyberte **nahrát soubor metadat**.
    1. Vyberte ikonu složky a vyberte soubor metadat a pak vyberte **nahrát**.
    1. Po úspěšném nahrání souboru metadat se v textovém poli ekarda oddílu automaticky zobrazí hodnoty pro **identifikátor** a **adresu URL odpovědi** .

    > [!Note]
    > Pokud se hodnoty **adresy URL** **identifikátoru** a odpovědi nezobrazí automaticky, vyplňte hodnoty ručně podle vašich požadavků.

1. Pokud nevidíte **soubor metadat poskytovatele služby** v **základní části Konfigurace SAML** a chcete aplikaci nakonfigurovat v režimu iniciované IDP, zadejte hodnoty pro následující pole:

    1. Do textového pole **identifikátor** zadejte adresu URL, která následuje po tomto vzoru: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která následuje po tomto vzoru: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Chcete-li nakonfigurovat aplikaci v režimu iniciované v režimu SP, vyberte možnost **nastavit další adresy URL** a postupujte takto:

    * Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která následuje po tomto vzoru: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Hodnoty v obou předchozích krocích nejsou reálné. Aktualizujte je skutečným identifikátorem, adresou URL odpovědi a hodnotami přihlašovací adresy URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta ekarda](mailto:contact@ekarda.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** a uložte **certifikát (Base64)** do počítače.

    ![Snímek obrazovky s oddílem podpisového certifikátu SAML na stránce nastavit jeden Sign-On se stránkou SAML se zvýrazněným odkazem na stažení pro certifikát base64.](common/certificatebase64.png)

1. V části **Nastavení ekarda** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Snímek obrazovky s oddílem nastavit ekarda na stránce nastavit jeden Sign-On se stránkou SAML s zvýrazněnými odkazy pro kopírování URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části použijete Azure Portal k vytvoření testovacího uživatele s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Zadejte například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ekarda.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **ekarda**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky s oddílem spravovat se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Snímek obrazovky části Uživatelé a skupiny se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud v kontrolním výrazu SAML očekáváte jakoukoli hodnotu role, vyberte příslušnou roli uživatele ze seznamu v dialogovém okně **Vybrat roli** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-ekarda-sso"></a>Konfigurace jednotného přihlašování ekarda

1. V jiném okně webového prohlížeče se přihlaste k webu ekarda společnosti jako správce.
1. Vyberte **správce**  >  **můj účet**.

    ![Snímek uživatelského rozhraní webu ekarda se zvýrazněným mým účtem v nabídce správce](./media/ekarda-tutorial/ekarda.png)

1. V dolní části stránky najděte část **Nastavení SAML** . V této části nakonfigurujete integraci SAML.
1. V části **Nastavení SAML** postupujte takto:

    ![Snímek obrazovky ekarda stránky nastavení SAML s zvýrazněnými konfiguračními poli SAML](./media/ekarda-tutorial/ekarda1.png)

    1. Vyberte odkaz **metadata poskytovatele služeb** a uložte ho jako soubor ve vašem počítači.
    1. Zaškrtněte políčko **Povolit SAML** .
    1. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali dříve z Azure Portal.
    1. Do textového pole **Adresa URL pro přihlášení IDP** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali dříve z Azure Portal.
    1. Do textového pole **Adresa URL pro odhlášení IDP** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali dříve z Azure Portal.
    1. Pomocí poznámkového bloku otevřete soubor **certifikátu (Base64)** , který jste stáhli z Azure Portal. Do textového pole **certifikát IDP x509** vložte tento obsah.
    1. Zaškrtněte políčko **Povolit slo** v části **Možnosti** .
    1. Vyberte **Aktualizovat**.

### <a name="create-an-ekarda-test-user"></a>Vytvořit testovacího uživatele v ekarda

V této části se v ekarda vytvoří uživatel s názvem B. Simon. ekarda podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. Nemáte žádnou akci, která by se měla provést v této části. Pokud uživatel s názvem B. Simon ještě v ekarda neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když na portálu moje aplikace vyberete dlaždici ekarda, měli byste se automaticky přihlásit k webu ekarda, pro který jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu [Úvod do portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
* [Vyzkoušejte si ekarda s Azure AD](https://aad.portal.azure.com/)
* Pomocí [řešení Enterprise eCard v ekarda](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) můžete zřídit libovolného počtu vašich zaměstnanců, kteří budou posílat eCardsy s logem vaší společnosti i jejich klientů a kolegům. Přečtěte si další informace o [zřizování ekarda jako řešení jednotného přihlašování](https://support.ekarda.com/#SSO-Implementation).
* [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Jak chránit ekarda pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
