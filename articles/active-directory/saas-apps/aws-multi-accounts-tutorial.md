---
title: 'Kurz: Azure Active Directory integrace s Amazon Web Services (AWS) pro připojení k několika účtům | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure AD a Amazon Web Services (AWS) (kurz starší verze).
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
ms.openlocfilehash: e6b4175f4f47c9dd378bec84da2575c079a2079f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014395"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Kurz: Azure Active Directory Integration with Amazon Web Services (AWS) (kurz starší verze)

V tomto kurzu se naučíte, jak integrovat Azure Active Directory (Azure AD) s Amazon Web Services (AWS) (kurz starší verze).

Integrace Amazon Web Services (AWS) se službou Azure AD poskytuje následující výhody:

- Můžete řídit v Azure AD, který má přístup k Amazon Web Services (AWS).
- Uživatelům můžete povolit automatické přihlášení k Amazon Web Services (AWS) (jednotné přihlašování) pomocí svých účtů Azure AD.
- Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Poznamenejte si prosím, že se jedna aplikace AWS připojuje ke všem účtům AWS a nejedná se o náš doporučený přístup. Místo toho doporučujeme pomocí [tohoto](./amazon-web-service-tutorial.md) přístupu nakonfigurovat více instancí AWS účtu na více instancí AWS aplikací v Azure AD. Tento postup byste měli použít jenom v případě, že v něm máte několik účtů AWS a rolí, takže tento model není škálovatelný, protože AWS účty a role uvnitř těchto účtů roste. Tento přístup nepoužívá funkce importu rolí AWS pomocí zřizování uživatelů Azure AD, takže musíte ručně přidat/aktualizovat nebo odstranit role. Další omezení tohoto přístupu najdete níže v podrobnostech.

**Upozorňujeme, že tento postup nedoporučujeme používat z následujících důvodů:**

* Chcete-li do aplikace opravit všechny role, je nutné použít přístup k aplikaci Microsoft Graph Explorer. Nedoporučujeme používat přístup k souboru manifestu.

* Zjistili jsme, že zákazníci, kteří po přidání rolí aplikace ~ 1200 pro jednu aplikaci AWS, nahlásí, že všechny operace spuštěné v aplikaci vyvolají chyby týkající se velikosti. Objekt aplikace má pevně stanovený limit velikosti.

* Roli musíte ručně aktualizovat, protože role se přidávají do některého z účtů, což je přístup k nahrazení a nemůžete to bohužel přidat. I když se vaše účty rozrůstá, nastanou vztah n × n s účty a rolemi.

* Všechny účty AWS budou používat stejný soubor XML s federačními metadaty a v době, kdy dojde ke změně certifikátu, je nutné tento obrovské cvičení využít k aktualizaci certifikátu na všech účtech AWS ve stejnou dobu.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Amazon Web Services (AWS) potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování Amazon Web Services (AWS)

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Amazon Web Services (AWS) podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie

Pokud chcete nakonfigurovat integraci Amazon Web Services (AWS) do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS Přidat Amazon Web Services (AWS) z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Amazon Web Services (AWS)** .
1. Z panelu výsledků vyberte **Amazon Web Services (AWS)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

1. Jakmile je aplikace přidána, přejdete na stránku **vlastnosti** a zkopírujte **ID objektu**.

    ![ID objektu](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS) na základě testovacího uživatele s názvem "Britta Simon".

Aby bylo jednotné přihlašování fungovat, musí Azure AD zjistit, co uživatel druhé strany v Amazon Web Services (AWS) je uživatel ve službě Azure AD. Jinými slovy, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Amazon Web Services (AWS).

V Amazon Web Services (AWS) přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu uživatelského **jména** , aby se mohl vytvořit vztah propojení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Amazon Web Services (AWS)](#configure-amazon-web-services-aws-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal a nakonfigurujete jednotné přihlašování v aplikaci Amazon Web Services (AWS).

**Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. V Azure Portal na stránce **Amazon Web Services (AWS)** Application Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu **tužky** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace už je předem integrovaná s Azure a klikne na **Uložit**.

5. Amazon Web Services (AWS) aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarace** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

    ![Snímek obrazovky ukazuje atributy uživatele s ovládacím prvkem pro úpravy, který se vyvolal.](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Název  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User. userPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Role | User. assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "zadejte hodnotu v rozmezí 900 sekund (15 minut) až 43200 sekund (12 hodin)" |  `https://aws.amazon.com/SAML/Attributes` |

    1. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

        ![Snímek obrazovky zobrazuje deklarace identity uživatelů pomocí Přidat novou deklaraci identity a uložit, která se vyvolala.](common/new-save-attribute.png)

        ![Snímek obrazovky ukazuje spravovat deklarace identity uživatelů, kde můžete zadat hodnoty popsané v tomto kroku.](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Do textového pole **obor názvů** zadejte hodnotu oboru názvů zobrazenou pro tento řádek.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

    >[!NOTE]
    >Další informace o rolích v Azure AD najdete [tady](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **XML federačních metadat** a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-sso"></a>Konfigurace jednotného přihlašování Amazon Web Services (AWS)

1. V jiném okně prohlížeče se přihlaste do vaší Amazon Web Services (AWS) firemní web jako správce.

1. Klikněte na **AWS Home (domů**).

    ![Nakonfigurovat jednu Sign-On domovskou stránku][11]

1. Klikněte na **Správa identit a přístupu**.

    ![Konfigurovat jednu Sign-On identitu][12]

1. Klikněte na **Zprostředkovatelé identity** a pak klikněte na **vytvořit poskytovatele**.

    ![Konfigurovat jednoho poskytovatele Sign-On][13]

1. Na stránce **Konfigurovat poskytovatele** proveďte následující kroky:

    ![Nakonfigurovat jeden Sign-On dialog][14]

    a. Jako **Typ poskytovatele** vyberte **SAML**.

    b. Do textového pole **název zprostředkovatele** zadejte název zprostředkovatele (například: *WAAD*).

    c. Pokud chcete nahrát stažený **soubor metadat** z Azure Portal klikněte na **zvolit soubor**.

    d. Klikněte na **Další krok**.

1. Na stránce **ověřit informace o poskytovateli** klikněte na **vytvořit**.

    ![Konfigurace jednoho Sign-On ověření][15]

1. Klikněte na **role** a pak klikněte na **vytvořit roli**.

    ![Konfigurace rolí s jednou Sign-On][16]

    > [!NOTE]
    > Kombinovaná délka ARN role a poskytovatele SAML ARN pro importované role musí mít 240 znaků nebo méně.

1. Na stránce **vytvořit roli** proveďte následující kroky:  

    ![Nakonfigurovat jeden Sign-On Trust][19]

    a. V části **Vybrat typ důvěryhodné entity** vyberte možnost **federace SAML 2,0** .

    b. V části **zvolte poskytovatele saml 2,0** vyberte **poskytovatele SAML** , kterého jste předtím vytvořili (například: *WAAD*).

    c. Zaškrtněte políčko **povolí přístup k programovým a AWS Management Console**.

    d. Klikněte na **Další: oprávnění**.

1. Na panelu hledání vyhledejte **přístup správce** a zaškrtněte políčko **AdministratorAccess** a potom klikněte na **Další: značky**.

    ![Snímek obrazovky zobrazuje AdministratorAccess vybrané jako název zásady.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. V části **Přidat značky (volitelné)** proveďte následující kroky:

    ![Přidání značek](./media/aws-multi-accounts-tutorial/config2.png)

    a. Do textového pole **klíč** zadejte název klíče pro: Azureadtest.

    b. Do textového pole **hodnota (volitelné)** zadejte hodnotu klíče v následujícím formátu `accountname-aws-admin` . Název účtu by měl být malými písmeny.

    c. Klikněte na **Další: zkontrolovat**.

1. V dialogovém okně **Kontrola** proveďte následující kroky:

    ![Konfigurovat jednu kontrolu Sign-On][34]

    a. Do textového pole **název role** zadejte hodnotu v následujícím vzoru `accountname-aws-admin` .

    b. Do textového pole **Popis role** zadejte stejnou hodnotu, kterou jste použili pro název role.

    c. Klikněte na **vytvořit roli**.

    d. Podle potřeby vytvořte tolik rolí a namapujte je na zprostředkovatele identity.

    > [!NOTE]
    > Podobně Vytvořte zbývající jiné role, jako je například Account-finance-admin, Account--Only-User, Account-DevOps-User, Account-TPM-User-User s jinými zásadami, které se mají připojit. Později taky tyto zásady rolí můžete změnit podle požadavků na AWS účet, ale vždycky je lépe udržovat stejné zásady pro každou roli v rámci účtů AWS.

1. Poznamenejte si ID účtu pro tento účet AWS buď z vlastností EC2, nebo z řídicího panelu IAM, jak je zvýrazněno níže:

    ![Snímek obrazovky ukazuje, kde se účet I D zobrazuje v okně A W S.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Teď se přihlaste Azure Portal a přejděte do **skupin**.

1. Vytvořte nové skupiny se stejným názvem, jako má dříve vytvořené role IAM, a poznamenejte si **ID objektů** těchto nových skupin.

    ![Vyberte správce Access1](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Odhlaste se z aktuálního účtu AWS a přihlaste se pomocí jiného účtu, kde chcete nakonfigurovat jednotné přihlašování pomocí Azure AD.

1. Jakmile se všechny role v účtech vytvoří, zobrazí se v seznamu **rolí** pro tyto účty.

    ![Snímek obrazovky zobrazuje seznam rolí s názvem role, popisem a důvěryhodnými entitami.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Musíme zachytit všechny ARN role a důvěryhodné entity pro všechny role napříč všemi účty, které potřebujeme namapovat ručně pomocí aplikace Azure AD.

1. Kliknutím na role zkopírujte hodnoty **role ARN** a **důvěryhodné entity** . Tyto hodnoty budete potřebovat pro všechny role, které je třeba vytvořit ve službě Azure AD.

    ![Role setup2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Proveďte výše uvedený krok pro všechny role ve všech účtech a uložte je do formátu **role ARN, důvěryhodné entity** v poznámkovém bloku.

1. Otevřete [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    1. Přihlaste se k webu Průzkumníka Microsoft Graph pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    1. Musíte mít dostatečná oprávnění k vytvoření rolí. Kliknutím na **Upravit oprávnění** získáte požadovaná oprávnění.

        ![Box1 dialogového okna Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    1. V seznamu vyberte následující oprávnění (Pokud je ještě nemáte) a klikněte na změnit oprávnění. 

        ![Box2 dialogového okna Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    1. To vás vyzve k opětovnému přihlášení a přijetí souhlasu. Po přijetí souhlasu se znovu přihlaste k Průzkumníku Microsoft Graph.

    1. Změňte rozevírací seznam verze na **beta**. K načtení všech instančních objektů z vašeho tenanta použijte následující dotaz: `https://graph.microsoft.com/beta/servicePrincipals` . Pokud používáte více adresářů, můžete použít následující vzor, který má v něm svou primární doménu: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` .

        ![Box3 dialogového okna Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    1. V seznamu načtených instančních objektů Získejte ten, který potřebujete upravit. K vyhledání aplikace ze všech uvedených ServicePrincipals můžete použít také kombinaci kláves CTRL + F. Můžete použít následující dotaz pomocí **ID objektu instančního objektu** , který jste zkopírovali ze stránky vlastností Azure AD, abyste se dostali k příslušnému instančnímu objektu.

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

        ![Box4 dialogového okna Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    1. Rozbalte vlastnost appRoles z instančního objektu služby.

        ![Box5 dialogového okna Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    1. Nyní musíte pro svou aplikaci vygenerovat nové role. 

    1. Pod JSON je příkladem objektu appRoles. Vytvořte podobný objekt pro přidání rolí, které chcete pro vaši aplikaci.

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
    > Nové role můžete přidat až po **msiam_access** pro operaci patch. V rámci vaší organizace taky můžete přidat tolik rolí, kolik potřebujete. Azure AD pošle **hodnotu** těchto rolí jako hodnotu deklarace v odpovědi SAML.

    1. Vraťte se do aplikace Microsoft Graph Explorer a změňte metodu z možnosti **získat** na **opravu**. Opravte objekt instančního objektu tak, aby měl požadované role, a to tak, že aktualizuje vlastnost appRoles podobnou té, kterou vidíte výše v příkladu. Kliknutím na **Spustit dotaz** spusťte operaci patch. Zpráva o úspěchu potvrzuje vytvoření role pro vaši aplikaci Amazon Web Services.

        ![Box6 dialogového okna Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Po opravě instančního objektu s více rolemi můžete přiřadit uživatele nebo skupiny k příslušným rolím. To se dá udělat tak, že přejdete na portál a přejdete na Amazon Web Services aplikaci. Klikněte na kartu **Uživatelé a skupiny** v horní části.

1. Doporučujeme vytvořit nové skupiny pro každou roli AWS, abyste tuto konkrétní roli mohli přiřadit v této skupině. Všimněte si, že se jedná o jedno mapování pro jednu skupinu na jednu roli. Pak můžete přidat členy, kteří patří do této skupiny.

1. Po vytvoření skupin vyberte skupinu a přiřaďte ji do aplikace.

    ![Konfigurace jednoho Sign-On ADD1](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Při přiřazování skupin se nepodporují vnořené skupiny.

1. Chcete-li přiřadit roli ke skupině, vyberte roli a klikněte na tlačítko **přiřadit** v dolní části stránky.

    ![Konfigurace jednoho Sign-On Add2](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Upozorňujeme, že pokud chcete zobrazit nové role, musíte aktualizovat svou relaci v Azure Portal.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí mých aplikací.

Po kliknutí na dlaždici Amazon Web Services (AWS) v okně moje aplikace byste měli zobrazit stránku aplikace Amazon Web Services (AWS) s možností výběru role.

![Test jednotného přihlašování – ON1](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Můžete také ověřit odpověď SAML a zobrazit role předávané jako deklarace identity.

![Test jednotného přihlašování – On2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Amazon Web Services (AWS), můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

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