---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s výstupem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a odchozím výstupem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 392697978041cdab50f45e22a73acbadb9122546
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519408"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egress"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s odchozím výstupem

V tomto kurzu se dozvíte, jak integrovat výstup pomocí Azure Active Directory (Azure AD). Při integraci odchozích dat se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k odchozímu přenosu.
* Umožněte uživatelům, aby se automaticky přihlásili k odchozímu přenosu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povolenou podporou jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Výstup podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Odchozí přenos dat podporuje **jenom v době** zřizování uživatelů.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-egress-from-the-gallery"></a>Přidat výstup z Galerie

Pokud chcete nakonfigurovat integraci odchozího přenosu do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat výstup z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **výstup** .
1. Na panelu výsledků vyberte **výstup** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-egress"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro odchozí přenosy

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s odchozím použitím pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v odchozím přenosu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí odchozího přenosu dat, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace](#configure-egress-sso)** fulltextového přihlášení – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. Vytvořte si nenáročného **[testovacího uživatele](#create-egress-test-user)** , abyste měli protějšek B. Simon v odchozím přenosu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací pro **výstup** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://switch.egress.com/ui/`

1. Klikněte na **Uložit**.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k odchozímu přenosu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **odchozí**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-egress-sso"></a>Konfigurace odchozího přihlašování

1. V jiném okně prohlížeče se přihlaste k vašemu výstupnímu webu společnosti jako správce.

1. Na následující stránce proveďte následující kroky.

    ![Konfigurace odchozího přenosu dat](./media/egress-tutorial/configure-1.PNG)

    a. V nabídce na levé straně klikněte na **Konfigurace jednotného přihlašování**.

    b. Pokud chcete použít jednotné přihlašování, vyberte možnost **použít přepínač jednotného přihlašování** .

    c. Zadejte platný popis v textovém poli **Popis poskytovatele** .

    d. Do textového pole **Adresa URL metadat** vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali.

    e. Klikněte na **načíst metadata**.

    f. Kliknutím na tlačítko **Uložit** aktualizujte konfiguraci jednotného přihlašování.

### <a name="create-egress-test-user"></a>Vytvořit odchozího testovacího uživatele

1. Přihlaste se k vašemu **výstupnímu** webu společnosti.

1. Kliknutím na **pozvat uživatele** v nabídce vlevo a kliknutím na **pozvat jednoho uživatele** přidejte uživatele.

    ![Snímek obrazovky, na které se zobrazí stránka pozvat uživatele s vybraným tlačítkem pozvat jednoho uživatele](./media/egress-tutorial/create-user-1.PNG)

1. Vyplňte požadovaná pole a klikněte na **pozvat**.

    ![Výstup – vytvořit testovacího uživatele](./media/egress-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro odchozí přihlášení, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k odchozímu přenosu přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k odchozímu přenosu, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici výstup v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k odchozímu přenosu, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování odchozího přenosu dat můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
