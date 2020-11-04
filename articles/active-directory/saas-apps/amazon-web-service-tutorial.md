---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Amazon Web Services (AWS) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Amazon Web Services (AWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: eb784d02ec4c380d72438a1c000290fe3a4eea78
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307499"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Amazon Web Services (AWS)

V tomto kurzu se naučíte, jak integrovat Amazon Web Services (AWS) s Azure Active Directory (Azure AD). Když integrujete Amazon Web Services (AWS) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Amazon Web Services (AWS).
* Umožněte uživatelům, aby se automaticky přihlásili k Amazon Web Services (AWS) s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Diagram vztahu Azure AD a AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Pro více instancí můžete nakonfigurovat více identifikátorů. Například:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Díky těmto hodnotám Azure AD odstraní hodnotu **#** a pošle správnou hodnotu `https://signin.aws.amazon.com/saml` jako adresu URL cílové skupiny v tokenu SAML.

Tento postup doporučujeme z následujících důvodů:

- Každá aplikace poskytuje jedinečný certifikát x509. Každá instance instance aplikace AWS může mít jiné datum vypršení platnosti certifikátu, které se dá spravovat na základě jednotlivých účtů AWS. V tomto případě je snazší celková změna certifikátu.

- Můžete povolit zřizování uživatelů pomocí aplikace AWS ve službě Azure AD a potom naše služba načte všechny role z tohoto účtu AWS. V aplikaci nemusíte ručně přidávat ani aktualizovat role AWS.

- Vlastník aplikace můžete přiřadit individuálně pro aplikaci. Tato osoba může spravovat aplikaci přímo ve službě Azure AD.

> [!Note]
> Ujistěte se, že používáte jenom aplikaci Galerie.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným AWSm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Amazon Web Services (AWS) podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie

Pokud chcete nakonfigurovat integraci Amazon Web Services (AWS) do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS Přidat Amazon Web Services (AWS) z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního účtu, školního účtu nebo osobního účet Microsoft.
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
1. V nabídce přehled Azure Active Directory vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Amazon Web Services (AWS)** .
1. Z panelu výsledků vyberte **Amazon Web Services (AWS)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Amazon Web Services (AWS)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Amazon Web Services (AWS).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[Amazon Web Services (AWS)](#configure-amazon-web-services-aws-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** – Chcete-li mít protějšek B. Simon v Amazon Web Services (AWS), která je propojená s reprezentací uživatele v Azure AD.
    1. **[Postup při konfiguraci zřizování rolí v Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **Amazon Web Services (AWS)** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** aktualizujte **identifikátor (ID entity)** a **adresu URL odpovědi** se stejnou výchozí hodnotou: `https://signin.aws.amazon.com/saml` . Chcete-li uložit změny konfigurace, je nutné vybrat možnost **Uložit** .

1. Pokud konfigurujete více než jednu instanci, zadejte hodnotu identifikátoru. Od druhé instance a dále použijte následující formát, včetně **#** znaménka k zadání jedinečné hodnoty hlavního názvu služby (SPN).

    `https://signin.aws.amazon.com/saml#2`

1. AWS aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace AWS několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Název  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User. userPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Role            | User. assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration             | "zadejte hodnotu v rozmezí 900 sekund (15 minut) až 43200 sekund (12 hodin)" |  `https://aws.amazon.com/SAML/Attributes` |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v dialogovém okně **podpisový certifikát SAML** (krok 3) vyberte **Přidat certifikát**.

    ![Vytvořit nový certifikát SAML](common/add-saml-certificate.png)

1. Vygenerujte nový podpisový certifikát SAML a pak vyberte **nový certifikát**. Zadejte e-mailovou adresu pro oznámení o certifikátech.
   
    ![Nový certifikát SAML](common/new-saml-certificate.png) 

1. V části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](./media/amazon-web-service-tutorial/certificate.png)

1. V části **nastavit Amazon Web Services (AWS)** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
1. V nabídce přehled Azure Active Directory vyberte **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Amazon Web Services (AWS).

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
1. V nabídce přehled Azure Active Directory vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Amazon Web Services (AWS)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-amazon-web-services-aws-sso"></a>Konfigurace jednotného přihlašování Amazon Web Services (AWS)

1. V jiném okně prohlížeče se přihlaste k webu AWS společnosti jako správce.

2. Vyberte **AWS Home (domů** ).

    ![Snímek obrazovky AWS společnosti se zvýrazněnou ikonou domovské stránky AWS][11]

3. Vyberte **Správa identit a přístupu**.

    ![Snímek stránky AWS Services se zvýrazněným nástrojem IAM][12]

4. Vyberte **Zprostředkovatelé identity**  >  **vytvořit poskytovatele**.

    ![Snímek stránky IAM se zvýrazněnou možností poskytovatelé identity a poskytovatel pro vytváření][13]

5. Na stránce **Konfigurovat poskytovatele** proveďte následující kroky:

    ![Snímek obrazovky s konfigurací zprostředkovatele][14]

    a. Jako **Typ poskytovatele** vyberte **SAML**.

    b. Jako **název poskytovatele** zadejte název zprostředkovatele (například: *WAAD* ).

    c. Pokud chcete nahrát stažený **soubor metadat** z Azure Portal, vyberte **zvolit soubor**.

    d. Vyberte **Další krok**.

6. Na stránce **ověřit informace o poskytovateli** vyberte **vytvořit**.

    ![Snímek obrazovky s ověřením informací o poskytovateli se zvýrazněným výrazem vytvořit][15]

7. Vyberte **role**  >  **vytvořit roli**.

    ![Snímek stránky rolí][16]

8. Na stránce **vytvořit roli** proveďte následující kroky:  

    ![Snímek obrazovky s vytvořením stránky role][19]

    a. V části **Vybrat typ důvěryhodné entity** vyberte možnost **federace SAML 2,0**.

    b. V části **zvolte poskytovatele saml 2,0** vyberte **poskytovatele SAML** , který jste předtím vytvořili (například: *WAAD* ).

    c. Zaškrtněte políčko **povolí přístup k programovým a AWS Management Console**.
  
    d. Vyberte **Další: Oprávnění**.

9. V dialogovém okně **připojit zásady oprávnění** Připojte příslušné zásady podle vaší organizace. Pak vyberte **Další: zkontrolovat**.  

    ![Snímek obrazovky dialogového okna připojit zásadu oprávnění][33]

10. V dialogovém okně **Kontrola** proveďte následující kroky:

    ![Snímek obrazovky s dialogovým oknem pro kontrolu][34]

    a. Do **název role** zadejte název své role.

    b. Do pole **Popis role** zadejte popis.

    c. Vyberte **vytvořit roli**.

    d. Podle potřeby vytvořte tolik rolí a namapujte je na zprostředkovatele identity.

11. Použijte přihlašovací údaje účtu služby AWS pro načtení rolí z účtu AWS ve zřizování uživatelů Azure AD. V takovém případě otevřete domovskou stránku konzoly AWS.

12. Vyberte **služby**. V části **zabezpečení, identita & dodržování předpisů** vyberte **IAM**.

    ![Snímek obrazovky domovské stránky konzoly AWS se zvýrazněnými službami a nástrojem IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. V části IAM vyberte **zásady**.

    ![Snímek obrazovky oddílu IAM se zvýrazněnými zásadami](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Vytvořte novou zásadu tak, že vyberete **vytvořit zásadu** pro načtení rolí z účtu AWS ve zřizování uživatelů Azure AD.

    ![Snímek stránky pro vytvoření role se zvýrazněnou možností vytvořit zásadu](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Vytvořte vlastní zásadu pro načtení všech rolí z účtů AWS.

    ![Snímek stránky pro vytvoření zásady se zvýrazněným kódem JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. V části **vytvořit zásadu** vyberte kartu **JSON** .

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

    a. Jako **název** zadejte **AzureAD_SSOUserRole_Policy**.

    b. Pro **Popis** zadejte **tuto zásadu, která umožní načtení rolí z účtů AWS**.

    c. Vyberte **Vytvořit zásadu**.

17. Ve službě AWS IAM vytvořte nový uživatelský účet.

    a. V konzole AWS IAM vyberte **Uživatelé**.

    ![Snímek obrazovky konzoly AWS IAM se zvýrazněnými uživateli](./media/amazon-web-service-tutorial/policy3.png)

    b. Chcete-li vytvořit nového uživatele, vyberte možnost **Přidat uživatele**.

    ![Snímek obrazovky s tlačítkem Přidat uživatele](./media/amazon-web-service-tutorial/policy4.png)

    c. V části **Přidat uživatele** :

    ![Snímek obrazovky se stránkou přidat uživatele se zvýrazněným uživatelským jménem a typem přístupu](./media/amazon-web-service-tutorial/adduser1.png)

    * Zadejte uživatelské jméno jako **AzureADRoleManager**.

    * Jako typ přístupu vyberte **programový přístup**. Tímto způsobem může uživatel vyvolat rozhraní API a načíst role z účtu AWS.

    * Vyberte **Další oprávnění**.

18. Vytvoří novou zásadu pro tohoto uživatele.

    ![Snímek obrazovky se zobrazí stránka Přidat uživatele, kde můžete vytvořit zásadu pro uživatele.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Vyberte možnost **připojit existující zásady přímo**.

    b. Vyhledejte nově vytvořenou zásadu v části filtru **AzureAD_SSOUserRole_Policy**.

    c. Vyberte zásadu a pak vyberte **Další: zkontrolovat**.

19. Zkontrolujte zásadu pro připojeného uživatele.

    ![Snímek obrazovky se stránkou přidat uživatele s zvýrazněným možností vytvořit uživatele](./media/amazon-web-service-tutorial/adduser3.png)

    a. Zkontrolujte uživatelské jméno, typ přístupu a zásadu namapované na uživatele.

    b. Vyberte **vytvořit uživatele**.

20. Stáhněte si přihlašovací údaje uživatele.

    ![Snímek obrazovky se stránkou přidat uživatele, která má tlačítko Stáhnout c s v pro získání přihlašovacích údajů uživatele.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Zkopírujte **ID přístupového klíče** uživatele a **tajný přístupový klíč**.

    b. Pokud chcete načíst role z konzoly AWS, zadejte tyto přihlašovací údaje do oddílu zřizování uživatelů Azure AD.

    c. Vyberte **Zavřít**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Postup při konfiguraci zřizování rolí v Amazon Web Services (AWS)

1. Na portálu pro správu Azure AD, v aplikaci AWS, pokračujte na **zřizování**.

    ![Snímek obrazovky aplikace AWS se zvýrazněným zřizováním](./media/amazon-web-service-tutorial/provisioning.png)

2. Zadejte přístupový klíč a tajný klíč do polí **ClientSecret** a **tajného tokenu** v uvedeném pořadí.

    ![Snímek obrazovky dialogového okna přihlašovací údaje správce](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Do pole **ClientSecret** zadejte přístupový klíč uživatele AWS.

    b. Do pole **token tajného klíče** zadejte tajný klíč uživatele AWS.

    c. Vyberte **Test připojení**.

    d. Uložte nastavení výběrem možnosti **Uložit**.

3. V části **Nastavení** pro **stav zřizování** vyberte **zapnuto**. Pak vyberte **Uložit**.

    ![Snímek obrazovky s oddílem nastavení se zvýrazněnou možností](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Služba zřizování importuje role jenom z AWS do Azure AD. Služba nezřizuje uživatele a skupiny z Azure AD do AWS.

> [!NOTE]
> Po uložení přihlašovacích údajů pro zřizování musíte počkat, až se spustí počáteční cyklus synchronizace. Synchronizace obvykle trvá přibližně 40 minut. V části **aktuální stav** můžete zobrazit stav na konci stránky **zřizování** .

### <a name="create-amazon-web-services-aws-test-user"></a>Vytvořit testovacího uživatele Amazon Web Services (AWS)

Cílem této části je vytvořit uživatele s názvem B. Simon v Amazon Web Services (AWS). Amazon Web Services (AWS) nevyžaduje vytvoření uživatele v systému pro jednotné přihlašování, takže nemusíte nic dělat.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL Amazon Web Services (AWS), kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Amazon Web Services (AWS) přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Amazon Web Services (AWS), pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici Amazon Web Services (AWS) na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Amazon Web Services (AWS), pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="known-issues"></a>Známé problémy

 * V části **zřizování** se v podčásti **mapování** zobrazuje "načítání...". zpráva a nikdy nezobrazuje mapování atributů. Jediným podporovaným pracovním postupem zřizování je import rolí z AWS do služby Azure AD pro výběr během přiřazení uživatele nebo skupiny. Mapování atributů pro tuto vlastnost jsou předem určena a nelze je konfigurovat.

 * Oddíl **zřizování** podporuje jenom zadání jedné sady přihlašovacích údajů pro jednoho klienta AWS ve chvíli. Všechny importované role se zapisují do `appRoles` vlastnosti [ `servicePrincipal` objektu](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) Azure AD pro tenanta AWS.

   `servicePrincipals`Do Azure AD se dá přidat víc TENANTŮ AWS (reprezentovaný) z galerie pro zřizování. K dispozici je ale známý problém s tím, že není možné automaticky zapisovat všechny importované role z několika AWS `servicePrincipals` používaných k zřizování do jediného `servicePrincipal` používaného pro jednotné přihlašování.

   Jako alternativní řešení můžete použít [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) k extrakci všech `appRoles` importovaných do každého AWS `servicePrincipal` , kde je zřizování nakonfigurované. Následně můžete přidat tyto řetězce rolí do AWS, `servicePrincipal` kde je nakonfigurované jednotné přihlašování.

* Role musí splňovat následující požadavky, aby měly nárok na Import z AWS do služby Azure AD:

  * Role musí mít přesně jednoho poskytovatele SAML definovaného v AWS.
  * Kombinovaná délka ARN (název prostředku Amazon) pro roli a ARN pro přidruženého poskytovatele SAML musí být kratší než 120 znaků.

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Amazon Web Services (AWS), můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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