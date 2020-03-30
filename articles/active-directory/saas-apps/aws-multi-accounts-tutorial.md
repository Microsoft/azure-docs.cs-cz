---
title: 'Kurz: Integrace Azure Active Directory s Amazon Web Services (AWS) pro připojení více účtů | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure AD a více účty Amazon Web Services (AWS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367996"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Kurz: Integrace Azure Active Directory s více účty Amazon Web Services (AWS)

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) s více účty Amazon Web Services (AWS).

Integrace Amazon Web Services (AWS) s Azure AD vám poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Amazon Web Services (AWS).
- Uživatelům můžete povolit automatické přihlášení k Amazon Web Services (AWS) (Jednotné přihlašování) pomocí svých účtů Azure AD.
- Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, podívejte se, [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Vezměte prosím na vědomí, že připojení jedné aplikace AWS ke všem vašim účtům AWS není naším doporučeným přístupem. Místo toho doporučujeme použít [tento](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) přístup ke konfiguraci více instancí účtu AWS na více instancí aplikací AWS ve službě Azure AD.

**Vezměte prosím na vědomí, že tento přístup nedoporučujeme používat z následujících důvodů:**

* Musíte použít přístup Aplikace Microsoft Graph Explorer k opravě všech rolí do aplikace. Nedoporučujeme používat přístup souboru manifestu.

* Viděli jsme zákazníky hlásí, že po přidání ~ 1200 role aplikace pro jednu aplikaci AWS, všechny operace v aplikaci začal yihovat chyby související s velikostí. Tam je pevný limit velikosti na aplikační objekt.

* Musíte ručně aktualizovat roli jako role získat přidány do některého z účtů, což je nahradit přístup a ne připojit bohužel. Také pokud vaše účty rostou, pak se to stane n x n vztah s účty a role.

* Všechny účty AWS budou používat stejný soubor XML metadat federace a v době přechodu certifikátu musíte řídit toto masivní cvičení, abyste aktualizovali certifikát na všech účtech AWS současně

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Amazon Web Services (AWS), budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s podporou jednotného přihlášení amazonských webových služeb (AWS)

> [!NOTE]
> Chcete-li otestovat kroky v tomto kurzu, nedoporučujeme používat produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Amazon Web Services (AWS) podporuje **SP a IDP** inicioval SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z galerie

Chcete-li nakonfigurovat integraci Amazon Web Services (AWS) do Azure AD, musíte přidat Amazon Web Services (AWS) z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat amazonské webové služby (AWS) z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Amazon Web Services (AWS)**, z panelu výsledků vyberte **amazonské webové služby (AWS)** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Amazon Web Services (AWS) v seznamu výsledků](common/search-new-app.png)

5. Po přidání aplikace přejděte na stránku **Vlastnosti** a zkopírujte **ID objektu**.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS) na základě testovacího uživatele s názvem "Britta Simon".

Aby jednotné přihlašování fungovalo, Azure AD potřebuje vědět, co je protějšek uživatele amazonských webových služeb (AWS) pro uživatele ve službě Azure AD. Jinými slovy vztah propojení mezi uživatelem Azure AD a související uživatel v Amazon Web Services (AWS) musí být vytvořen.

V Amazon Web Services (AWS) přiřaďte hodnotu **uživatelského jména** ve službě Azure AD jako hodnotu **uživatelského jména** k vytvoření vztahu propojení.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace amazonských webových služeb (AWS) single sign-on](#configure-amazon-web-services-aws-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal a nakonfigurujete jednotné přihlašování v aplikaci Amazon Web Services (AWS).

**Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Amazon Web Services (AWS)** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![image](common/preintegrated.png)

5. Aplikace Amazon Web Services (AWS) očekává kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Hodnoty těchto atributů můžete spravovat v části **Atributy uživatele & deklarace identity** na stránce integrace aplikace. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na tlačítko **Upravit** otevřete dialogové okno **Atributy uživatele & deklarace identity.**

    ![image](common/edit-attribute.png)

6. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name (Název)  | Atribut zdroje  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | Název roleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | Doba trvání relace             | "zadejte hodnotu mezi 900 sekundami (15 minut) a 43200 sekundami (12 hodin)" |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Do textového pole **Obor názvů** zadejte hodnotu Obor názvů zobrazenou pro daný řádek.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurace jednotného přihlášení amazonských webových služeb (AWS)

1. V jiném okně prohlížeče se přihlaste k webu společnosti Amazon Web Services (AWS) jako správce.

2. Klikněte na **AWS Home**.

    ![Konfigurace domovské ho přihlašování][11]

3. Klepněte na **položku Správa identit a přístupu**.

    ![Konfigurace identity jednotného přihlášení][12]

4. Klepněte na **položku Zprostředkovatelé identity**a potom klepněte na příkaz **Vytvořit zprostředkovatele**.

    ![Konfigurace zprostředkovatele jednotného přihlášení][13]

5. Na stránce dialogového okna **Konfigurovat zprostředkovatele** proveďte následující kroky:

    ![Dialogové okno Konfigurovat jednotné přihlašování][14]

    a. Jako **typ zprostředkovatele**vyberte **možnost SAML**.

    b. Do textového pole **Název zprostředkovatele** zadejte název zprostředkovatele (například *WAAD*).

    c. Pokud chcete nahrát stažený **soubor metadat** z webu Azure Portal, klikněte na **Zvolit soubor**.

    d. Klepněte na tlačítko **Další krok**.

6. Na stránce Dialogové okno **Ověřit informace o zprostředkovateli** klepněte na tlačítko **Vytvořit**.

    ![Konfigurace ověření jednotného přihlášení][15]

7. Klikněte na **Role**a potom klikněte na **Vytvořit roli**.

    ![Konfigurace rolí jednotného přihlašování][16]

8. Na stránce **Vytvořit roli** proveďte následující kroky:  

    ![Konfigurace vztahu důvěryhodnosti jednotného přihlášení][19]

    a. V části Vybrat typ **důvěryhodné entity**vyberte možnost **Federace SAML 2.0** .

    b. V části **Vyberte zprostředkovatele SAML 2.0**vyberte dříve vytvořeného **poskytovatele SAML** (například *WAAD).*

    c. Vyberte **Povolit programový přístup konzoly AWS a přístup ke službě AWS .**
  
    d. Klikněte na **Další: Oprávnění**.

9. V dialogovém okně **Připojit zásady oprávnění** připojte příslušné zásady podle vaší organizace. Klikněte na **Další: Recenze**.  

    ![Konfigurace zásad jednotného přihlašování][33]

10. V dialogovém okně **Revize** proveďte následující kroky:

    ![Konfigurace kontroly jednotného přihlášení][34]

    a. Do textového pole **Název role** zadejte název role.

    b. Do textového pole **Popis role** zadejte popis.

    c. Klepněte na **tlačítko Vytvořit roli**.

    d. Vytvořte tolik rolí, kolik je potřeba, a namapujte je na zprostředkovatele identity.

11. Odhlaste se z aktuálního účtu AWS a přihlaste se pomocí jiného účtu, kde chcete nakonfigurovat jednotné přihlašování pomocí Azure AD.

12. Proveďte krok 2 až krok 10 a vytvořte více rolí, které chcete pro tento účet nastavit. Pokud máte více než dva účty, proveďte stejné kroky pro všechny účty a vytvořte pro ně role.

13. Jakmile jsou všechny role vytvořeny v účtech, zobrazí se v seznamu **rolí** pro tyto účty.

    ![Nastavení rolí](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Musíme zachytit všechny role ARN a důvěryhodné entity pro všechny role napříč všemi účty, které potřebujeme mapovat ručně pomocí aplikace Azure AD.

15. Kliknutím na role zkopírujete hodnoty **Role ARN** a **Důvěryhodné entity.** Tyto hodnoty potřebujete pro všechny role, které potřebujete vytvořit ve službě Azure AD.

    ![Nastavení rolí](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Proveďte výše uvedený krok pro všechny role ve všech účtech a uložte je všechny ve formátu **Role ARN, Důvěryhodné entity** v poznámkovém bloku.

17. Otevřete [aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    a. Přihlaste se k webu Průzkumníka grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    b. K vytvoření rolí musíte mít dostatečná oprávnění. Chcete-li získat požadovaná oprávnění, klepněte na **tlačítko Upravit oprávnění.**

    ![Dialogové okno Průzkumník a graf](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Vyberte následující oprávnění ze seznamu (pokud je ještě nemáte) a klikněte na tlačítko Změnit oprávnění. 

    ![Dialogové okno Průzkumník a graf](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Tím vás požádáte, abyste se znovu přihlásili a přijali souhlas. Po přijetí souhlasu jste znovu přihlášeni do průzkumníka grafu.

    e. Změňte rozbalovací verzi na **beta verzi**. Chcete-li načíst všechny objekty zabezpečení služeb z vašeho tenanta, použijte následující dotaz:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, můžete použít následující vzor, který má primární doménu v něm`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogové okno Průzkumník a graf](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Ze seznamu načíst instančníobjekty, získat ten, který potřebujete upravit. Můžete také použít Ctrl + F pro vyhledávání v aplikaci ze všech uvedených ServicePrincipals. Následující dotaz můžete použít pomocí **ID objektu,** který jste zkopírovali ze stránky Vlastnosti Azure AD se dostat do příslušného instančního objektu.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogové okno Průzkumník a graf](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahujte vlastnost appRoles z objektu instančního objektu.

    ![Dialogové okno Průzkumník a graf](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nyní je třeba generovat nové role pro vaši aplikaci. 

    i. Pod JSON je příklad appRoles objektu. Vytvořte podobný objekt a přidejte role, které chcete pro vaši aplikaci.

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
    > Nové role můžete přidat pouze po **msiam_access** pro operaci opravy. Můžete také přidat libovolný počet rolí podle potřeby organizace. Azure AD odešle **hodnotu** těchto rolí jako hodnotu deklarace v odpovědi SAML.

    j. Vraťte se do aplikace Microsoft Graph Explorer a změňte metodu z **GET** na **PATCH**. Oprava Objekt uměl instanční objekt mít požadované role aktualizací appRoles vlastnost podobná té, která je uvedena výše v příkladu. Chcete-li spustit operaci opravy, klepněte na tlačítko **Spustit dotaz.** Zpráva o úspěchu potvrzuje vytvoření role pro vaši aplikaci Amazon Web Services.

    ![Dialogové okno Průzkumník aplikace Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Po opravě objektu Zabezpečení služeb s více rolemi můžete přiřadit uživatelé nebo skupiny k příslušným rolím. To lze provést přechodem na portál a přechodem do aplikace Amazon Web Services. Klikněte na kartu **Uživatelé a skupiny** v horní části.

19. Doporučujeme vytvořit nové skupiny pro každou roli AWS, abyste mohli přiřadit tuto konkrétní roli v této skupině. Všimněte si, že toto je mapování jedna ku jedné pro jednu skupinu na jednu roli. Potom můžete přidat členy, kteří patří do této skupiny.

20. Po vytvoření skupiny vyberte skupinu a přiřaďte k aplikaci.

    ![Konfigurace přidání jednotného přihlášení](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Vnořené skupiny nejsou při přiřazování skupin podporovány.

21. Chcete-li přiřadit roli skupině, vyberte roli a klikněte na **tlačítko Přiřadit** v dolní části stránky.

    ![Konfigurace přidání jednotného přihlášení](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Vezměte prosím na vědomí, že je třeba aktualizovat relaci na webu Azure Portal, abyste viděli nové role.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Amazon Web Services (AWS) na přístupovém panelu, měli byste získat Amazon Web Services (AWS) aplikační stránku s možností vybrat roli.

![Konfigurace přidání jednotného přihlášení](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Můžete také ověřit odpověď SAML, abyste viděli role předávané jako deklarace identity.

![Konfigurace přidání jednotného přihlášení](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace zřizování pomocí rozhraní API aplikace Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
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
