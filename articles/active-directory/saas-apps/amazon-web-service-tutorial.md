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
ms.openlocfilehash: f6640708905abc266b07b7b66f5da09aeb890f01
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823868"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Kurz: Integrace Amazon Web Services (AWS) s Azure Active Directory

V tomto kurzu se naučíte, jak integrovat Amazon Web Services (AWS) s Azure Active Directory (Azure AD). Když integrujete Amazon Web Services (AWS) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Amazon Web Services (AWS).
* Umožněte uživatelům, aby se automaticky přihlásili k Amazon Web Services (AWS) s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Můžete nakonfigurovat více identifikátorů pro více instancí, jak je uvedeno níže.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Pomocí těchto hodnot služba Azure AD odebere hodnotu **#** a pošle správnou hodnotu `https://signin.aws.amazon.com/saml` jako adresu URL cílové skupiny v tokenu SAML.

**Tuto metodu doporučujeme použít z následujících důvodů:**

a. Každá aplikace vám poskytne jedinečný certifikát x509. Každá instance instance aplikace AWS může mít jiné datum vypršení platnosti certifikátu, které se dá spravovat na základě jednotlivých účtů AWS. V tomto případě bude snazší celková změna certifikátu.

b. Můžete povolit zřizování uživatelů pomocí aplikace AWS ve službě Azure AD a potom naše služba načte všechny role z tohoto účtu AWS. V aplikaci nemusíte ručně přidávat ani aktualizovat role AWS.

c. Vlastník aplikace můžete přiřadit individuálně pro aplikaci, která může spravovat aplikaci přímo ve službě Azure AD.

> [!Note]
> Ujistěte se, že používáte jenom aplikaci Galerie.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Amazon Web Services (AWS) odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Amazon Web Services (AWS) podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie

Pokud chcete nakonfigurovat integraci Amazon Web Services (AWS) do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS Přidat Amazon Web Services (AWS) z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Amazon Web Services (AWS)** .
1. Z panelu výsledků vyberte **Amazon Web Services (AWS)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Amazon Web Services (AWS).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte Amazon Web Services (AWS)](#configure-amazon-web-services-aws)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořit testovacího uživatele Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** , aby měl protějšek B. Simon v Amazon Web Services (AWS), která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Amazon Web Services (AWS)** najděte část **Správa** a vyberte jednotné přihlašování.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

5. Pokud konfigurujete víc než jednu instanci, zadejte prosím hodnotu identifikátoru. Od druhé instance zadejte prosím hodnotu identifikátoru v následujícím formátu. Použijte **#** prosím znaménko k zadání jedinečné hodnoty hlavního názvu služby (SPN).

    `https://signin.aws.amazon.com/saml#2`

6. Aplikace Amazon Web Services (AWS) očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

7. Kromě výše uvedené aplikace Amazon Web Services (AWS) očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Name  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "zadejte hodnotu v rozmezí 900 sekund (15 minut) až 43200 sekund (12 hodin)" |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Do textového pole **obor názvů** zadejte hodnotu oboru názvů zobrazenou pro tento řádek.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **nastavit Amazon Web Services (AWS)** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Konfigurace Amazon Web Services (AWS)

1. V jiném okně prohlížeče se přihlaste do vaší Amazon Web Services (AWS) firemní web jako správce.

2. Klikněte na **AWS Home (domů**).

    ![Konfigurace jednotného přihlašování domů][11]

3. Klikněte na **Správa identit a přístupu**.

    ![Konfigurovat identitu jednotného přihlašování][12]

4. Klikněte na **Zprostředkovatelé identity**a pak klikněte na **vytvořit poskytovatele**.

    ![Konfigurace poskytovatele jednotného přihlašování][13]

5. Na stránce **Konfigurovat poskytovatele** proveďte následující kroky:

    ![Dialogové okno Konfigurace jednotného přihlašování][14]

    a. Jako **Typ poskytovatele**vyberte **SAML**.

    b. Do textového pole **název zprostředkovatele** zadejte název zprostředkovatele (například: *WAAD*).

    c. Pokud chcete nahrát stažený **soubor metadat** z Azure Portal klikněte na **zvolit soubor**.

    d. Klikněte na **Další krok**.

6. Na stránce **ověřit informace o poskytovateli** klikněte na **vytvořit**.

    ![Konfigurace ověření jednotného přihlašování][15]

7. Klikněte na **role**a pak klikněte na **vytvořit roli**.

    ![Konfigurace rolí jednotného přihlašování][16]

8. Na stránce **vytvořit roli** proveďte následující kroky:  

    ![Nakonfigurovat vztah důvěryhodnosti s jednotným přihlašováním][19]

    a. V části **Vybrat typ důvěryhodné entity**vyberte možnost **federace SAML 2,0** .

    b. V části **zvolte poskytovatele saml 2,0**vyberte **poskytovatele SAML** , kterého jste předtím vytvořili (například: *WAAD*)

    c. Zaškrtněte políčko **povolí přístup k programovým a AWS Management Console**.
  
    d. Klikněte **na další: Oprávnění**.

9. V dialogu **připojit zásady oprávnění** připojte ke své organizaci příslušné zásady. Klikněte **na další: Zkontrolujte**.  

    ![Konfigurace zásady jednotného přihlašování][33]

10. V dialogovém okně **Kontrola** proveďte následující kroky:

    ![Konfigurace kontroly jednotného přihlašování][34]

    a. Do textového pole **název role** zadejte název své role.

    b. Do textového pole **Popis role** zadejte popis.

    c. Klikněte na **vytvořit roli**.

    d. Podle potřeby vytvořte tolik rolí a namapujte je na zprostředkovatele identity.

11. Použijte přihlašovací údaje účtu služby AWS pro načtení rolí z účtu AWS při zřizování uživatelů Azure AD. V takovém případě otevřete domovskou stránku konzoly AWS.

12. Klikněte na zabezpečení **služby** ->  **, identita & dodržování předpisů** -> **IAM**.

    ![načítají se role z účtu AWS.](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. V části IAM vyberte kartu **zásady** .

    ![načítají se role z účtu AWS.](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Kliknutím na **vytvořit zásadu** pro načtení rolí z účtu AWS ve zřizování uživatelů Azure AD vytvořte novou zásadu.

    ![Vytváří se nová zásada.](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Vytvořte vlastní zásadu pro načtení všech rolí z účtů AWS provedením následujících kroků:

    ![Vytváří se nová zásada.](./media/amazon-web-service-tutorial/policy1.png)

    a. V části **vytvořit zásadu** klikněte na kartu **JSON** .

    b. V dokumentu zásady přidejte následující JSON.

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

    c. Kliknutím na **tlačítko zkontrolovat zásadu** zásadu ověřte.

    ![Definování nové zásady](./media/amazon-web-service-tutorial/policy5.png)

16. Pomocí následujících kroků definujte **nové zásady** :

    ![Definování nové zásady](./media/amazon-web-service-tutorial/policy2.png)

    a. Zadejte **název zásady** jako **AzureAD_SSOUserRole_Policy**.

    b. Můžete zadat **Popis** zásady, protože **tyto zásady umožní načtení rolí z účtů AWS**.

    c. Klikněte na tlačítko **vytvořit zásadu** .

17. Vytvořte nový uživatelský účet ve službě IAM AWS provedením následujících kroků:

    a. V konzole AWS IAM klikněte na navigace **uživatelů** .

    ![Definování nové zásady](./media/amazon-web-service-tutorial/policy3.png)

    b. Kliknutím na tlačítko **Přidat uživatele** vytvoříte nového uživatele.

    ![Přidat uživatele](./media/amazon-web-service-tutorial/policy4.png)

    c. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat uživatele](./media/amazon-web-service-tutorial/adduser1.png)

    * Zadejte uživatelské jméno jako **AzureADRoleManager**.

    * V poli Typ přístupu vyberte možnost **programový přístup** . Tímto způsobem může uživatel vyvolávat rozhraní API a načíst role z účtu AWS.

    * Klikněte na tlačítko **Další oprávnění** v pravém dolním rohu.

18. Nyní vytvořte novou zásadu pro tohoto uživatele provedením následujících kroků:

    ![Přidat uživatele](./media/amazon-web-service-tutorial/adduser2.png)

    a. Klikněte na tlačítko **připojit existující zásady přímo** .

    b. V části Filter **AzureAD_SSOUserRole_Policy**(filtr) vyhledejte nově vytvořenou zásadu.

    c. Vyberte **zásadu** a pak klikněte **na další: Tlačítko** revize.

19. Projděte si zásadu k připojenému uživateli provedením následujících kroků:

    ![Přidat uživatele](./media/amazon-web-service-tutorial/adduser3.png)

    a. Zkontrolujte uživatelské jméno, typ přístupu a zásadu namapované na uživatele.

    b. Kliknutím na tlačítko **vytvořit uživatele** v pravém dolním rohu vytvořte uživatele.

20. Pomocí následujících kroků stáhněte přihlašovací údaje uživatele uživatele:

    ![Přidat uživatele](./media/amazon-web-service-tutorial/adduser4.png)

    a. Zkopírujte **ID přístupového klíče** uživatele a **tajný přístupový klíč**.

    b. Pokud chcete načíst role z konzoly AWS, zadejte tyto přihlašovací údaje do oddílu zřizování uživatelů Azure AD.

    c. V dolní části klikněte na tlačítko **Zavřít** .

21. Přejděte do části **zřizování uživatelů** v aplikaci Amazon Web Services ve službě Azure AD portál pro správu.

    ![Přidat uživatele](./media/amazon-web-service-tutorial/provisioning.png)

22. Do pole **tajný klíč klienta** a tajný **token** zadejte **přístupový klíč** a **tajný** klíč.

    ![Přidat uživatele](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Do pole **ClientSecret** zadejte přístupový klíč uživatele AWS.

    b. Do pole **token tajného klíče** zadejte tajný klíč uživatele AWS.

    c. Klikněte na tlačítko **Testovat připojení** a měli byste mít možnost toto připojení úspěšně otestovat.

    d. Uložte nastavení kliknutím na tlačítko **Uložit** v horní části.

23. Teď zkontrolujte, jestli jste v části nastavení povolili stav zřizování, a pak klikněte na tlačítko **Uložit** v horní části.

    ![Přidat uživatele](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Amazon Web Services (AWS).

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Amazon Web Services (AWS)** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-amazon-web-services-aws-test-user"></a>Vytvořit testovacího uživatele Amazon Web Services (AWS)

Cílem této části je vytvořit uživatele s názvem B. Simon v Amazon Web Services (AWS). Amazon Web Services (AWS) nevyžaduje vytvoření uživatele v systému pro jednotné přihlašování, takže nemusíte nic dělat.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Amazon Web Services (AWS), měli byste se automaticky přihlásili k Amazon Web Services (AWS), pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Známé problémy

 * V oddílu **zřizování** se v dílčí části **mapování** zobrazí zpráva "načítání...". zpráva a nikdy nezobrazuje mapování atributů. Jediným podporovaným pracovním postupem zřizování je import rolí z AWS do služby Azure AD pro výběr během přiřazování uživatelů nebo skupin. Mapování atributů pro tuto vlastnost jsou předem určena a nelze je konfigurovat.
 
 * Oddíl **zřizování** podporuje jenom zadání jedné sady přihlašovacích údajů pro jednoho klienta AWS ve chvíli. Všechny importované role se zapisují do vlastnosti appRoles objektu Azure AD [servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pro tenanta AWS. Do služby Azure AD se dá přidat několik tenantů AWS (reprezentovaných servicePrincipals) z galerie pro účely zřizování, ale dojde k známému problému, který nedokáže automaticky zapisovat všechny importované role z několika AWS servicePrincipals používaných pro zřizování do jediného servicePrincipal používaného pro jednotné přihlašování Jako alternativní řešení můžete [Microsoft Graph rozhraní API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) použít k extrakci všech appRoles importovaných do každého AWS servicePrincipal, kde je zřizování nakonfigurované. Tyto řetězce role je možné následně přidat do AWS servicePrincipal, kde je nakonfigurované jednotné přihlašování.

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

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
