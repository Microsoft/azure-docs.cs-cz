---
title: 'Kurz: Integrace Azure Active Directory s Cloudovou službou Alibaba (jednotné přihlašování založené na roli) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Alibaba Cloudovou službu (jednotné přihlašování založené na roli).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b154b8ce55c381f1398c696bc439067dccfab
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785758"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Kurz: Integrace Azure Active Directory s Cloudovou službou Alibaba (jednotné přihlašování založené na roli)

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Alibaba cloudové služby (jednotné přihlašování založené na roli).
Integrace Alibaba cloudové služby (na základě rolí SSO) s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Alibaba cloudových služeb (jednotné přihlašování založené na roli).
* Můžete povolit uživatelům, aby se automaticky přihlášeni ke cloudové službě Alibaba (jednotné přihlašování založené na roli) (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cloudovou službou Alibaba (jednotné přihlašování na základě rolí), potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Alibaba cloudové služby (jednotné přihlašování založené na roli) jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Alibaba Cloudovou službu (jednotné přihlašování založené na roli) **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Přidání Alibaba Cloudovou službu (jednotné přihlašování založené na roli) z Galerie

Pokud chcete nakonfigurovat integraci Alibaba Cloudovou službu (jednotné přihlašování založené na roli) do služby Azure AD, potřebujete přidat Alibaba Cloudovou službu (jednotné přihlašování založené na roli) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Alibaba Cloudovou službu (jednotné přihlašování založené na roli) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Alibaba cloudové služby (jednotné přihlašování založené na roli)** vyberte **Alibaba cloudové služby (jednotné přihlašování založené na roli)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Alibaba cloudové služby (jednotné přihlašování založené na roli) v seznamu výsledků](common/search-new-app.png)

5. Na **Alibaba cloudové služby (jednotné přihlašování založené na roli)** klikněte na **vlastnosti** v levém navigačním podokně a zkopírujte **ID objektu** a uložte ho do počítače pozdější použití.

    ![Vlastnosti konfigurace](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s Cloudovou službou Alibaba (jednotné přihlašování založené na roli) na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v cloudové službě Alibaba (jednotné přihlašování založené na roli).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cloudovou službou Alibaba (jednotné přihlašování na základě rolí), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace na základě rolí jednotné přihlašování v cloudové službě Alibaba](#configure-role-based-single-sign-on-in-alibaba-cloud-service)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Alibaba Cloudovou službu (jednotné přihlašování založené na roli) Single Sign-On](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Alibaba Cloudovou službu (jednotné přihlašování založené na roli)](#create-alibaba-cloud-service-role-based-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon v cloudové službě Alibaba (jednotné přihlašování založené na roli) je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Cloudovou službou Alibaba (jednotné přihlašování na základě rolí), proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Alibaba cloudové služby (jednotné přihlašování založené na roli)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    >[!NOTE]
    >Zobrazí se metadata poskytovatele služeb z tohoto [adresy URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v textovém poli části Alibaba Cloudovou službu (jednotné přihlašování založené na roli):

    ![image](common/idp-intiated.png)

    > [!Note]
    > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty nechcete získat automaticky vyplní, a potom zadejte hodnoty ručně podle vašich požadavků.

5. Alibaba cloudové služby (jednotné přihlašování založené na roli) aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikony otevřete **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. Kromě výše Alibaba Cloudovou službu (jednotné přihlašování založené na roli) aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Obor názvů | Zdrojový atribut|
    | ---------------| ------------| --------------- |
    | Role | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Po klepnutí na [tady](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) vědět, jak nakonfigurovat **Role** ve službě Azure AD

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavení Alibaba Cloudovou službu (jednotné přihlašování založené na roli)** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurace na základě rolí jednotné přihlašování v Alibaba cloudové služby

1. Přihlaste se do cloudu Alibaba [RAM konzoly](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) pomocí Account1.

2. V levém navigačním podokně vyberte **jednotného přihlašování**.

3. Na **jednotného přihlašování na základě rolí** klikněte na tlačítko **vytvořit zprostředkovatele identity**.

4. Na zobrazené stránce zadejte `AAD` v poli Název zprostředkovatele identity, zadejte popis do **Poznámka** pole, klikněte na tlačítko **nahrát** nahrát soubor metadat federace jste stáhli dříve, a klikněte na  **OK**.

5. Po úspěšném vytvoření zprostředkovatele identity, klikněte na tlačítko **vytvořit roli RAM**.

6. V **název Role paměti RAM** pole zadejte `AADrole`vyberte `AAD` z **vyberte zprostředkovatele identity** rozevíracího seznamu a klikněte na tlačítko OK.

    >[!NOTE]
    >Podle potřeby můžete udělit oprávnění k roli. Po vytvoření zprostředkovatele identity a odpovídající roli, doporučujeme uložit ARNs zprostředkovatele identity a role pro pozdější použití. Můžete získat ARNs na stránce informace o zprostředkovateli identity a informace o stránce role.

7. Přidružte roli Alibaba cloudu RAM (AADrole) pomocí uživatele Azure AD (u2): Pokud chcete přidružit RAM role uživatele Azure AD, musíte vytvořit role ve službě Azure AD pomocí následujících kroků:

    a. Přihlaste se k [Azure AD Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Klikněte na tlačítko **změnit oprávnění** získat požadovaná oprávnění pro vytvoření role.

    ![Konfigurace grafu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Vyberte následující oprávnění ze seznamu a klikněte na tlačítko **upravit oprávnění**, jak je znázorněno na následujícím obrázku.

    ![Konfigurace grafu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Jakmile se neudělí oprávnění, přihlaste se k Graph Exploreru znovu.

    d. Na stránce Graph Exploreru vyberte **získat** v prvním rozevíracím seznamu a **beta** z druhého rozevíracího seznamu. Zadejte `https://graph.microsoft.com/beta/servicePrincipals` v poli vedle rozevíracích seznamů a klikněte na **spustit dotaz**.

    ![Konfigurace grafu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Pokud používáte více adresářů, můžete zadat `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` do pole dotazu.

    e. V **náhled odpovědi** části, extrahovat vlastnost appRoles z "Service Principal" pro pozdější použití.

    ![Konfigurace grafu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Vlastnost appRoles můžete najít tak, že zadáte `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` do pole dotazu. Všimněte si, `objectID` je ID objektu, který jste zkopírovali ze služby Azure AD **vlastnosti** stránky.

    f. Přejděte zpět do Průzkumníku grafu, změňte metodu z **získat** k **oprava**, vložte následující obsah do **tělo požadavku** části a klikněte na tlačítko **spustit dotaz** :
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` Je ARNs zprostředkovatele identity a role vytvořené v konzole paměti RAM. Tady můžete přidat víc rolí, podle potřeby. Azure AD pošle hodnotu z těchto rolí jako hodnota deklarace identity v odpověď SAML. Však lze přidat pouze nové role po `msiam_access` část pro operaci patch. Plynulé proces vytváření, doporučujeme používat generátor ID, jako je například GUID Generator ke generování ID v reálném čase.

    g. Poté, co objekt služby je opravit pomocí požadované role, připojte roli pomocí uživatele Azure AD (u2) pomocí následujících kroků **přiřadit testovacího uživatele Azure AD** části kurzu.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Konfigurace Alibaba cloudové služby (jednotné přihlašování založené na roli) jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Alibaba cloudové služby (jednotné přihlašování založené na roli)** straně, je nutné odeslat na stažený **kód XML metadat federace** a odpovídající zkopírován adresy URL z webu Azure portal [ Tým podpory Alibaba Cloudovou službu (jednotné přihlašování založené na roli)](https://www.aliyun.com/service/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup ke cloudové službě Alibaba (jednotné přihlašování založené na roli).

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Alibaba cloudové služby (jednotné přihlašování založené na roli)**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Alibaba cloudové služby (jednotné přihlašování založené na roli)**.

    ![Odkaz Alibaba Cloudovou službu (jednotné přihlašování založené na roli) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. Na **uživatelů a skupin** kartu, vyberte u2 ze seznamu uživatelů a klikněte na tlačítko **vyberte**. Potom klikněte na **přiřadit**.

    ![Konfigurace testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Zobrazení přiřazených rolí a testování Alibaba Cloudovou službu (jednotné přihlašování založené na roli).

    ![Konfigurace testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Po přiřazení uživatele (u2) vytvořené role je automaticky připojen k uživateli. Pokud vytvoříte více rolí, musíte připojit příslušné role uživatele podle potřeby. Pokud chcete implementovat na základě rolí jednotné přihlašování z Azure AD, k několika účtům Alibaba Cloud, zopakujte předchozí kroky.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Vytvořit testovacího uživatele Alibaba cloudové služby (na základě rolí jednotné přihlašování)

V této části vytvořte uživatele Britta Simon v cloudové službě Alibaba (jednotné přihlašování založené na roli). Práce s [Alibaba cloudové služby (jednotné přihlašování založené na roli) tým podpory](https://www.aliyun.com/service/) přidat uživatele na platformě Alibaba Cloudovou službu (jednotné přihlašování založené na roli). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

Po dokončení předchozí konfigurace testů Alibaba Cloudovou službu (jednotné přihlašování založené na roli) pomocí následujících kroků:

1. Na webu Azure Portal, přejděte na **Alibaba cloudové služby (jednotné přihlašování založené na roli)** stránce **jednotného přihlašování**a klikněte na tlačítko **Test**.

    ![Konfigurace testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klikněte na **Přihlásit se jako aktuální uživatel**.

    ![Konfigurace testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na stránce Výběr účtu vyberte u2.

    ![Konfigurace testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Zobrazí se na následující stránce, označující, jestli jednotného přihlašování na základě rolí se úspěšně dokončila.

    ![Konfigurace testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

