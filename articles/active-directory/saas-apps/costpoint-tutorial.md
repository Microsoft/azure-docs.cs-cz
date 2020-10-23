---
title: 'Kurz: Azure Active Directory integrace s Costpoint | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 16c9d64cc4dd49898245d74108cd6a4f2f0e4660
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455166"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Kurz: integrace Costpoint s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Costpoint s Azure Active Directory (Azure AD). Když integrujete Costpoint s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Costpoint.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Costpoint svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Costpointm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí. Costpoint podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="generate-costpoint-metadata"></a>Generovat metadata Costpoint

Costpoint konfigurace jednotného přihlašování SAML se vysvětluje v příručce pro **DeltekCostpoint711Security.pdf** . Tento průvodce si můžete stáhnout z webu podpory Deltek Costpoint a přečtěte si téma **instalace jednotného přihlašování SAML**  >  **Konfigurace jednotného přihlašování SAML mezi Costpoint a Microsoft Azure** . Postupujte podle pokynů a vygenerujte soubor **XML federačních metadat pro COSTPOINT SP** . 

![Snímek obrazovky zobrazující, že nástroj pro konfiguraci produktu má vybranou kartu WebLogic-Security.](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Přidání Costpoint z Galerie

Pro integraci Costpoint s Azure AD nejdřív přidejte Costpoint do seznamu spravovaných aplikací SaaS z galerie v Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

1. V levém navigačním podokně vyberte službu **Azure Active Directory** .

   ![Tlačítko Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

   ![Okno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.

   ![Tlačítko Nová aplikace](common/add-new-app.png)

1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Costpoint** .

   ![Costpoint v seznamu výsledků](common/search-new-app.png)

1. V seznamu výsledků vyberte **Costpoint**a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Konfigurace a testování funkce Azure AD Single SGN – on

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Costpoint pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Costpoint.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Costpoint, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
1. **[Nakonfigurujte Costpoint](#configure-costpoint)** pro konfiguraci nastavení jednotného přihlašování SAML na straně aplikace.
1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Vytvořte Costpoint testovacího uživatele](#create-a-costpoint-test-user)** , který bude mít protějšek B. Simon v Costpoint, který se odkazuje na reprezentaci uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. Na stránce integrace aplikací **Costpoint** vyberte **jednotné přihlašování**.

   ![Konfigurace odkazu jednotného přihlašování](common/select-sso.png)

1. V části **základní konfigurace SAML** Pokud máte *soubor metadat poskytovatele služby*, proveďte tyto kroky:

   > [!NOTE]
   > Soubor metadat poskytovatele služeb získáte v části [generování metadat Costpoint](#generate-costpoint-metadata). Použití souboru je vysvětleno dále v tomto kurzu.
 
   1. Vyberte tlačítko **nahrát soubor metadat** a pak vyberte soubor **XML s metadaty Costpoint SP** , který dřív vygenerovala Costpoint, a pak vyberte tlačítko **Přidat** , aby se soubor nahrál.

      ![Nahrání souboru metadat](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi vyplní automaticky v části Costpoint.

      > [!NOTE]
      > Pokud hodnoty **adresy URL** **identifikátoru** a odpovědi nejsou automaticky polulated, zadejte hodnoty ručně podle vašich požadavků. Ověřte, že je správně nastavený **identifikátor (ID entity)** a **Adresa URL odpovědi (adresa URL služby pro příjemce kontrolního výrazu)** a že **Adresa URL služby ACS** je platná adresa URL Costpoint, která končí na **/LoginServlet.CPS**.

   1. Vyberte **nastavit další adresy URL**. Do pole **stav přenosu**zadejte hodnotu pomocí následujícího vzoru: `system=[your system]` (například **System = DELTEKCP**).

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte v části **podpisový certifikát SAML** ikonu **kopírování** a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do poznámkového bloku.

   ![Podpisový certifikát SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Konfigurace Costpoint

1. Vraťte se do konfiguračního nástroje Costpoint. Do textového pole **XML federačních metadat IDP** vložte obsah souboru *adresy URL federačních metadat aplikace* . 

   ![Nástroj pro konfiguraci Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Pokračujte v pokynech průvodce **DeltekCostpoint711Security.pdf** a dokončete instalaci Costpoint SAML.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem B. Simon.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

   ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

1. Vyberte **Nový uživatel**.

   ![Tlačítko pro nového uživatele](common/new-user.png)

1. V části vlastnosti **uživatele** proveďte tyto kroky:

   ![Uživatelský dialog](common/user-properties.png)

   1. Do pole **název** zadejte **B. Simon**.
   
   1. Do pole **uživatelské jméno** zadejte `b.simon\@yourcompanydomain.extension` (například B.Simon@contoso.com ).
   
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte B. Simon přístup k Costpoint.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.

1. V seznamu aplikace vyberte **Costpoint**.

1. V části **Správa** na stránce Přehled aplikace vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** v seznamu **Uživatelé** vyberte **B. Simon**. Pak zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak zvolte **Vybrat**.

1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-costpoint-test-user"></a>Vytvořit testovacího uživatele v Costpoint

V této části vytvoříte uživatele v Costpoint. Předpokládejme, že ID uživatele je **b. Simon** a jméno uživatele je **b. Simon**. Pokud chcete přidat uživatele na platformě Costpoint, pracujte s [týmem podpory klienta Costpoint](https://www.deltek.com/about/contact-us) . Aby bylo možné použít jednotné přihlašování, musí být uživatel vytvořen a aktivován.

Po vytvoření uživatele musí být výběr **metody ověřování** uživatele ve **službě Active Directory**, musí být zaškrtnuto políčko **jednotné přihlašování SAML** a uživatelské jméno z Azure Active Directory musí být **Služba Active Directory nebo ID certifikátu** (zobrazené na následujícím snímku obrazovky).

![Uživatel Costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Costpoint, měli byste být automaticky přihlášeni k aplikaci Costpoint, protože jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)