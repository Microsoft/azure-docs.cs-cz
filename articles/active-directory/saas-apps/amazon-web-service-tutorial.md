---
title: 'Kurz: Azure Active Directory integrace s Amazon Web Services (AWS) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 672a3571202b92232bd45a42254a43019f6a9796
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617333"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Kurz: Integrace Amazon Web Services (AWS) s Azure Active Directory

V tomto kurzu se naučíte, jak integrovat Amazon Web Services (AWS) s Azure Active Directory (Azure AD). Když integrujete AWS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AWS.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AWS svým účtům Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram vztahu Azure AD a AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Pro více instancí můžete nakonfigurovat více identifikátorů. Příklad:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Díky těmto hodnotám Azure AD odstraní hodnotu **#** a pošle správnou hodnotu `https://signin.aws.amazon.com/saml` jako adresu URL cílové skupiny v tokenu SAML.

Tento postup doporučujeme z následujících důvodů:

- Každá aplikace poskytuje jedinečný certifikát x509. Každá instance instance aplikace AWS může mít jiné datum vypršení platnosti certifikátu, které se dá spravovat na základě jednotlivých účtů AWS. V tomto případě je snazší celková změna certifikátu.

- Můžete povolit zřizování uživatelů pomocí aplikace AWS ve službě Azure AD a potom naše služba načte všechny role z tohoto účtu AWS. V aplikaci nemusíte ručně přidávat ani aktualizovat role AWS.

- Vlastník aplikace můžete přiřadit individuálně pro aplikaci. Tato osoba může spravovat aplikaci přímo ve službě Azure AD.

> [!Note]
> Ujistěte se, že používáte jenom aplikaci Galerie.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným AWSm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. AWS podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="add-aws-from-the-gallery"></a>Přidání AWS z Galerie

Pokud chcete nakonfigurovat integraci AWS do služby Azure AD, musíte přidat AWS z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Amazon Web Services (AWS)** .
1. Na panelu výsledků vyberte **Amazon Web Services (AWS)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí AWS pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v AWS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AWS, dokončete následující stavební bloky:

1. **NAKONFIGURUJTE jednotné přihlašování Azure AD** , aby vaši uživatelé mohli používat tuto funkci.
2. **NAKONFIGURUJTE AWS** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **Vytvořte testovacího uživatele Azure AD** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. **Přiřaďte testovacímu uživateli Azure AD** povolení B. Simon pro použití jednotného přihlašování služby Azure AD.
5. **Vytvořte AWS testovacího uživatele** , který bude mít protějšek B. Simon v AWS, který je propojený s zastoupením uživatele Azure AD.
6. **Otestujte jednotné přihlašování** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Amazon Web Services (AWS)** najděte část **Správa** a vyberte jednotné přihlašování.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou pera](common/edit-urls.png)

4. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit výběrem možnosti **Uložit**.

5. Pokud konfigurujete více než jednu instanci, zadejte hodnotu identifikátoru. Od druhé instance a dále použijte následující formát, včetně **#** znaménka k zadání jedinečné hodnoty hlavního názvu služby (SPN).

    `https://signin.aws.amazon.com/saml#2`

6. Aplikace AWS očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Výběrem ikony pera otevřete dialogové okno atributy uživatele.

    ![Snímek obrazovky dialogového okna atributů uživatele se zvýrazněnou ikonou pera](common/edit-attribute.png)

7. Kromě předchozích atributů očekává aplikace AWS několik dalších atributů, které se vrátí zpět v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky a přidejte tak atribut tokenu SAML.

    | Name  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "zadejte hodnotu v rozmezí 900 sekund (15 minut) až 43200 sekund (12 hodin)" |  https://aws.amazon.com/SAML/Attributes |

    a. Výběrem možnosti **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky s deklarací identity uživatelů s přidáním nové deklarace identity a zvýrazněnou možností Uložit](common/new-save-attribute.png)

    ![Snímek obrazovky dialogového okna spravovat deklarace identity uživatelů](common/new-attribute-details.png)

    b. Do pole **název**zadejte název atributu zobrazeného pro tento řádek.

    c. Do pole **obor názvů**zadejte hodnotu oboru názvů zobrazenou pro tento řádek.

    d. Ve **zdroji**vyberte **atribut**.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Vyberte **OK**.

    g. Vyberte **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML federačních metadat**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Snímek obrazovky s oddílem podpisového certifikátu SAML se zvýrazněnou možností stažení](common/metadataxml.png)

1. V části **nastavit Amazon Web Services (AWS)** zkopírujte příslušné adresy URL podle vašeho požadavku.

   ![Snímek obrazovky s nastavením Amazon Web Services (AWS) v oddílu s zvýrazněnými adresami URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>Konfigurace AWS

1. V jiném okně prohlížeče se přihlaste k webu AWS společnosti jako správce.

2. Vyberte **AWS Home (domů**).

    ![Snímek obrazovky AWS společnosti se zvýrazněnou ikonou domovské stránky AWS][11]

3. Vyberte **Správa identit a přístupu**.

    ![Snímek stránky AWS Services se zvýrazněným nástrojem IAM][12]

4. Vyberte **Zprostředkovatelé** > identity**vytvořit poskytovatele**.

    ![Snímek stránky IAM se zvýrazněnou možností poskytovatelé identity a poskytovatel pro vytváření][13]

5. Na stránce **Konfigurovat poskytovatele** proveďte následující kroky:

    ![Snímek obrazovky s konfigurací zprostředkovatele][14]

    a. Jako **Typ poskytovatele**vyberte **SAML**.

    b. Jako **název poskytovatele**zadejte název zprostředkovatele (například: *WAAD*).

    c. Pokud chcete nahrát stažený **soubor metadat** z Azure Portal, vyberte **zvolit soubor**.

    d. Vyberte **Další krok**.

6. Na stránce **ověřit informace o poskytovateli** vyberte **vytvořit**.

    ![Snímek obrazovky s ověřením informací o poskytovateli se zvýrazněným výrazem vytvořit][15]

7. Vyberte **role** > **vytvořit roli**.

    ![Snímek stránky rolí][16]

8. Na stránce **vytvořit roli** proveďte následující kroky:  

    ![Snímek obrazovky s vytvořením stránky role][19]

    a. V části **Vybrat typ důvěryhodné entity**vyberte možnost **federace SAML 2,0**.

    b. V části **zvolte poskytovatele saml 2,0**vyberte **poskytovatele SAML** , který jste předtím vytvořili (například: *WAAD*).

    c. Zaškrtněte políčko **povolí přístup k programovým a AWS Management Console**.
  
    d. Vyberte **další: Oprávnění**.

9. V dialogovém okně **připojit zásady oprávnění** Připojte příslušné zásady podle vaší organizace. Pak vyberte **další: Zkontrolujte**.  

    ![Snímek obrazovky dialogového okna připojit zásadu oprávnění][33]

10. V dialogovém okně **Kontrola** proveďte následující kroky:

    ![Snímek obrazovky s dialogovým oknem pro kontrolu][34]

    a. Do **název role**zadejte název své role.

    b. Do pole **Popis role**zadejte popis.

    c. Vyberte **vytvořit roli**.

    d. Podle potřeby vytvořte tolik rolí a namapujte je na zprostředkovatele identity.

11. Použijte přihlašovací údaje účtu služby AWS pro načtení rolí z účtu AWS ve zřizování uživatelů Azure AD. V takovém případě otevřete domovskou stránku konzoly AWS.

12. Vyberte **služby**. V části **zabezpečení, identita & dodržování předpisů**vyberte **IAM**.

    ![Snímek obrazovky domovské stránky konzoly AWS se zvýrazněnými službami a nástrojem IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. V části IAM vyberte **zásady**.

    ![Snímek obrazovky oddílu IAM se zvýrazněnými zásadami](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Vytvořte novou zásadu tak, že vyberete **vytvořit zásadu** pro načtení rolí z účtu AWS ve zřizování uživatelů Azure AD.

    ![Snímek stránky pro vytvoření role se zvýrazněnou možností vytvořit zásadu](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Vytvořte vlastní zásadu pro načtení všech rolí z účtů AWS.

    ![Snímek stránky pro vytvoření zásady se zvýrazněným kódem JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. V části **vytvořit zásadu**vyberte kartu **JSON** .

    b. V dokumentu zásady přidejte následující kód JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Vyberte možnost **zkontrolovat zásadu** a zásadu ověřte.

    ![Snímek obrazovky s vytvořením stránky zásad](./media/amazon-web-service-tutorial/policy5.png)

16. Definujte novou zásadu.

    ![Snímek stránky pro vytvoření zásady se zvýrazněnými poli název a popis](./media/amazon-web-service-tutorial/policy2.png)

    a. Jako **název**zadejte **AzureAD_SSOUserRole_Policy**.

    b. Pro **Popis**zadejte **tuto zásadu, která umožní načtení rolí z účtů AWS**.

    c. Vyberte **vytvořit zásadu**.

17. Ve službě AWS IAM vytvořte nový uživatelský účet.

    a. V konzole AWS IAM vyberte **Uživatelé**.

    ![Snímek obrazovky konzoly AWS IAM se zvýrazněnými uživateli](./media/amazon-web-service-tutorial/policy3.png)

    b. Chcete-li vytvořit nového uživatele, vyberte možnost **Přidat uživatele**.

    ![Snímek obrazovky s tlačítkem Přidat uživatele](./media/amazon-web-service-tutorial/policy4.png)

    c. V části **Přidat uživatele** :

    ![Snímek obrazovky se stránkou přidat uživatele se zvýrazněným uživatelským jménem a typem přístupu](./media/amazon-web-service-tutorial/adduser1.png)

    * Zadejte uživatelské jméno jako **AzureADRoleManager**.

    * Jako typ přístupu vyberte programový **přístup**. Tímto způsobem může uživatel vyvolat rozhraní API a načíst role z účtu AWS.

    * Vyberte **Další oprávnění**.

18. Vytvoří novou zásadu pro tohoto uživatele.

    ![Snímek obrazovky s přidáním uživatele](./media/amazon-web-service-tutorial/adduser2.png)

    a. Vyberte možnost **připojit existující zásady přímo**.

    b. V části Filter **AzureAD_SSOUserRole_Policy**(filtr) vyhledejte nově vytvořenou zásadu.

    c. Vyberte zásadu a potom vyberte **další: Zkontrolujte**.

19. Zkontrolujte zásadu pro připojeného uživatele.

    ![Snímek obrazovky se stránkou přidat uživatele s zvýrazněným možností vytvořit uživatele](./media/amazon-web-service-tutorial/adduser3.png)

    a. Zkontrolujte uživatelské jméno, typ přístupu a zásadu namapované na uživatele.

    b. Vyberte **vytvořit uživatele**.

20. Stáhněte si přihlašovací údaje uživatele.

    ![Snímek obrazovky s přidáním uživatele](./media/amazon-web-service-tutorial/adduser4.png)

    a. Zkopírujte **ID přístupového klíče** uživatele a **tajný přístupový klíč**.

    b. Pokud chcete načíst role z konzoly AWS, zadejte tyto přihlašovací údaje do oddílu zřizování uživatelů Azure AD.

    c. Vyberte **Zavřít**.

21. Na portálu pro správu Azure AD, v aplikaci AWS, pokračujte na **zřizování**.

    ![Snímek obrazovky aplikace AWS se zvýrazněným zřizováním](./media/amazon-web-service-tutorial/provisioning.png)

22. Zadejte přístupový klíč a tajný klíč do polí **ClientSecret** a **tajného tokenu** v uvedeném pořadí.

    ![Snímek obrazovky dialogového okna přihlašovací údaje správce](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Do pole **ClientSecret** zadejte přístupový klíč uživatele AWS.

    b. Do pole **token tajného klíče** zadejte tajný klíč uživatele AWS.

    c. Vyberte **Test připojení**.

    d. Uložte nastavení výběrem možnosti **Uložit**.

23. V části **Nastavení** pro **stav zřizování**vyberte **zapnuto**. Potom vyberte **Uložit**.

    ![Snímek obrazovky s oddílem nastavení se zvýrazněnou možností](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   
   a. Do pole **Název** zadejte `B.Simon`.  
   b. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.   
   c. Vyberte možnost **Zobrazit heslo**a zapište ho. Potom vyberte **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure tím, že udělíte přístup k AWS.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Amazon Web Services (AWS)** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky s oddílem spravovat se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek obrazovky s přidáním uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte **B. Simon**. Pak zvolte **Vybrat**.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte **Vybrat**.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Když na přístupovém panelu vyberete dlaždici AWS, měli byste se automaticky přihlásit k AWS, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Známé problémy

 * V části **zřizování** se v podčásti **mapování** zobrazuje "načítání...". zpráva a nikdy nezobrazuje mapování atributů. Jediným podporovaným pracovním postupem zřizování je import rolí z AWS do služby Azure AD pro výběr během přiřazení uživatele nebo skupiny. Mapování atributů pro tuto vlastnost jsou předem určena a nelze je konfigurovat.
 
 * Oddíl **zřizování** podporuje jenom zadání jedné sady přihlašovacích údajů pro jednoho klienta AWS ve chvíli. Všechny importované role se zapisují do `appRoles` vlastnosti [ `servicePrincipal` objektu](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) Azure AD pro tenanta AWS. 
 
   Do Azure AD se dá přidat víc `servicePrincipals`tenantů AWS (reprezentovaný) z galerie pro zřizování. K dispozici je ale známý problém s tím, že není možné automaticky zapisovat všechny importované role z několika AWS `servicePrincipals` používaných k zřizování do jediného `servicePrincipal` používaného pro jednotné přihlašování. 
   
   Jako alternativní řešení můžete použít [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) k extrakci všech `appRoles` importovaných do každého AWS `servicePrincipal` , kde je zřizování nakonfigurované. Následně můžete přidat tyto řetězce rolí do AWS `servicePrincipal` , kde je nakonfigurované jednotné přihlašování.
 
* Role musí splňovat následující požadavky, aby měly nárok na Import z AWS do služby Azure AD:

  * Role musí mít přesně jednoho poskytovatele SAML definovaného v AWS.

  * Kombinovaná délka ARN role a ARN zprostředkovatele SAML pro importované role musí mít 119 znaků nebo méně.

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
