---
title: 'Kurz: Integrace Azure Active Directory s AirWatch | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 366ffa560cb16f6445eca93e79068fba6cc31e6d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813113"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Kurz: Integrace Azure Active Directory s AirWatch

V tomto kurzu se dozvíte, jak integrovat AirWatch s Azure Active Directory (Azure AD).
AirWatch integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k AirWatch.
* Můžete povolit uživatelům být automaticky přihlášeni k AirWatch (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AirWatch, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* AirWatch jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje AirWatch **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-airwatch-from-the-gallery"></a>Přidání AirWatch z Galerie

Konfigurace integrace AirWatch do služby Azure AD, budete muset přidat AirWatch z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat AirWatch z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **AirWatch**vyberte **AirWatch** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![AirWatch v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí AirWatch podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v AirWatch.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AirWatch, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace AirWatch Single Sign-On](#configure-airwatch-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele AirWatch](#create-airwatch-test-user)**  – Pokud chcete mít protějšek Britta Simon AirWatch, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s AirWatch, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **AirWatch** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![AirWatch domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. V **identifikátor (Entity ID)** textové pole, zadejte hodnotu jako: `AirWatch`

    > [!NOTE]
    > Tato hodnota není reálné. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory AirWatch klienta](https://www.air-watch.com/company/contact-us/) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení AirWatch** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-airwatch-single-sign-on"></a>Konfigurace AirWatch jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu firmy AirWatch jako správce.

2. V levém navigačním podokně klikněte na tlačítko **účty**a potom klikněte na tlačítko **správci**.

   ![Správci](./media/airwatch-tutorial/ic791920.png "správci")

3. Rozbalte **nastavení** nabídky a pak klikněte na tlačítko **Directory Services**.

   ![Nastavení](./media/airwatch-tutorial/ic791921.png "nastavení")

4. Klikněte na tlačítko **uživatele** kartě **Base DN** textového pole zadejte název domény a pak klikněte na tlačítko **Uložit**.

   ![Uživatel](./media/airwatch-tutorial/ic791922.png "uživatele")

5. Klikněte na tlačítko **Server** kartu.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

6. Proveďte následující kroky:

    ![Nahrát](./media/airwatch-tutorial/ic791924.png "nahrát")   

    a. Jako **typ adresáře**vyberte **žádný**.

    b. Vyberte **používat protokol SAML pro ověřování**.

    c. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **nahrát**.

7. V **žádosti** části, proveďte následující kroky:

    ![Požádat o](./media/airwatch-tutorial/ic791925.png "žádosti")  

    a. Jako **typ vazby žádosti**vyberte **příspěvek**.

    b. Na webu Azure Portal na **nakonfigurovat jednotné přihlašování v Airwatch** dialogového okna stránky, zkopírujte **přihlašovací adresa URL** hodnotu a vložte jej do **jednotné přihlašování na adresa URL zprostředkovatele Identity** textové pole.

    c. Jako **formátem**vyberte **e-mailovou adresu**.

    d. Klikněte na **Uložit**.

8. Klikněte na tlačítko **uživatele** kartu znovu.

    ![Uživatel](./media/airwatch-tutorial/ic791926.png "uživatele")

9. V **atribut** části, proveďte následující kroky:

    ![Atribut](./media/airwatch-tutorial/ic791927.png "atribut")

    a. V **identifikátor objektu** textové pole, typ `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. V **uživatelské jméno** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. V **zobrazovaný název** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. V **křestní jméno** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. V **příjmení** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k AirWatch použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **AirWatch**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **AirWatch**.

    ![Odkaz AirWatch v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-airwatch-test-user"></a>Vytvoření AirWatch testovacího uživatele

Umožňuje uživatelům Azure AD se přihlaste k AirWatch, musí být poskytnuty v k AirWatch. V případě AirWatch zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **AirWatch** společnosti serveru jako správce.

2. V navigačním podokně na levé straně klikněte na tlačítko **účty**a potom klikněte na tlačítko **uživatelé**.
  
   ![Uživatelé](./media/airwatch-tutorial/ic791929.png "uživatelů")

3. V **uživatelé** nabídky, klikněte na tlačítko **zobrazení seznamu**a potom klikněte na tlačítko **přidat \> přidat uživatele**.
  
   ![Přidání uživatele](./media/airwatch-tutorial/ic791930.png "přidat uživatele")

4. Na **přidat / upravit uživatele** dialogového okna, proveďte následující kroky:

   ![Přidání uživatele](./media/airwatch-tutorial/ic791931.png "přidat uživatele")

   a. Typ **uživatelské jméno**, **heslo**, **potvrzení hesla**, **křestní jméno**, **příjmení**,  **E-mailová adresa** platného účtu služby Azure Active Directory ke zřízení do související textových polí.

   b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné AirWatch uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných AirWatch uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici AirWatch na přístupovém panelu, můžete by měl být automaticky přihlášeni k AirWatch, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)