---
title: 'Kurz: Azure Active Directory integrace s Freedcamp | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freedcamp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 19378251408d55868ed844a5505ae48ece55dc3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92451478"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Kurz: integrace Freedcamp s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Freedcamp s Azure Active Directory (Azure AD). Když integrujete Freedcamp s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Freedcamp.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Freedcamp svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Freedcamp odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Freedcamp podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-freedcamp-from-the-gallery"></a>Přidání Freedcamp z Galerie

Pokud chcete nakonfigurovat integraci Freedcamp do služby Azure AD, musíte přidat Freedcamp z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Freedcamp** .
1. Na panelu výsledků vyberte **Freedcamp** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Freedcamp pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Freedcamp.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Freedcamp, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte Freedcamp](#configure-freedcamp)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele Freedcamp test](#create-freedcamp-test-user)** , který má protějšek Britta Simon v Freedcamp, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Freedcamp** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    1. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Uživatelé můžou zadat i hodnoty URL s ohledem na svou vlastní doménu zákazníka a nemusí být nutně vzor `freedcamp.com` , můžou zadat libovolnou hodnotu specifickou pro doménu zákazníka, která je specifická pro svou instanci aplikace. Také se můžete obrátit na [tým podpory klienta Freedcamp](mailto:devops@freedcamp.com) , kde najdete další informace o vzorech adres URL.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Freedcamp** zkopírujte na základě vašeho požadavku příslušné adresy URL.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurace Freedcamp

1. Pokud chcete automatizovat konfiguraci v rámci Freedcamp, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Freedcamp nastavení** a nasměrujte vás na aplikaci Freedcamp. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Freedcamp. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Freedcamp ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Freedcamp společnosti jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na **profil** a potom přejděte na **můj účet**.

    ![Snímek obrazovky zobrazující, že je vybraná možnost profil a můj účet](./media/freedcamp-tutorial/config01.png)

5. Na levé straně řádku nabídek klikněte na **jednotné přihlašování** a na stránce **připojení SSO** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje "S S" v panelu nabídek na levé straně a na stránce "vaše připojení", se zadanými hodnotami a vybraným tlačítkem Odeslat.](./media/freedcamp-tutorial/config02.png)

    a. Do textového pole **název** zadejte název.

    b. Do textového pole **ID entity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete v programu Poznámkový blok certifikát kódovaný jako base64, zkopírujte jeho obsah a vložte ho do textového pole **certifikát** .

    e. Klikněte na **Odeslat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Freedcamp.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Freedcamp**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-freedcamp-test-user"></a>Vytvořit testovacího uživatele Freedcamp

Pokud chcete povolit uživatele Azure AD, přihlaste se k Freedcamp, musí být zřízené do Freedcamp. V Freedcamp je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. V jiném okně webového prohlížeče se přihlaste k Freedcamp jako správce zabezpečení.

2. V pravém horním rohu stránky klikněte na **profil** a potom přejděte ke **správě systému**.

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Na pravé straně stránky Správa systému proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje vybrané tlačítko Přidat nebo pozvat uživatele, zvýrazněné pole E-mail a vybrané tlačítko Přidat uživatele.](./media/freedcamp-tutorial/config04.png)

    a. Klikněte na **Přidat nebo pozvat uživatele**.

    b. Do textového pole **e-mail** zadejte e-maily uživatele jako `Brittasimon@contoso.com` .

    c. Klikněte na **Add User** (Přidat uživatele).

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Freedcamp, měli byste se automaticky přihlásit k Freedcamp, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)