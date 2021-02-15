---
title: 'Kurz: Azure Active Directory integrace s Amazon Web Services pro připojení více účtů | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure AD a Amazon Web Services (kurz starší verze).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384108"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Kurz: Azure Active Directory integrace s Amazon Web Services

V tomto kurzu se naučíte, jak integrovat Azure Active Directory (Azure AD) s Amazon Web Services (AWS) (kurz starší verze).

Tato integrace přináší následující výhody:

- Můžete kontrolovat v Azure AD, kteří mají přístup k AWS.
- Uživatelům můžete umožnit, aby se automaticky přihlásili k AWS pomocí jednotného přihlašování (SSO) s účty Azure AD.
- Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

![Diagram Integrace Azure AD s AWS](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Doporučujeme *, abyste nepřipojili* jednu aplikaci AWS ke všem účtům AWS. Místo toho doporučujeme použít [integraci Azure AD jednotného přihlašování s AWS](./amazon-web-service-tutorial.md) a nakonfigurovat více instancí AWS účtu na více instancí aplikací AWS ve službě Azure AD. 

Doporučujeme *, abyste nepřipojili* jednu aplikaci AWS ke všem účtům AWS, a to z následujících důvodů:

* Tento přístup používejte jenom v případě, že máte malý počet účtů AWS a rolí, protože tento model není škálovatelný jako počet účtů AWS a role, které se v nich nacházejí. Přístup nepoužívá funkci importu rolí AWS s zřizováním uživatelů Azure AD, takže je nutné ručně přidat, aktualizovat nebo odstranit role. 

* Chcete-li do aplikace opravit všechny role, je nutné použít přístup k aplikaci Microsoft Graph Explorer. Nedoporučujeme používat přístup k souboru manifestu.

* Zákazníci nahlásí, že po přidání rolí aplikace ~ 1 200 pro jednu aplikaci AWS se při každé další operaci v aplikaci spustí chybové chyby týkající se velikosti. Objekt aplikace má omezení pevné velikosti.

* Role je potřeba aktualizovat ručně, když se přidají do některého z účtů. Toto je však přístup k *nahrazení* , nikoli přístup pro *připojení* . I když se zvyšuje počet účtů, bude se jednat o relaci *n* &times; *n* s účty a rolemi.

* Všechny účty AWS používají stejný soubor XML s federačními metadaty. V době, kdy se změna certifikátu aktualizuje, může být aktualizace certifikátu na všech účtech AWS ve stejnou dobu obrovským cvičením.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s AWS potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, můžete získat měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné s povoleným AWS SSO.

> [!NOTE]
> Nedoporučujeme testovat kroky v tomto kurzu v produkčním prostředí, pokud to není nutné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

AWS podporuje jednotné přihlašování iniciované v SP a IDP.

## <a name="add-aws-from-the-gallery"></a>Přidání AWS z Galerie

Pokud chcete nakonfigurovat integraci AWS do Azure AD, přidejte AWS z Galerie do seznamu spravovaných aplikací software jako služba (SaaS).

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu Azure AD, se kterou chcete pracovat.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Amazon Web Services** .
1. V seznamu výsledků vyberte **Amazon Web Services** a pak přidejte aplikaci. Během několika sekund se aplikace přidá do vašeho tenanta.

1. V podokně **vlastnosti** klikněte na a potom zkopírujte hodnotu, která se zobrazuje v poli **ID objektu** .

    ![Snímek obrazovky s polem ID objektu v podokně Vlastnosti.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí AWS na základě testovacího uživatele s názvem "Britta Simon".

Aby bylo jednotné přihlašování fungovat, musí Azure AD zjistit, co je uživatel partnerského systému v AWS k uživateli Azure AD. Jinými slovy je potřeba zřídit vztah propojení mezi uživatelem Azure AD a stejným uživatelem v AWS.

V AWS přiřaďte hodnotu **uživatelského jména** ve službě Azure AD jako hodnotu uživatelského **jména** AWS pro vytvoření vztahu odkazu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AWS, udělejte toto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
1. **[NAKONFIGURUJTE jednotné](#configure-aws-sso)** přihlašování AWS pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal a pomocí následujícího postupu nakonfigurujete jednotné přihlašování v aplikaci AWS:

1. V Azure Portal klikněte v levém podokně na stránce integrace aplikace **Amazon Web Services (AWS)** na možnost **jednotné přihlašování**.

    ![Snímek obrazovky s příkazem jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Snímek obrazovky s podoknem výběr metody jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jeden Sign-On s** podoknem SAML vyberte tlačítko  **Upravit** (ikona tužky).

    ![Snímek obrazovky s tlačítkem Upravit v podokně nastavit jeden Sign-On s SAML](common/edit-urls.png)

1. Otevře se **základní podokno konfigurace SAML** . Tuto část přeskočte, protože aplikace je předintegrovaná s Azure. Vyberte **Uložit**.

   Aplikace AWS očekává kontrolní výrazy SAML v určitém formátu. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarací** na stránce **Integrace aplikací** . 
   
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte tlačítko **Upravit** .

    ![Snímek obrazovky s tlačítkem Upravit v podokně atributy uživatele](common/edit-attribute.png)

1. V části **deklarace identity uživatelů** v podokně **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML pomocí hodnot v následující tabulce:

    | Name  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User. userPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Role | User. assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "zadejte hodnotu od 900 sekund (15 minut) do 43200 sekund (12 hodin)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Vyberte **Přidat novou deklaraci identity** a potom v podokně **Spravovat deklarace identity uživatele** proveďte tyto kroky:

   ![Snímek obrazovky s tlačítky přidat nové deklarace identity a uložit v podokně deklarace identity uživatele](common/new-save-attribute.png)

   ![Snímek obrazovky s podoknem spravovat deklarace identity uživatele](common/new-attribute-details.png)

   b. Do pole **název** zadejte název atributu.  

   c. Do pole **obor názvů** zadejte hodnotu oboru názvů.

   d. Jako **zdroj** vyberte **atribut**.

   e. V rozevíracím seznamu **zdrojový atribut** vyberte atribut.

   f. Vyberte **OK** a pak vyberte **Uložit**.

   >[!NOTE]
   >Další informace o rolích ve službě Azure AD najdete v tématu [Přidání rolí aplikace do vaší aplikace a jejich přijetí v tokenu](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte **Stáhnout** a Stáhněte si soubor XML federačních metadat a uložte ho do svého počítače.

   ![Snímek obrazovky s odkazem na stažení federačních metadat XML v podokně podpisový certifikát SAML](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Konfigurace jednotného přihlašování AWS

1. V novém okně prohlížeče se přihlaste k webu AWS společnosti jako správce.

1. Vyberte ikonu **domovské stránky AWS** .

   ![Snímek obrazovky s ikonou "AWS Home".][11]

1. V podokně **služby AWS Services** v části **zabezpečení, identita & dodržování předpisů** vyberte **IAM (Správa přístupu identit &)**.

    ![Snímek odkazu "Správa identit a přístupu" v podokně "AWS Services".][12]

1. V levém podokně vyberte **Zprostředkovatelé identity** a pak vyberte **vytvořit poskytovatele**.

    ![Snímek obrazovky s tlačítkem vytvořit poskytovatele][13]

1. V podokně **Konfigurovat poskytovatele** udělejte toto:

    ![Snímek obrazovky s podoknem konfigurovat zprostředkovatele][14]

    a. V rozevíracím seznamu **Typ poskytovatele** vyberte **SAML**.

    b. Do pole **název zprostředkovatele** zadejte název zprostředkovatele (například. *WAAD*).

    c. Vedle pole **dokument metadat** vyberte **zvolit soubor** a odešlete stažený soubor XML federačních metadat do Azure Portal.

    d. Vyberte **Další krok**.

1. V podokně **ověřit informace o poskytovateli** vyberte **vytvořit**.

    ![Snímek obrazovky s podoknem ověřit informace o poskytovateli][15]

1. V levém podokně vyberte **role** a pak vyberte **vytvořit roli**.

    ![Snímek obrazovky s tlačítkem vytvořit roli v podokně role][16]

    > [!NOTE]
    > Kombinovaná délka názvu role Amazon Resource Name (ARN) a poskytovatele SAML ARN pro importované role musí mít 240 nebo méně znaků.

1. Na stránce **vytvořit roli** udělejte toto:  

    ![Snímek obrazovky s tlačítkem "Důvěryhodná entita" SAML 2,0 "na stránce" vytvořit roli ".][19]

    a. V části **Vybrat typ důvěryhodné entity** vyberte možnost **federace SAML 2,0**.

    b. V části **zvolte poskytovatele saml 2,0** vyberte poskytovatele SAML, kterého jste předtím vytvořili (například *WAAD*).

    c. Zaškrtněte políčko **povolí přístup k programovým a AWS Management Console**.

    d. Vyberte **Další: Oprávnění**.

1. Do vyhledávacího pole zadejte oprávnění **správce**, zaškrtněte políčko **AdministratorAccess** a potom vyberte **Další: značky**.

    ![Snímek obrazovky se seznamem název zásad se zvolenými zásadami AdministratorAccess.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. V podokně **Přidat značky (volitelné)** udělejte toto:

    ![Snímek obrazovky s podoknem přidat značky (volitelné)](./media/aws-multi-accounts-tutorial/config2.png)

    a. Do pole **klíč** zadejte název klíče (například *Azureadtest*).

    b. Do pole **hodnota (volitelné)** zadejte hodnotu klíče v následujícím formátu: `<accountname-aws-admin>` . Název účtu musí být malými písmeny.

    c. Vyberte **Další: Kontrola**.

1. V podokně **Kontrola** udělejte toto:

    ![Snímek obrazovky s podoknem revize s zvýrazněnými rámečky "název role" a "Popis role".][34]

    a. Do pole **název role** zadejte hodnotu v následujícím formátu: `<accountname-aws-admin>` .

    b. V poli **Popis role** zadejte hodnotu, kterou jste použili pro název role.

    c. Vyberte **vytvořit roli**.

    d. Vytvořte tolik rolí, kolik potřebujete, a namapujte je na zprostředkovatele identity.

    > [!NOTE]
    > Podobně můžete vytvořit další role, jako je například *account-finance-admin*, *account--Only-User*, *account-DevOps-User* nebo *account-TPM-User-User*, Each s připojenou jinou zásadou. Tyto zásady rolí můžete později změnit podle požadavků pro každý účet AWS. Je vhodné zachovat stejné zásady pro každou roli v rámci účtů AWS.

1. Nezapomeňte si poznamenat ID účtu účtu AWS buď z podokna vlastností cloudu Amazon elastického cloudu (Amazon EC2), nebo pomocí řídicího panelu IAM, jak je znázorněno na následujícím snímku obrazovky:

    ![Snímek obrazovky zobrazující, kde se ID účtu zobrazuje v podokně Správa identit a přístupu](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Přihlaste se k Azure Portal a pak přejít do **skupin**.

1. Vytvořte nové skupiny se stejným názvem, jako má role IAM, které jste vytvořili dříve, a pak si poznamenejte hodnotu v poli **ID objektu** u každé z těchto nových skupin.

    ![Snímek obrazovky s podrobnostmi o účtu pro novou skupinu](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Odhlaste se z aktuálního účtu AWS a pak se přihlaste k jinému účtu, ve kterém chcete nakonfigurovat jednotné přihlašování pomocí Azure AD.

1. Po vytvoření všech rolí v účtech se zobrazí v seznamu **rolí** pro tyto účty.

    ![Snímek obrazovky se seznamem rolí zobrazující název, popis a důvěryhodné entity jednotlivých rolí](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Dál potřebujete zachytit všechny role ARNs a důvěryhodné entity pro všechny role napříč všemi účty. Budete je muset namapovat ručně pomocí aplikace Azure AD. Postupujte následovně:

1. Vyberte jednotlivé role pro zkopírování jejích rolí ARN a hodnot důvěryhodných entit. Budete je potřebovat pro všechny role, které vytvoříte ve službě Azure AD.

    ![Snímek obrazovky podokna souhrn pro role ARNs a důvěryhodné entity](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Předchozí krok opakujte pro všechny role ve všech účtech a pak je uložte do textového souboru v následujícím formátu: `<Role ARN>,<Trusted entities>` .

1. Otevřete [aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)a proveďte následující kroky:

    a. Přihlaste se k webu Průzkumníka Microsoft Graph pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    b. K vytvoření rolí potřebujete dostatečná oprávnění. Vyberte **Upravit oprávnění**.

      ![Snímek obrazovky s odkazem změnit oprávnění v podokně ověřování v Průzkumníkovi Microsoft Graph.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Pokud ještě nemáte oprávnění, která jsou zobrazena na následujícím snímku obrazovky, vyberte v seznamu oprávnění možnost **Upravit oprávnění**. 

      ![Snímek obrazovky seznamu oprávnění Průzkumníka Microsoft Graph se zvýrazněnými příslušnými oprávněními](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Přihlaste se znovu do Průzkumníka graphu a přijměte podmínky použití webu. 

    e. V horní části podokna vyberte **získat** pro metodu, pro verzi vyberte **beta** a potom do pole dotaz zadejte jednu z následujících možností: 
    
    * K načtení všech instančních objektů z vašeho tenanta použijte `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Pokud používáte více adresářů, použijte `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` , který obsahuje vaši primární doménu.

    ![Snímek obrazovky s podoknem dotaz "text žádosti v Průzkumníkovi Microsoft Graph.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. V seznamu instančních objektů získáte tu, kterou potřebujete upravit. 
    
    Můžete také vyhledat aplikaci pro všechny uvedené objekty služby výběrem kombinace kláves CTRL + F. Pokud chcete získat konkrétní instanční objekt, zahrňte do dotazu ID objektu zabezpečení služby, které jste zkopírovali dříve v podokně vlastností Azure AD, jak je znázorněno zde:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Snímek obrazovky znázorňující dotaz objektu služby, který obsahuje ID objektu.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    například Rozbalte vlastnost appRoles z instančního objektu služby.

    ![Snímek obrazovky kódu pro extrakci vlastnosti appRoles z instančního objektu služby](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nyní musíte pro svou aplikaci vygenerovat nové role. 

    i. Následující kód JSON je příkladem objektu appRoles. Vytvořte podobný objekt pro přidání rolí, které chcete pro vaši aplikaci.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Nové role můžete přidat až po přidání *msiam_access* pro operaci patch. V závislosti na potřebách vaší organizace můžete také přidat tolik rolí, kolik chcete. Azure AD odesílá *hodnoty* těchto rolí jako hodnotu deklarace v odpovědi SAML.

    j. V Průzkumníku Microsoft Graph změňte metodu z **Get** na **patch**. Opravte objekt instančního objektu požadovanými rolemi, a to tak, že aktualizujete vlastnost appRoles, jako je ta uvedená v předchozím příkladu. Výběrem **Spustit dotaz** spusťte operaci patch. Zpráva o úspěchu potvrzuje vytvoření role pro vaši aplikaci AWS.

      ![Snímek obrazovky podokna Průzkumníka Microsoft Graph s metodou změněnou na PATCH](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Po opravě instančního objektu s více rolemi můžete uživatelům a skupinám přiřadit příslušné role. Provedete to v Azure Portal tak, že do aplikace AWS kliknete a pak vyberete kartu **Uživatelé a skupiny** v horní části.

1. Doporučujeme vytvořit novou skupinu pro každou roli AWS, abyste tuto konkrétní roli mohli přiřadit ve skupině. Toto mapování 1:1 znamená, že jedna skupina je přiřazena k jedné roli. Pak můžete přidat členy, kteří patří do této skupiny.

1. Po vytvoření skupin vyberte skupinu a přiřaďte ji k aplikaci.

    ![Snímek obrazovky s podoknem uživatelé a skupiny](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Při přiřazování skupin se vnořené skupiny nepodporují.

1. Chcete-li přiřadit roli ke skupině, vyberte roli a potom vyberte **přiřadit**.

    ![Snímek obrazovky s podoknem přidat přiřazení](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Po přiřazení rolí je můžete zobrazit tak, že aktualizujete relaci Azure Portal.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí aplikace Microsoft moje aplikace.

Když v okně moje aplikace vyberete dlaždici **AWS** , otevře se stránka aplikace AWS s možností výběru role.

![Snímek obrazovky stránky AWS pro testování jednotného přihlašování](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Můžete také ověřit odpověď SAML a zobrazit role předávané jako deklarace identity.

![Snímek obrazovky s odpovědí SAML](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Další informace o mých aplikacích najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování AWS můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. Další informace najdete v tématu [vymáhání řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
