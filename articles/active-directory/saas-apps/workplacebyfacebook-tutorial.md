---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s pracovištěm na Facebooku | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a na pracovišti pomocí Facebooku.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725486"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s pracovištěm na Facebooku

V tomto kurzu se dozvíte, jak integrovat pracovní plochu na Facebooku pomocí Azure Active Directory (Azure AD). Když integrujete pracovní plochu na Facebooku s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k pracovišti prostřednictvím Facebooku.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k pracovišti prostřednictvím Facebooku s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Pracoviště podle předplatného jednotného přihlašování (SSO) na Facebooku.

> [!NOTE]
> Facebook má dva produkty, na pracovišti (zdarma) a na pracovišti Premium (placené). Každý tenant na pracovišti může nakonfigurovat integraci SCIM a SSO bez dalších požadavků na náklady nebo licence. Jednotné přihlašování a SCIM nejsou k dispozici ve standardních instancích pracoviště.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pracoviště na webu Facebook podporuje jednotné přihlašování na základě standardu **SP**
* Pracoviště podle Facebooku podporuje **zřizování za běhu**
* Pracoviště – na Facebooku podporuje **[Automatické zřizování uživatelů](workplacebyfacebook-provisioning-tutorial.md)**
* Mobilní aplikace na pracovišti na pracovišti se teď dají nakonfigurovat pomocí Azure AD a povolit jednotné přihlašování. V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Přidání pracovního místa na Facebooku z Galerie

Pokud chcete nakonfigurovat integraci pracoviště pomocí Facebooku do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat pracovní plochu z Galerie Facebook.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **pracovní plochu podle Facebooku** .
1. Vyberte možnost **pracovní plocha na Facebooku** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro pracovní plochu prostřednictvím Facebooku

Nakonfigurujete a otestujete jednotné přihlašování Azure AD s využitím webu na Facebooku pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v pracovní ploše Facebooku.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí webu Facebook, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte pracoviště pomocí jednotného přihlašování na Facebooku](#configure-workplace-by-facebook-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvoření pracoviště pomocí služby Facebook Test User](#create-workplace-by-facebook-test-user)** – Pokud chcete mít protějšek B. Simon na pracovišti na pracovišti, které je propojené s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **pracoviště podle webu Facebook** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** (která se nachází na pracovišti jako adresa URL příjemce) zadejte adresu URL pomocí následujícího vzoru: `https://.workplace.com/work/saml.php`

    b. Do textového pole **identifikátor (ID entity)** (který se nachází na pracovišti jako adresa URL cílové skupiny) zadejte adresu URL pomocí následujícího vzoru: `https://www.workplace.com/company/`

    c. Do textového pole **Adresa URL odpovědi** (které se nachází na pracovišti jako služba příjemce kontrolního výrazu) zadejte adresu URL pomocí následujícího vzoru: `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Správné hodnoty pro komunitu na pracovišti najdete na stránce ověřování na řídicím panelu firemní společnosti. to je vysvětleno později v tomto kurzu.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit pracovní plochu podle Facebooku** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k pracovišti prostřednictvím Facebooku.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **pracovní plocha na Facebooku**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-workplace-by-facebook-sso"></a>Konfigurace pracoviště pomocí jednotného přihlašování na Facebooku

1. Pokud chcete automatizovat konfiguraci v rámci pracoviště pomocí Facebooku, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na možnost **nastavit pracovní plochu na Facebooku** a nasměrujte vás na pracoviště po Facebooku. Odtud zadejte přihlašovací údaje správce pro přihlášení k pracovišti na Facebooku. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit pracovní plochu ručně pomocí Facebooku, otevřete nové okno webového prohlížeče a přihlaste se k pracovišti pomocí webu Facebook společnosti jako správce a proveďte následující kroky:

    > [!NOTE]
    > V rámci procesu ověřování SAML může pracovní plocha využívat řetězce dotazů o velikosti až 2,5 kilobajtů, aby bylo možné předat parametry do služby Azure AD.

1. V levém navigačním panelu přejděte na kartu ověřování **zabezpečení**  >   .

    ![Panel pro správu](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Ověřte možnost **jednotného přihlašování (SSO)** .
    
    b. Klikněte na **+ Přidat nového poskytovatele jednotného přihlašování**.
    > [!NOTE]
    > Ujistěte se, že jste zaškrtli políčko přihlášení k heslu. Správci mohou tuto možnost potřebovat pro přihlášení během změny certifikátu, aby se mohly přestat zablokovat.

1. Na kartě **ověřování** vyberte **jednotné přihlašování (SSO)** a proveďte následující kroky:

    ![Karta ověřování](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. V **názvu poskytovatele jednotného přihlašování** zadejte název instance SSO, jako je Azureadsso.

    b. Do textového pole **URL adresy SAML** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL vystavitele SAML** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    d. Otevřete v programu Poznámkový blok **certifikát s kódováním Base-64** stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu SAML** .

    e. Zkopírujte **adresu URL cílové skupiny** pro vaši instanci a vložte ji do textového pole **identifikátor (ID entity)** v **základní části Konfigurace SAML** na Azure Portal.

    f. Zkopírujte **adresu URL příjemce** vaší instance a vložte ji do textového pole **přihlašovací adresa URL** v **základní části Konfigurace SAML** na Azure Portal.

    například Zkopírujte **adresu URL služby ACS (pro příjemce kontrolního výrazu)** pro vaši instanci a vložte ji do textového pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** na Azure Portal.

    h. Posuňte se do dolní části části a klikněte na tlačítko **test SSO** . Výsledkem je automaticky otevírané okno se zobrazeným přihlašovací stránkou Azure AD. Pro ověření zadejte svoje přihlašovací údaje v normálním režimu.

    **Řešení potíží:** Ujistěte se, že se e-mailová adresa vracená zpátky ze služby Azure AD shoduje s účtem pracovní plochy, se kterým jste se přihlásili.

    i. Po úspěšném dokončení testu se posuňte do dolní části stránky a klikněte na tlačítko **Uložit** .

    j. Všichni uživatelé, kteří používají pracoviště na pracovišti, se teď zobrazí na přihlašovací stránce Azure AD pro ověřování.

1. **Přesměrování odhlášení SAML (volitelné)** -

    Volitelně můžete nakonfigurovat adresu URL pro odhlášení SAML, která se dá použít k ukázání na odhlašovací stránce služby Azure AD. Když je toto nastavení povolené a nakonfigurované, uživatel už nebude přesměrován na stránku pro odhlášení pracovní plochy. Místo toho bude uživatel přesměrován na adresu URL, která byla přidána v nastavení přesměrování odhlášení SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfigurace četnosti opakovaného ověřování

Na pracovišti můžete nakonfigurovat, aby se spouštěla výzva ke kontrole SAML každý den, tři dny, týden, dva týdny, měsíc nebo nikdy.

> [!NOTE]
> Minimální hodnota pro u mobilních aplikací kontroly SAML je nastavená na jeden týden.

Resetování SAML pro všechny uživatele můžete také vynutit pomocí tlačítka: vyžadovat ověřování SAML pro všechny uživatele nyní.

### <a name="create-workplace-by-facebook-test-user"></a>Vytvoření pracoviště pomocí služby Facebook Test User

V této části se na pracovišti na Facebooku vytvoří uživatel s názvem B. Simon. Pracoviště na Facebooku podporuje zřizování za běhu, což je ve výchozím nastavení povolené.

V této části není žádná akce. Pokud uživatel na pracovišti neexistuje na Facebooku, vytvoří se nový, když se pokusíte o přístup k pracovní ploše z Facebooku.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [pracovní plochu podle týmu podpory pro klienty Facebook](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na pracovní plochu na adrese URL pro přihlášení na Facebooku, kde můžete spustit tok přihlášení. 

* Přejít na pracovní plochu pomocí přihlašovací adresy URL přímo na Facebooku a spustit tok přihlášení odtud.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici pracovní plocha na Facebooku v části Moje aplikace, přesměruje se na pracovní plochu na adrese URL pro přihlášení na Facebooku. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testování jednotného přihlašování pro pracovní plochu na Facebooku (mobilní zařízení)

1. Otevřete pracovní plochu na Facebooku mobilní aplikaci. Na přihlašovací stránce klikněte na **Přihlásit** se.

    ![Přihlášení](./media/workplacebyfacebook-tutorial/test05.png)

2. Zadejte svůj obchodní e-mail a klikněte na **pokračovat**.

    ![E-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Klikněte **jenom jednou**.

    ![Jednou](./media/workplacebyfacebook-tutorial/test04.png)

4. Klikněte na tlačítko **povoleno**.

    ![Povolení](./media/workplacebyfacebook-tutorial/test03.png)

5. Nakonec po úspěšném přihlášení se zobrazí domovská stránka aplikace.    

    ![Domovská stránka](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete pracovní plochu na Facebooku, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)