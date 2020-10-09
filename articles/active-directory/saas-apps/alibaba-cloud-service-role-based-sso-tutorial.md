---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s cloudovou službou Alibaba (SSO založené na rolích) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudovou službou Alibaba (jednotné přihlašování založené na rolích).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.openlocfilehash: 4ffaad77a34be66d06f8f0033731d0496e444e52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715879"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s cloudovou službou Alibaba (jednotné přihlašování založené na rolích)

V tomto kurzu se dozvíte, jak integrovat cloudovou službu Alibaba (SSO založená na rolích) s Azure Active Directory (Azure AD). Když integrujete cloudovou službu Alibaba (SSO založená na rolích) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudové službě Alibaba (SSO založené na rolích).
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Alibaba cloudové službě (SSO založené na rolích) s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Alibaba Cloud Service (jednotné přihlašování založené na rolích) – odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloudová služba Alibaba (SSO založená na rolích) podporuje jednotné přihlašování (SSO) **IDP** .

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Přidání cloudové služby Alibaba (SSO založené na rolích) z Galerie

Pokud chcete nakonfigurovat integraci Alibaba cloudové služby (SSO založená na rolích) do Azure AD, musíte z Galerie přidat cloudovou službu Alibaba (SSO založená na rolích) do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Alibaba cloudovou službu (SSO založené na rolích)** .
1. Z panelu výsledků vyberte **cloudovou službu Alibaba (SSO založené na rolích)** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.
5. Na stránce **Alibaba Cloud Service (SSO na základě rolí)** klikněte na **vlastnosti** v navigačním PODOKNĚ vlevo a zkopírujte **ID objektu** a uložte ho do svého počítače pro následné použití.

    ![Konfigurace vlastností](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD pro cloudovou službu Alibaba (jednotné přihlašování založené na rolích)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí cloudové služby Alibaba (SSO založené na rolích) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudové službě Alibaba (jednotné přihlašování na základě rolí).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudové služby Alibaba (SSO založené na rolích), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte Role-Based jeden Sign-On v cloudové službě Alibaba](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – umožníte uživatelům používat tuto funkci.
    1. **[Nakonfigurujte Alibaba cloudovou službu (jednotné přihlašování na základě rolí)](#configure-alibaba-cloud-service-role-based-sso-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte testovacího uživatele Alibaba cloudovou službu (SSO založená na rolích)](#create-alibaba-cloud-service-role-based-sso-test-user)** – bude mít protějšek Britta Simon v cloudové službě v Alibaba (jednotné přihlašování založené na rolích), která je propojená s reprezentací uživatele v Azure AD.
3. **[Testování jednotného jednotného přihlašování](#test-sso)** – ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **cloudová služba Alibaba (jednotné přihlašování na základě rolí)** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    >[!NOTE]
    >Z této [adresy URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) budete dostávat metadata poskytovatele služeb.

    a. Klikněte na **nahrát soubor metadat**.

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi automaticky naplní v části ALIBABA Cloud Service (SSO založené na rolích) – textové pole:

    > [!Note]
    > Pokud se hodnoty **adresy URL** pro **identifikátor** a odpověď nezískají automaticky, vyplníte hodnoty ručně podle vašich požadavků.

1. Cloudová služba Alibaba (SSO založená na rolích) vyžaduje, aby byly ve službě Azure AD nakonfigurované role. Deklarace role je předem nakonfigurovaná, takže ji nemusíte konfigurovat, ale je potřeba, abyste je ve službě Azure AD mohli vytvořit pomocí tohoto [článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení cloudové služby Alibaba (jednotné přihlašování založené na rolích)** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke cloudové službě Alibaba (SSO založené na rolích).

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **cloudovou službu Alibaba (jednotné přihlašování na základě rolí)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. Na kartě **Uživatelé a skupiny** vyberte v seznamu uživatelů možnost U2 a klikněte na **Vybrat**. Pak klikněte na **přiřadit**.

    ![Snímek obrazovky zobrazuje podokno Add-Assignment Alibaba bez výběru uživatelů a skupin.](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Zobrazit přiřazenou roli a testovat cloudovou službu Alibaba (jednotné přihlašování založené na rolích)

    ![Snímek obrazovky s přiřazenou ochranou pro uživatele je popsán.](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Po přiřazení uživatele (U2) je vytvořená role automaticky připojena k uživateli. Pokud jste vytvořili více rolí, musíte k uživateli podle potřeby připojit příslušnou roli. Pokud chcete implementovat jednotné přihlašování založené na rolích z Azure AD do několika cloudových účtů Alibaba, opakujte předchozí kroky.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurace Role-Based jednoho Sign-On v cloudové službě Alibaba

1. Přihlaste se ke konzole Alibaba Cloud [RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) pomocí Account1.

2. V navigačním podokně na levé straně vyberte **jednotné přihlašování**.

3. Na kartě **jednotné přihlašování na základě rolí** klikněte na **vytvořit IDP**.

4. Na zobrazené stránce zadejte `AAD` do pole název IDP, zadejte popis do pole **Poznámka** , klikněte na tlačítko **nahrát** a nahrajte soubor federačních metadat, který jste stáhli dříve, a klikněte na **OK**.

5. Po úspěšném vytvoření IdP klikněte na **vytvořit roli RAM**.

6. V poli **název role RAM** zadejte `AADrole` vyberte v `AAD` rozevíracím seznamu **Vybrat IDP** a klikněte na OK.

    >[!NOTE]
    >Podle potřeby můžete roli udělit oprávnění. Po vytvoření IdP a odpovídající role doporučujeme, abyste uložili ARNs pro IdP a roli pro následné použití. ARNs můžete získat na stránce informace o IdP a na stránce s informacemi o roli.

7. Přidružte Alibaba cloudovou roli RAM (AADrole) k uživateli Azure AD (U2): Pokud chcete roli RAM přidružit k uživateli Azure AD, musíte ve službě Azure AD vytvořit roli pomocí následujících kroků:

    a. Přihlaste se k [průzkumníku Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).

    b. Kliknutím na **Upravit oprávnění** získáte požadovaná oprávnění pro vytvoření role.

    ![Snímek obrazovky s odkazem na změnu oprávnění zobrazuje Průzkumník grafů.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. V seznamu vyberte následující oprávnění a klikněte na **Upravit oprávnění**, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky zobrazuje oprávnění pro výběr: Directory. AccessAsUser. All, Directory. Read. All a Directory. četl. All.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Po udělení oprávnění se znovu přihlaste do Průzkumníka graphu.

    d. Na stránce Průzkumník grafů vyberte v prvním rozevíracím seznamu **získat** a **beta** z druhého rozevíracího seznamu. Potom zadejte `https://graph.microsoft.com/beta/servicePrincipals` do pole vedle rozevíracího seznamu a klikněte na **Spustit dotaz**.

    ![Snímek obrazovky s vybraným možnostmi získat a beta zobrazuje Průzkumníka graphu a tlačítko spustit dotaz se vyvolalo.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Pokud používáte více adresářů, můžete zadat `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` do pole dotazu.

    e. V části **Náhled odpovědi** rozbalte vlastnost AppRoles z instančního objektu pro následné použití.

    ![Snímek obrazovky se v části Náhled odpovědi zobrazí jako prostý text, kde můžete získat vlastnost appRoles.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Vlastnost appRoles můžete vyhledat zadáním `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` do pole dotazu. Všimněte si, že `objectID` je ID objektu, které jste zkopírovali ze stránky **vlastností** Azure AD.

    f. Vraťte se do Průzkumníka graphu, změňte metodu z možnosti **získat** na **opravu**, vložte následující obsah do části **Text žádosti** a klikněte na **Spustit dotaz**:
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
    > `value`Je ARNs IDP a role, kterou jste vytvořili v konzole RAM. Tady můžete podle potřeby přidat víc rolí. Azure AD pošle hodnotu těchto rolí jako hodnotu deklarace v odpovědi SAML. Nové role však můžete přidat pouze po `msiam_access` části operace opravy. Pro vyhlazení procesu vytváření doporučujeme, abyste pro generování ID v reálném čase použili generátor ID, jako je například generátor GUID.

    například Po opravě ' instančního objektu ' s požadovanou rolí připojte roli k uživateli Azure AD (U2) podle postupu v části **přiřazení testovacího uživatele Azure AD** v tomto kurzu.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Konfigurace služby Alibaba pro jednotné přihlašování na základě rolí

Ke konfiguraci jednotného přihlašování na straně **Alibaba cloudové služby (SSO založené na rolích)** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [ALIBABA cloudové služby (SSO na základě rolí)](https://www.aliyun.com/service/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Vytvořit testovacího uživatele Alibaba cloudovou službu (SSO založené na rolích)

V této části vytvoříte uživatele s názvem Britta Simon v cloudové službě Alibaba (SSO na základě rolí). Pracujte s [týmem podpory Alibaba Cloud Service (SSO založená na rolích)](https://www.aliyun.com/service/) a přidejte uživatele do platformy Alibaba cloudová služba (SSO založená na rolích). Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

Po dokončení předchozích konfigurací otestujte cloudovou službu Alibaba (SSO založené na rolích) pomocí následujících kroků:

1. V Azure Portal přejděte na stránku **cloudová služba Alibaba (SSO založená na rolích)** , vyberte **jednotné přihlašování**a klikněte na **test**.

    ![Snímek obrazovky s tlačítkem testu zobrazuje testovací jednotné přihlašování pomocí cloudové služby Alibaba.](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klikněte na **Přihlásit se jako aktuální uživatel**.

    ![Snímek obrazovky ukazuje odkaz Přihlásit se jako aktuální uživatel.](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na stránce Výběr účtu vyberte možnost U2.

    ![Snímek obrazovky se zobrazí možnost S možností přihlášení s vybraným příznakem uživatele.](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Zobrazí se následující stránka s oznámením, že jednotné přihlašování na základě rolí je úspěšné.

    ![Snímek obrazovky se zobrazí stránka Produkty & služby, která indikuje, že test proběhl úspěšně. ](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si cloudovou službu Alibaba (SSO založená na rolích) se službou Azure AD](https://aad.portal.azure.com/)

