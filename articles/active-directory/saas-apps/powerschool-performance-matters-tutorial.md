---
title: 'Kurz: Azure Active Directory integrace s Powerschoolmi výkonem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Powerschoolmi otázkami výkonu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 7b75e2cbffaaf05dc0f5ca30497c165b91adf6d1
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515341"
---
# <a name="tutorial-azure-active-directory-integration-with-powerschool-performance-matters"></a>Kurz: Azure Active Directory integrace s Powerschoolmi otázkami o výkonu

V tomto kurzu se dozvíte, jak integrovat PowerSchool výkon pomocí Azure Active Directory (Azure AD). Při integraci Powerschoolch aspektů výkonu s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k Powerschoolm aspektům výkonu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Powerschoolm aspektům výkonu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* PowerSchool výkon s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Otázky týkající se výkonu PowerSchool podporují jednotné přihlašování s využitím **SP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-powerschool-performance-matters-from-the-gallery"></a>Přidání aspektů výkonu PowerSchool z Galerie

Pokud chcete nakonfigurovat integraci Powerschoolch aspektů výkonu do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat PowerSchool otázky výkonu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **PowerSchool Performance** .
1. Z panelu výsledků vyberte **PowerSchool výkonnostní otázky** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-powerschool-performance-matters"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Powerschoolé otázky týkající se výkonu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Form.com pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Form.com.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Form.com postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat](#configure-powerschool-performance-matters-sso)** jednotné přihlašování PowerSchool v otázkách – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte si testovacího uživatele v oblasti výkonu PowerSchool](#create-powerschool-performance-matters-test-user)** , abyste měli protějšek B. Simon v otázkách výkonu PowerSchool, které jsou propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **PowerSchool Performance** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující krok:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:
    
    ```https
        https://ola.performancematters.com/ola/?clientcode=<Client Code>
        https://unify.performancematters.com/?idp=<IDP>
    ```

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [tým podpory PowerSchool výkonu](mailto:pmsupport@powerschoo.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení aspektů výkonu PowerSchool** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k otázkám týkajícím se výkonu PowerSchool.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **PowerSchool výkonnostní otázky**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-powerschool-performance-matters-sso"></a>Konfigurace PowerSchool v oblasti výkonu pro jednotné přihlašování

Chcete-li nakonfigurovat jednotné přihlašování na straně **PowerSchool výkonu** , je třeba odeslat stažená **metadata federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory v oblasti výkonu PowerSchool](mailto:pmsupport@powerschoo.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-powerschool-performance-matters-test-user"></a>Vytvoření testovacího uživatele v oblasti výkonu PowerSchool

V této části vytvoříte v otázkách výkonu PowerSchool uživatele s názvem Britta Simon. Pracujte s [PowerSchool výkonem podpory v oblasti výkonu](mailto:pmsupport@powerschoo.com) , abyste mohli přidávat uživatele na platformě Powerschoolch věcí pro výkon. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na PowerSchool výkonové přihlašovací adresy URL, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici PowerSchool výkon v nabídce Moje aplikace, přesměruje se na adresu URL týkající se přihlašování Powerschoolch otázek výkonu. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete otázky týkající se výkonu PowerSchool, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).