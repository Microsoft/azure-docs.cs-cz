---
title: 'Kurz: Azure Active Directory integrace s Amazon Web Services (AWS) pro připojení k několika účtům | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure AD a více účty Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: edd54352b1328c95ae2c3e466003b64eaa0fcfde
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367996"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Kurz: Azure Active Directory integrace s více účty Amazon Web Services (AWS)

V tomto kurzu se naučíte, jak integrovat Azure Active Directory (Azure AD) s více účty Amazon Web Services (AWS).

Integrace Amazon Web Services (AWS) se službou Azure AD poskytuje následující výhody:

- Můžete řídit v Azure AD, který má přístup k Amazon Web Services (AWS).
- Uživatelům můžete povolit automatické přihlášení k Amazon Web Services (AWS) (jednotné přihlašování) pomocí svých účtů Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Poznamenejte si prosím, že se jedna aplikace AWS připojuje ke všem účtům AWS a nejedná se o náš doporučený přístup. Místo toho doporučujeme pomocí [tohoto](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) přístupu nakonfigurovat více instancí AWS účtu na více instancí AWS aplikací v Azure AD.

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
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Amazon Web Services (AWS) podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie

Pokud chcete nakonfigurovat integraci Amazon Web Services (AWS) do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS Přidat Amazon Web Services (AWS) z galerie.

**Pokud chcete přidat Amazon Web Services (AWS) z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Amazon Web Services (AWS)** , vyberte **Amazon Web Services (AWS)** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Amazon Web Services (AWS) v seznamu výsledků](common/search-new-app.png)

5. Jakmile je aplikace přidána, přejdete na stránku **vlastnosti** a zkopírujte **ID objektu**.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS) na základě testovacího uživatele s názvem "Britta Simon".

Aby bylo jednotné přihlašování fungovat, musí Azure AD zjistit, co uživatel druhé strany v Amazon Web Services (AWS) je uživatel ve službě Azure AD. Jinými slovy, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Amazon Web Services (AWS).

V Amazon Web Services (AWS) přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu uživatelského **jména** , aby se mohl vytvořit vztah propojení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal a nakonfigurujete jednotné přihlašování v aplikaci Amazon Web Services (AWS).

**Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. V [Azure Portal](https://portal.azure.com/)na stránce **Amazon Web Services (AWS)** Application Integration vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    ![image](common/preintegrated.png)

5. Amazon Web Services (AWS) aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarace** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** kliknutím na tlačítko **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Název  | Zdrojový atribut  | Obor názvů |
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

    f. Klikněte na **OK** .

    g. Klikněte na možnost **Uložit**.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurace jednotného přihlašování Amazon Web Services (AWS)

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

    b. V části **zvolte poskytovatele saml 2,0**vyberte **poskytovatele SAML** , kterého jste předtím vytvořili (například: *WAAD*).

    c. Zaškrtněte políčko **povolí přístup k programovým a AWS Management Console**.
  
    d. Klikněte na **Další: oprávnění**.

9. V dialogu **připojit zásady oprávnění** připojte ke své organizaci příslušné zásady. Klikněte na **Další: zkontrolovat**.  

    ![Konfigurace zásady jednotného přihlašování][33]

10. V dialogovém okně **Kontrola** proveďte následující kroky:

    ![Konfigurace kontroly jednotného přihlašování][34]

    a. Do textového pole **název role** zadejte název své role.

    b. Do textového pole **Popis role** zadejte popis.

    c. Klikněte na **vytvořit roli**.

    d. Podle potřeby vytvořte tolik rolí a namapujte je na zprostředkovatele identity.

11. Odhlaste se z aktuálního účtu AWS a přihlaste se pomocí jiného účtu, kde chcete nakonfigurovat jednotné přihlašování pomocí Azure AD.

12. Proveďte krok 2 pro krok 10 pro vytvoření více rolí, které chcete pro tento účet nastavit. Pokud máte více než dva účty, proveďte prosím stejný postup, abyste pro všechny účty vytvořili role pro ně.

13. Jakmile se všechny role v účtech vytvoří, zobrazí se v seznamu **rolí** pro tyto účty.

    ![Nastavení rolí](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Musíme zachytit všechny ARN role a důvěryhodné entity pro všechny role napříč všemi účty, které potřebujeme namapovat ručně pomocí aplikace Azure AD.

15. Kliknutím na role zkopírujte hodnoty **role ARN** a **důvěryhodné entity** . Tyto hodnoty budete potřebovat pro všechny role, které je třeba vytvořit ve službě Azure AD.

    ![Nastavení rolí](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Proveďte výše uvedený krok pro všechny role ve všech účtech a uložte je do formátu **role ARN, důvěryhodné entity** v poznámkovém bloku.

17. Otevřete [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    a. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    b. Musíte mít dostatečná oprávnění k vytvoření rolí. Kliknutím na **Upravit oprávnění** získáte požadovaná oprávnění.

    ![Průzkumník graphu – dialogové okno](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. V seznamu vyberte následující oprávnění (Pokud je ještě nemáte) a klikněte na změnit oprávnění. 

    ![Průzkumník graphu – dialogové okno](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. To vás vyzve k opětovnému přihlášení a přijetí souhlasu. Po přijetí souhlasu se znovu přihlaste do Průzkumníka graphu.

    e. Změňte rozevírací seznam verze na **beta**. K načtení všech instančních objektů z vašeho tenanta použijte následující dotaz:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, můžete použít následující vzor, který má vaši primární doménu v něm `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Průzkumník graphu – dialogové okno](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. V seznamu načtených instančních objektů Získejte ten, který potřebujete upravit. K vyhledání aplikace ze všech uvedených ServicePrincipals můžete použít také kombinaci kláves CTRL + F. Pomocí následujícího dotazu můžete získat přístup k příslušnému instančnímu **objektu pomocí ID objektu** , který jste zkopírovali ze stránky vlastností Azure AD.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Průzkumník graphu – dialogové okno](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Rozbalte vlastnost appRoles z instančního objektu služby.

    ![Průzkumník graphu – dialogové okno](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nyní musíte pro svou aplikaci vygenerovat nové role. 

    i. Pod JSON je příkladem objektu appRoles. Vytvořte podobný objekt pro přidání rolí, které chcete pro vaši aplikaci.

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

    j. Vraťte se do Microsoft Graph Exploreru a změňte metodu z možnosti **získat** na **opravu**. Opravte objekt instančního objektu tak, aby měl požadované role, a to tak, že aktualizuje vlastnost appRoles podobnou té, kterou vidíte výše v příkladu. Kliknutím na **Spustit dotaz** spusťte operaci patch. Zpráva o úspěchu potvrzuje vytvoření role pro vaši aplikaci Amazon Web Services.

    ![Dialogové okno Průzkumníka Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Po opravě instančního objektu s více rolemi můžete přiřadit uživatele nebo skupiny k příslušným rolím. To se dá udělat tak, že přejdete na portál a přejdete na Amazon Web Services aplikaci. Klikněte na kartu **Uživatelé a skupiny** v horní části.

19. Doporučujeme vytvořit nové skupiny pro každou roli AWS, abyste tuto konkrétní roli mohli přiřadit v této skupině. Všimněte si, že se jedná o jedno mapování pro jednu skupinu na jednu roli. Pak můžete přidat členy, kteří patří do této skupiny.

20. Po vytvoření skupin vyberte skupinu a přiřaďte ji do aplikace.

    ![Nakonfigurovat přidání jednotného přihlašování](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Při přiřazování skupin se nepodporují vnořené skupiny.

21. Chcete-li přiřadit roli ke skupině, vyberte roli a klikněte na tlačítko **přiřadit** v dolní části stránky.

    ![Nakonfigurovat přidání jednotného přihlašování](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Upozorňujeme, že pokud chcete zobrazit nové role, musíte aktualizovat svou relaci v Azure Portal.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Amazon Web Services (AWS) na přístupovém panelu, měli byste získat stránku aplikace Amazon Web Services (AWS) s možností výběru role.

![Nakonfigurovat přidání jednotného přihlašování](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Můžete také ověřit odpověď SAML a zobrazit role předávané jako deklarace identity.

![Nakonfigurovat přidání jednotného přihlašování](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Jak nakonfigurovat zřizování pomocí rozhraní Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

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
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
