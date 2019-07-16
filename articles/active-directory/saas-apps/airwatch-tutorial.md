---
title: 'Kurz: Integrace Azure Active Directory s AirWatch | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227608"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Kurz: AirWatch integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat AirWatch s Azure Active Directory (Azure AD). Když integrujete AirWatch s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k AirWatch.
* Aby uživatelé mohli být automaticky přihlášeni k AirWatch pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje AirWatch **SP** jednotné přihlašování zahájené.

## <a name="adding-airwatch-from-the-gallery"></a>Přidání AirWatch z Galerie

Konfigurace integrace AirWatch do služby Azure AD, budete muset přidat AirWatch z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **AirWatch** do vyhledávacího pole.
1. Vyberte **AirWatch** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s AirWatch pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v AirWatch.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s AirWatch, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování AirWatch](#configure-airwatch-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele AirWatch](#create-airwatch-test-user)**  – Pokud chcete mít protějšek Britta Simon AirWatch, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **AirWatch** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    1. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. V **identifikátor (Entity ID)** textové pole, zadejte hodnotu jako: `AirWatch`

    > [!NOTE]
    > Tato hodnota není reálné. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory AirWatch klienta](https://www.air-watch.com/company/contact-us/) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. AirWatch aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

1. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Name |  Zdrojový atribut|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **kód XML metadat federace** a vyberte **Stáhnout** kód XML metadat a uložte ve vašem počítači.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Na **nastavení AirWatch** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Konfigurace jednotného přihlašování AirWatch

1. V okně jiné webové prohlížeče Přihlaste se k webu firmy AirWatch jako správce.

1. Na stránce nastavení. Vyberte **Nastavení > podniková integrace > Directory Services**.

   ![Nastavení](./media/airwatch-tutorial/ic791921.png "nastavení")

1. Klikněte na tlačítko **uživatele** kartě **Base DN** textového pole zadejte název domény a pak klikněte na tlačítko **Uložit**.

   ![Uživatel](./media/airwatch-tutorial/ic791922.png "uživatele")

1. Klikněte na tlačítko **Server** kartu.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Následující postup proveďte **LDAP** části:

    ![Nahrát](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Jako **typ adresáře**vyberte **žádný**.

    b. Vyberte **používat protokol SAML pro ověřování**.

1. Na **SAML 2.0** část odeslání staženého certifikátu, klikněte na tlačítko **nahrát**.

    ![Nahrát](./media/airwatch-tutorial/ic791932.png "nahrát")

1. V **žádosti** části, proveďte následující kroky:

    ![Požádat o](./media/airwatch-tutorial/ic791925.png "žádosti")  

    a. Jako **typ vazby žádosti**vyberte **příspěvek**.

    b. Na webu Azure Portal na **nakonfigurovat jednotné přihlašování v AirWatch** dialogového okna stránky, zkopírujte **přihlašovací adresa URL** hodnotu a vložte jej do **jednotné přihlašování na adresa URL zprostředkovatele Identity** textové pole.

    c. Jako **formátem**vyberte **e-mailovou adresu**.

    d. Jako **zabezpečení požadavku ověřování**vyberte **žádný**.

    e. Klikněte na **Uložit**.

1. Klikněte na tlačítko **uživatele** kartu znovu.

    ![Uživatel](./media/airwatch-tutorial/ic791926.png "uživatele")

1. V **atribut** části, proveďte následující kroky:

    ![Atribut](./media/airwatch-tutorial/ic791927.png "atribut")

    a. V **identifikátor objektu** textové pole, typ `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. V **uživatelské jméno** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. V **zobrazovaný název** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. V **křestní jméno** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. V **příjmení** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon k udělení přístupu k AirWatch použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **AirWatch**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-airwatch-test-user"></a>Vytvoření AirWatch testovacího uživatele

Povolení služby Azure AD uživatelům umožní přihlásit k AirWatch, musí být zřízená v k AirWatch. V případě AirWatch zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **AirWatch** společnosti serveru jako správce.

2. V navigačním podokně na levé straně klikněte na tlačítko **účty**a potom klikněte na tlačítko **uživatelé**.
  
   ![Uživatelé](./media/airwatch-tutorial/ic791929.png "uživatelů")

3. V **uživatelé** nabídky, klikněte na tlačítko **zobrazení seznamu**a potom klikněte na tlačítko **Přidat > Přidat uživatele**.
  
   ![Přidání uživatele](./media/airwatch-tutorial/ic791930.png "přidat uživatele")

4. Na **přidat / upravit uživatele** dialogového okna, proveďte následující kroky:

   ![Přidání uživatele](./media/airwatch-tutorial/ic791931.png "přidat uživatele")

   a. Typ **uživatelské jméno**, **heslo**, **potvrzení hesla**, **křestní jméno**, **příjmení**,  **E-mailová adresa** platného účtu služby Azure Active Directory ke zřízení do související textových polí.

   b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné AirWatch uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných AirWatch uživatelským účtům, zřídit AAD.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice AirWatch na přístupovém panelu, můžete by měl být automaticky přihlášeni k AirWatch, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
