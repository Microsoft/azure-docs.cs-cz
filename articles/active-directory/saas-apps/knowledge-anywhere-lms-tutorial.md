---
title: 'Kurz: Integrace Azure Active Directory se znalostí odkudkoli | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a znalostí odkudkoli.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: eab1304408f896f5c9d736488997c1246ae167f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458978"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Kurz: integrace znalostí odkudkoli do LMS pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat znalostní bázi LMS odkudkoli s Azure Active Directory (Azure AD). Při integraci znalostí odkudkoli s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k znalostnímu prostoru LMS odkudkoli.
* Umožněte uživatelům, aby se k vašim účtům Azure AD automaticky přihlásili do znalostí odkudkoli.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO) pro jednotné přihlašování přes LMS

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Znalostní báze LMS odkudkoli **podporuje jednotné** přihlašování, které podporuje jednotné přihlašování **uživatelů.**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Přidání znalostí z Galerie odkudkoli z Galerie

Pokud chcete nakonfigurovat integraci znalostí odkudkoli do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat znalosti z Galerie odkudkoli.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text ve **znalostní bázi odkudkoli** .
1. Z panelu výsledků vyberte **knowledgeers odkudkoli** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí znalostí odkudkoli a pomocí testovacího uživatele s názvem **B. Simon**. K tomu, aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci znalostní báze LMS kdekoli.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí znalostí odkudkoli, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte si LMS odkudkoli](#configure-knowledge-anywhere-lms)** a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořte si uživatele se znalostí pro uživatele](#create-knowledge-anywhere-lms-test-user)** v systému LMS, aby měli protějšek B. Simon ve znalostní bázi pro uživatele, který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **znalostní báze** aplikace pro hledání na úrovni Standard pro LMS najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    1. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi, která je vysvětlena dále v tomto kurzu.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory pro klienty ve znalostní bázi LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení znalostní báze na libovolném místě LMS** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurace znalostí odkudkoli

1. Chcete-li automatizovat konfiguraci v rámci znalostí odkudkoli, je nutné nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Setup knowledgeers odkudkoli** vás nasměruje na aplikaci v rámci znalostí odkudkoli. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat k znalostnímu prostoru LMS odkudkoli. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete ručně nastavit znalosti v systému LMS odkudkoli, otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti na webu LMS odkudkoli jako správce a proveďte následující kroky:

4. Vyberte na kartě **Web** .

    ![Snímek obrazovky se zobrazí na kartě webu.](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Vyberte na kartě **Nastavení SAML** .

    ![Snímek obrazovky s vybraným nastavením SAML zobrazuje stránku s vědomím](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klikněte na **Přidat nový**.

    ![Snímek obrazovky se zobrazí tlačítko Přidat nový v nastavení poskytovatele služeb.](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na stránce **Přidat nebo aktualizovat nastavení SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka Přidat nebo aktualizovat nastavení SAML, kde můžete udělat změny popsané tady.](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Zadejte název IDP podle vaší organizace. Pro např.: `Azure` .

    b. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL IDP** vložte hodnotu **Adresa URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený soubor certifikátu z Azure Portal do programu Poznámkový blok, zkopírujte obsah certifikátu a vložte ho do textového pole **certifikátu** .

    e. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    f. Z rozevíracího seznamu pro **doménu** vyberte **hlavní lokalitu** .

    například Zkopírujte hodnotu **ID entity SP** a vložte ji **do textového pole** **základní konfigurační oddíl SAML** v Azure Portal.

    h. Zkopírujte hodnotu **adresy URL odpovědi SP (ACS)** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup ke znalostnímu prostoru v systému LMS odkudkoli.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **znalostní báze LMS odkudkoli**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-knowledge-anywhere-lms-test-user"></a>Vytváření znalostí pro uživatele s testovacím prostředím pro systém LMS

V této části se ve znalostní bázi LMS odkudkoli vytvoří uživatel s názvem B. Simon. Znalostní báze LMS odkudkoli podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistoval v rámci znalosti v rámci vašeho uživatelského prostředí, vytvoří se po ověření nový.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu kliknete na dlaždici s informacemi o znalostech a/s, měli byste se automaticky přihlásili ke znalostnímu prostoru, ve kterém jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)