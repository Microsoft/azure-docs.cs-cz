---
title: 'Kurz: Integrace Azure Active Directory s Oracle cloudové infrastruktury konzoly | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Oracle cloudu infrastruktury konzoly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a034e7dc06defd701b58ec283ee161f5557c075d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095451"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory Oracle cloudové infrastruktury konzoly

V tomto kurzu se dozvíte, jak integrovat Oracle konzoly infrastruktury cloudu s Azure Active Directory (Azure AD). Při integraci Oracle konzoly infrastruktury cloudu s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup ke konzole Oracle cloudové infrastruktury.
* Aby uživatelé mohli být automaticky přihlášeni Oracle cloudové infrastruktury konzoly pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Oracle cloudové infrastruktury konzoly jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Oracle cloudové infrastruktury konzola podporuje **SP** jednotné přihlašování zahájené.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Přidání Oracle cloudové infrastruktury konzoly z Galerie

Ke konfiguraci integrace konzoly infrastruktury cloudu Oracle do služby Azure AD, budete muset přidat Oracle cloudové infrastruktury konzoly z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Oracle cloudové infrastruktury konzoly** do vyhledávacího pole.
1. Vyberte **Oracle cloudové infrastruktury konzoly** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Oracle cloudové infrastruktury konzoly pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Oracle cloudové infrastruktury konzoly.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s konzolou pro Oracle cloudové infrastruktury, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace Oracle cloudové infrastruktury konzoly](#configure-oracle-cloud-infrastructure-console)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
1. **[Vytvořit testovacího uživatele konzoly infrastruktury cloudu Oracle](#create-oracle-cloud-infrastructure-console-test-user)**  mít protějšek B. Simon v konzole pro infrastruktury cloudu Oracle, který je propojený s Azure AD reprezentace uživatele.
1. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Oracle cloudové infrastruktury konzoly** stránky integrace aplikací, najdete **spravovat** a vyberte **jeden přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

   > [!NOTE]
   > Zobrazí se soubor metadat poskytovatele služeb z **konfigurace Oracle cloudové infrastruktury konzoly jednotného přihlašování** části kurzu.
    
   1. Klikněte na tlačítko **nahrát soubor metadat**.

   1. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

   1. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části textového pole.
    
      > [!NOTE]
      > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty nechcete získat polulated automaticky, a potom zadejte hodnoty ručně podle vašich požadavků.

      V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: ` https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta Oracle cloudové infrastruktury konzoly](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **kód XML metadat federace** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Oracle cloudové infrastruktury Konzolová aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

   ![image](common/edit-attribute.png)

1. Kromě toho výše Oracle cloudové infrastruktury Konzolová aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **atributy uživatele a deklarace identity** části na **deklarace skupiny (Preview)**  dialogového okna, proveďte následující kroky:

   1. Klikněte na tlačítko **pera** vedle **název hodnota identifikátoru**.

   1. Vyberte **trvalá** jako **zvolte název identifikátoru formátu**.
 
   1. Klikněte na **Uložit**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Klikněte na tlačítko **pera** vedle **skupiny vrácené v deklaraci identity**.

   1. Vyberte **skupiny zabezpečení** ze seznamu přepínač.

   1. Vyberte **zdrojového atributu** z **ID skupiny**.

   1. Zkontrolujte **přizpůsobit název deklarace skupiny**.

   1. V **název** textového pole, typ **groupName**.

   1. V **Namespace (volitelné)** textového pole, typ `https://auth.oraclecloud.com/saml/claims`.

   1. Klikněte na **Uložit**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Na **nastavení Oracle cloudové infrastruktury konzoly** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurace Oracle cloudové infrastruktury konzoly

1. V okně jiné webové prohlížeče Přihlaste se k Oracle Cloud infrastruktury Console jako správce.

1. Klikněte v levé nabídce a klepněte na **Identity** přejděte na **federace**.

   ![Konfigurace](./media/oracle-cloud-tutorial/config01.png)

1. Uložit **soubor metadat poskytovatele služeb** kliknutím **stáhněte si tento dokument** propojit a nahrajte ho do **základní konfiguraci SAML** části webu Azure portal a potom Klikněte na **přidat zprostředkovatele Identity**.

   ![Konfigurace](./media/oracle-cloud-tutorial/config02.png)

1. Na **přidat zprostředkovatele Identity** rozbalovací, proveďte následující kroky:

   ![Konfigurace](./media/oracle-cloud-tutorial/config03.png)

   1. V **název** textové pole, zadejte své jméno.

   1. V **popis** textové pole, zadejte popis.

   1. Vyberte **MICROSOFT ACTIVE DIRECTORY FEDERATION Services (ADFS) nebo zprostředkovatele kompatibilní IDENTITY SAML 2.0** jako **typ**.

   1. Klikněte na tlačítko **Procházet** nahrát kód XML metadat federace, který jste si stáhli z webu Azure portal.

   1. Klikněte na tlačítko **pokračovat** a na **upravit zprostředkovatele Identity** části proveďte následující kroky:

      ![Konfigurace](./media/oracle-cloud-tutorial/config09.png)

   1. Pro **SKUPINU poskytovatele IDENTITY** pole, zadejte název skupiny a ID skupiny, které jsou nastavené na webu Azure portal. Skupina musí být namapována na žádnou odpovídající skupina v **OCI skupiny** pole.

   1. Závislosti na vašem nastavení webu Azure portal a vaše organizace potřebují lze namapovat více skupin. Klikněte na **+ přidat mapování** přidat libovolný počet skupin podle potřeby.

   1. Klikněte na **Submit** (Odeslat).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B. Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B. Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure díky udělení přístupu ke konzole Oracle cloudové infrastruktury.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Oracle cloudové infrastruktury konzoly**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Vytvoření Oracle cloudové infrastruktury konzoly testovacího uživatele

 Oracle cloudové infrastruktury konzola podporuje just-in-time zřizování, což je ve výchozím nastavení. Neexistuje žádná položka akce pro vás v této části. Nový uživatel získat nebyl vytvořen při pokusu o přístup a také bez nutnosti vytvoření uživatele.

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Oracle cloudové infrastruktury konzoly na přístupovém panelu, budete přesměrováni na přihlašovací stránku konzoly Oracle cloudové infrastruktury. Vyberte **zprostředkovatele IDENTITY** z rozevírací nabídky a klikněte na tlačítko **pokračovat** jak je znázorněno níže pro přihlášení. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfigurace](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
