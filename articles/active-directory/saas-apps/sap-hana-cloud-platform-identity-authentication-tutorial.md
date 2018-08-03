---
title: 'Kurz: Integrace Azure Active Directory s ověřováním identit cloudové platformy SAP | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ověřováním identit cloudové platformy SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 266c9523f45294899e3cddbe782cbc54846eb119
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422303"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Kurz: Integrace Azure Active Directory s ověřováním identit cloudové platformy SAP

V tomto kurzu se dozvíte, jak integrovat ověřováním identit cloudové platformy SAP pomocí Azure Active Directory (Azure AD). Ověřováním identit cloudové platformy SAP slouží jako proxy server zprostředkovatele identity pro přístup k SAP aplikace, které používají službu Azure AD jako zprostředkovatele identity hlavní.

Když integrujete ověřováním identit cloudové platformy SAP v Azure AD, získáte následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k aplikacím, SAP.
- Můžete povolit uživatelům automaticky přihlašovat k SAP aplikace pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ověřováním identit cloudové platformy SAP, potřebujete následující položky:

- Předplatné služby Azure AD.
- Jednotné přihlašování na povoleno předplatné pro ověřováním identit cloudové platformy SAP.

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Pokud nemáte Azure AD zkušební prostředí [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ověřováním identit cloudové platformy SAP z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

Než se začtete do technických podrobností, je důležité pochopit koncepty, kterým se chcete podívat na. Ověřováním identit cloudové platformy SAP a Active Directory Federation Services umožňují implementaci jednotného přihlašování napříč aplikacemi nebo služby, které jsou chráněné službou Azure AD (jako identity) s aplikacemi SAP a služby, které jsou chráněné službou SAP cloudem Ověření Identity Platform.

V současné době ověřováním identit cloudové platformy SAP funguje jako zprostředkovatel Identity Proxy aplikací SAP. Azure Active Directory pak funguje jako přední poskytovatel Identity v tomto nastavení. 

Následující diagram znázorňuje tuto relaci:

![Vytváří se testovací uživatele služby Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

S tímto nastavením je tenant ověřováním identit cloudové platformy SAP nakonfigurovaný jako důvěryhodné aplikace v Azure Active Directory. 

Všechny aplikace SAP a služby, které chcete chránit tímto způsobem jsou následně nakonfigurovat v konzole pro správu ověřováním identit cloudové platformy SAP.

Proto autorizaci k udělování přístupu k SAP aplikacím a službám musí proběhnout v ověřováním identit cloudové platformy SAP (na rozdíl od služby Azure Active Directory).

Tím, že nakonfigurujete ověřováním identit cloudové platformy SAP jako aplikaci prostřednictvím Azure Active Directory Marketplace, není nutné ke konfiguraci jednotlivých deklarací identity nebo kontrolní výrazy SAML.

>[!NOTE] 
>Momentálně se podporuje jenom jednotného přihlašování k webu je testovaná obě strany. Toky, které jsou nezbytné pro aplikace API nebo komunikace rozhraní API pro rozhraní API by mělo fungovat, ale ještě nebyly testovány. Se provést test během následující aktivity.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidat z Galerie ověřováním identit cloudové platformy SAP
Konfigurace integrace ověřováním identit cloudové platformy SAP v Azure AD, budete muset přidat ověřováním identit cloudové platformy SAP z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ověřováním identit cloudové platformy SAP z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat nový novou aplikaci, vyberte **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **ověřováním identit cloudové platformy SAP**. 

1. Vyberte **ověřováním identit cloudové platformy SAP** z panel výsledků a pak vyberte **přidat** tlačítko.

    ![Ověřováním identit cloudové platformy SAP v seznamu výsledků](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ověřováním identit cloudové platformy SAP. Konfigurace a testování s názvem "Britta Simon." testovacího uživatele

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo je uživatel protějšky v ověřováním identit cloudové platformy SAP. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatel v ověřováním identit cloudové platformy SAP.

V ověřováním identit cloudové platformy SAP, zadejte hodnotu **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dva uživatele.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ověřováním identit cloudové platformy SAP, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvořit testovacího uživatele s ověřováním identit cloudové platformy SAP](#create-an-sap-cloud-platform-identity-authentication-test-user) mít protějšek Britta Simon v SAP ověřováním identit cloudové platformy, který je propojený s Azure AD reprezentace uživatele.
1. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ověřováním identit cloudové platformy SAP.

**Ke konfiguraci Azure AD jednotné přihlašování s ověřováním identit cloudové platformy SAP, proveďte následující kroky:**

1. Na webu Azure Portal na **ověřováním identit cloudové platformy SAP** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. V **jednotného přihlašování** dialogovém okně **přihlašování na základě SAML**vyberte **režimu** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

1. Pokud chcete nakonfigurovat aplikace v **zprostředkovatele identity** zahájeno v režimu **SAP Cloud Platform Identity ověřování domény a adresy URL** části, proveďte následující kroky:  

    ![SAP Cloud Platform Identity ověřování domény a adresy URL jednotného přihlašování – informace](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. V **identifikátor** pole, zadejte adresu URL s následujícím vzorem: `<IAS-tenant-id>.accounts.ondemand.com`

    b. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Obraťte se [tým podpory SAP Cloud Platform Identity ověřování klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) k získání těchto hodnot. Pokud nevíte, hodnota identifikátoru, přečtěte si dokumentaci ověřováním identit cloudové platformy SAP o [konfigurace Tenanta SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

1. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, vyberte **zobrazit pokročilé nastavení URL**.

    ![SAP Cloud Platform Identity ověřování domény a adresy URL jednotného přihlašování – informace](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Použijte prosím vaše konkrétní obchodní aplikace přihlašovací adresa URL. Obraťte se [tým podpory SAP Cloud Platform Identity ověřování klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Pokud máte jakékoli nejisté.

1. V **podpisový certifikát SAML** vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

1. Aplikace ověřováním identit cloudové platformy SAP očekává, že kontrolní výrazy SAML v určitém formátu. Správa hodnoty z těchto atributů **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad formátu. 

    ![Konfigurace jednotného přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

1. Pokud vaše aplikace SAP očekává, že atribut jako **firstName**, přidejte **firstName** atribut **atributy uživatele** části. Tato možnost je dostupná v **jednotného přihlašování** dialogovému oknu **atributy tokenu SAML** dialogové okno...

    a. Chcete-li otevřít **přidat atribut** dialogu **přidat atribut**. 
    
    ![Konfigurace jednotného přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. V **název** zadejte název atributu **firstName**.
    
    c. Z **hodnotu** vyberte hodnotu atributu **user.givenname**.
    
    d. Vyberte **OK**.

1. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování tlačítko Uložit](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

1. V **SAP Cloud Platform Identity ověřování konfigurace** vyberte **konfigurace SAP ověřováním identit cloudové platformy** otevřít **nakonfigurovatpřihlašování**okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace ověřování SAP Cloud Platform Identity](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, přejděte do konzoly pro správu ověřováním identit cloudové platformy SAP. Adresa URL má následující vzor: `https://<tenant-id>.accounts.ondemand.com/admin`. Pak si můžete přečíst dokumentaci o ověřováním identit cloudové platformy SAP na [integrace s Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

1. Na webu Azure Portal, vyberte **Uložit** tlačítko.

1. Pokračujte s tímto pouze v případě, že chcete přidat a povolení jednotného přihlašování pro jiné aplikace SAP. Opakujte kroky v části **přidání SAP ověřováním identit cloudové platformy z Galerie**.

1. Na webu Azure Portal na **ověřováním identit cloudové platformy SAP** integrace stránce aplikace vyberte **propojené přihlášení**.

    ![Konfigurace propojené přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

1. Uložte konfiguraci.

>[!NOTE] 
>Nová aplikace využívá konfiguraci jednotné přihlašování předtím u aplikace SAP. Ujistěte se, že používáte stejné podnikové zprostředkovatelé Identity v konzole pro správu ověřováním identit cloudové platformy SAP.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com) při nastavení aplikace!  Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a přístup k vložený dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace na [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Vytvořit testovacího uživatele s ověřováním identit cloudové platformy SAP

Není nutné vytvořit uživatele v ověřováním identit cloudové platformy SAP. Uživatelé, kteří jsou v úložišti uživatele Azure AD můžete použít funkci jednotného přihlašování.

Ověřováním identit cloudové platformy SAP podporuje možnost federace identit. Tato možnost umožňuje aplikace a zjistit, zda existují uživatelé, kteří jsou ověřeni zprostředkovatelem podniková identita v uživatele úložiště z SAP ověřováním identit cloudové platformy. 

Ve výchozím nastavení je zakázána možnost federace identit. Pokud je povolené federace identit, jenom uživatelé, které jsou importovány do ověřováním identit cloudové platformy SAP přístup k aplikaci. 

Další informace o tom, jak povolit nebo zakázat federaci identit s ověřováním identit cloudové platformy SAP, najdete v části "Povolení Identity federace s SAP ověřováním identit cloudové platformy" v [konfigurace federace identit se službou Ověření Identity uživatele Store SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ověřováním identit cloudové platformy SAP.

![Přiřazení role uživatele][200] 

**Chcete-li přiřadit Britta Simon ověřováním identit cloudové platformy SAP, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Dále přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ověřováním identit cloudové platformy SAP**.

    ![V seznamu aplikací na odkaz ověřováním identit cloudové platformy SAP](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

1. Vyberte **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici ověřováním identit cloudové platformy SAP na přístupovém panelu, získáte automaticky přihlášeni k aplikaci ověřováním identit cloudové platformy SAP.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
