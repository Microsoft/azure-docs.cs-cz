---
title: 'Kurz: Azure Active Directory integrace s BambooHR | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180332"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Kurz: Azure Active Directory integrace s BambooHR

V tomto kurzu se dozvíte, jak integrovat BambooHR s Azure Active Directory (Azure AD). Když integrujete BambooHR s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k BambooHR.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k BambooHR svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* BambooHR odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* BambooHR podporuje jednotné přihlašování iniciované v **SP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-bamboohr-from-the-gallery"></a>Přidání BambooHR z Galerie

Pokud chcete nakonfigurovat integraci BambooHR do služby Azure AD, musíte přidat BambooHR z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **BambooHR** .
1. Na panelu výsledků vyberte **BambooHR** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Konfigurace a testování jednotného přihlašování Azure AD pro BambooHR

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí BambooHR pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v BambooHR.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí BambooHR postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte BAMBOOHR SSO](#configure-bamboohr-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    * **[Vytvoření BambooHR Test User](#create-bamboohr-test-user)** – pro Britta Simon v BambooHR, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **BambooHR** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company>.bamboohr.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    | Adresa URL odpovědi |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi. Pokud chcete získat hodnoty, obraťte se na [tým podpory klienta BambooHR](https://www.bamboohr.com/contact.php) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení BambooHR** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k BambooHR.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **BambooHR**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-bamboohr-sso"></a>Konfigurace jednotného přihlašování BambooHR

1. V novém okně se přihlaste k webu BambooHR společnosti jako správce.

2. Na domovské stránce proveďte následující kroky:
   
    ![BambooHR jednu Sign-On stránku](./media/bamboo-hr-tutorial/ic796691.png "Jednotné přihlašování")   

    a. Vyberte **aplikace**.
   
    b. V podokně **aplikace** vyberte **jednotné přihlašování**.
   
    c. Vyberte **jednotné přihlašování SAML**.

3. V podokně **jednotné přihlašování SAML** udělejte toto:
   
    ![Jednoduché podokno Sign-On SAML](./media/bamboo-hr-tutorial/IC796692.png "Single Sign-On SAML")
   
    a. Do pole **Adresa URL pro přihlášení SSO** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal v kroku 6.
      
    b. V programu Poznámkový blok otevřete certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah a vložte ho do pole **certifikát X. 509** .
   
    c. Vyberte **Uložit**.

### <a name="create-bamboohr-test-user"></a>Vytvořit testovacího uživatele BambooHR

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k BambooHR, nastavte je ručně v BambooHR pomocí následujícího postupu:

1. Přihlaste se k webu **BambooHR** jako správce.

2. Na panelu nástrojů v horní části vyberte **Nastavení**.
   
    ![Tlačítko nastavení](./media/bamboo-hr-tutorial/IC796694.png "Nastavení")

3. Vyberte **Přehled**.

4. V levém podokně vyberte   >  **uživatele** zabezpečení.

5. Zadejte uživatelské jméno, heslo a e-mailovou adresu platného účtu Azure AD, který chcete nastavit.

6. Vyberte **Uložit**.
        
>[!NOTE]
>K nastavení uživatelských účtů Azure AD můžete použít také nástroje pro vytváření BambooHR uživatelských účtů nebo rozhraní API.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k BambooHR, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k BambooHR přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Po kliknutí na dlaždici BambooHR na přístupovém panelu se tato akce přesměruje na přihlašovací adresu BambooHR. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování BambooHR můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).