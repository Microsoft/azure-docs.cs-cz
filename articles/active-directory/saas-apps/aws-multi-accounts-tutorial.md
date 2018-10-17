---
title: 'Kurz: Integrace Azure Active Directory pomocí Amazon Web Services (AWS) připojit více účtů | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure AD a několika účty Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: a9acb9539497c85f408ce7417fa5983072ea80b9
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365658"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Kurz: Integrace Azure Active Directory s více účty Amazon Web Services (AWS)

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) s více účty Amazon Web Services (AWS).

Integrace služby Amazon Web Services (AWS) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Amazon Web Services (AWS).
- Uživatele, aby automaticky získat přihlášeného k Amazon Web Services (AWS) (jednotné přihlašování) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Upozorňujeme, že připojení jednu aplikaci AWS ke všem účtům AWS není náš doporučený postup. Místo toho doporučujeme, abyste použili [to](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) přístup ke konfiguraci víc instancí AWS účtu na více instancí AWS aplikací ve službě Azure AD.

**Mějte prosím na paměti, že nedoporučujeme tuto metodu použijte, z následujících důvodů:**

* Budete muset použít přístup Graph Exploreru o opravu všech rolí na aplikaci. Nedoporučujeme používat soubor manifestu přístup.

* Jsme viděli, že zákazníci vytváření sestav, že po přidání role aplikace ~ 1200 pro jednu aplikaci AWS, všechny operace v rámci aplikace spuštěné chyby související s velikostí. Existuje pevný limit velikosti objektu aplikace.

* Budete muset ručně aktualizovat roli, protože role nechejte se přidat v některém z účtů, což je bohužel nahradit přístup a ne připojení. Pokud vaše účty jsou stále se rozšiřující pak toto stane n x n vztah s účtů a rolí.

* Všechny účty AWS budete používat stejný soubor XML metadat federace a současně s certifikáty vyměnit, budete muset jednotka v tomto cvičení masivní aktualizovat certifikát na všechny účty AWS ve stejnou dobu

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD, Amazon Web Services (AWS), potřebujete následující položky:

- Základní nebo prémiové předplatné Azure AD
- Amazon Web Services (AWS) více jednotného přihlašování povolené účty

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Amazon Web Services (AWS) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie
Ke konfiguraci integrace služby Amazon Web Services (AWS) do služby Azure AD, budete muset přidat Amazon Web Services (AWS) do seznamu spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Amazon Web Services (AWS) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/aws-multi-accounts-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/aws-multi-accounts-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/aws-multi-accounts-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Amazon Web Services (AWS)** vyberte **Amazon Web Services (AWS)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Po přidání aplikace, přejděte do **vlastnosti** stránky a zkopírujte **ID objektu**.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Amazon Web Services (AWS) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Amazon Web Services (AWS).

V Amazon Web Services (AWS), přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Amazon Web Services (AWS).

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Amazon Web Services (AWS)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/aws-multi-accounts-tutorial/B1_B2_Select_SSO.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/aws-multi-accounts-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/aws-multi-accounts-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Amazon Web Services (AWS) aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![image](./media/aws-multi-accounts-tutorial/i4-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele a deklarace identity** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "zadejte hodnotu mezi 900 sekundách (15 minutách) na 43200 sekund (12 hodin)" |  https://aws.amazon.com/SAML/Attributes |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/aws-multi-accounts-tutorial/i2-attribute.png)

    ![image](./media/aws-multi-accounts-tutorial/i3-attribute.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Zadejte **Namespace** hodnotu.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  a uložte ho do počítače.

    ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

8. V jiném okně prohlížeče přihlašování k webu společnosti Amazon Web Services (AWS) jako správce.

9. Klikněte na tlačítko **AWS domovské**.

    ![Konfigurovat Single Sign-On Domovská stránka][11]

10. Klikněte na tlačítko **správu identit a přístupu**.

    ![Konfigurace Identity jednotné přihlašování][12]

11. Klikněte na tlačítko **zprostředkovatelé Identity**a potom klikněte na tlačítko **vytvořit poskytovatele**.

    ![Konfigurace zprostředkovatele přihlášení][13]

12. Na **konfigurovat zprostředkovatele** dialogového okna stránky, proveďte následující kroky:

    ![Konfigurovat Single Sign-On dialogového okna][14]

    a. Jako **typ zprostředkovatele**vyberte **SAML**.

    b. V **název zprostředkovatele** textového pole zadejte název zprostředkovatele (například: *WAAD*).

    c. K odeslání vaší stažené **soubor metadat** z webu Azure portal, klikněte na tlačítko **zvolit soubor**.

    d. Klikněte na tlačítko **další krok**.

13. Na **ověřte informace o poskytovateli** dialogového okna stránky, klikněte na tlačítko **vytvořit**.

    ![Konfigurovat Single Sign-On ověření][15]

14. Klikněte na tlačítko **role**a potom klikněte na tlačítko **vytvořit roli**.

    ![Konfiguraci rolí jednotné přihlašování][16]

15. Na **vytvořit roli** stránce, proveďte následující kroky:  

    ![Nakonfigurovat jednotné přihlašování vztah důvěryhodnosti][19]

    a. Vyberte **SAML 2.0 federace** pod **vyberte typ entity pro důvěryhodného**.

    b. V části **zvolte oddíl SAML 2.0 poskytovatele**, vyberte **SAML zprostředkovatele** jste vytvořili dříve (Příklad: *WAAD*)

    c. Vyberte **povolit programové a přístup ke konzole správy AWS**.
  
    d. Klikněte na tlačítko **Další: oprávnění**.

16. Na **připojit zásady oprávnění** dialogového okna, nemusíte připojit žádné zásady. Klikněte na tlačítko **Další: Revize**.  

    ![Nakonfigurujte zásady přihlašování][33]

17. Na **revize** dialogového okna, proveďte následující kroky:

    ![Nakonfigurovat jednotné přihlašování revize][34]

    a. V **název Role** textového pole zadejte název Role.

    b. V **popis Role** textového pole zadejte popis.

    c. Klikněte na tlačítko **vytvořit roli**.

    d. Vytvořte tolik role podle potřeby a jejich namapování na zprostředkovatele Identity.

18. Odhlaste se z aktuálního účtu AWS a přihlaste se pomocí jiného účtu, ve které chcete nakonfigurovat jednotné přihlašování v Azure AD.

19. Proveďte krok 9 do kroku 17. Chcete-li vytvořit víc rolí, které chcete nastavení pro tento účet. Pokud máte více než dva účty, proveďte stejné kroky pro všechny účty pro ně vytvořit role.

20. Po vytvoření všechny role v účtech, zobrazí se v **role** seznamu pro tyto účty.

    ![Nastavení role](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Je potřeba zaznamenat všechny Role ARN a důvěryhodné entity pro všechny role pro všechny účty, které potřebujeme pro ruční mapování s aplikací Azure AD. 

22. Klikněte na role, které chcete kopírovat **Role ARN** a **důvěryhodné entity** hodnoty. Tyto hodnoty budete potřebovat pro všechny role, které je potřeba vytvořit ve službě Azure AD.

    ![Nastavení role](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

23. Provedení předchozí krok u všech rolí ve všech účtech a všechny z nich ukládat ve formátu **ARN Role, důvěryhodné entity** do poznámkového bloku.

24. Otevřít [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    a. Přihlaste se k webu Graph Exploreru pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    b. Musíte mít dostatečná oprávnění k vytvoření rolí. Klikněte na **změnit oprávnění** a získejte požadovaná oprávnění.

    ![Dialogové okno Průzkumníka grafu](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Vyberte následující oprávnění ze seznamu (pokud tyto ještě nemáte) a klikněte na tlačítko "Upravit oprávnění" 

    ![Dialogové okno Průzkumníka grafu](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Zeptá se znovu přihlaste a přijmout souhlasu. Po přijetí svůj souhlas, jste přihlášeni ke Graph Exploreru znovu.

    e. Změnit verzi rozevírací nabídku a **beta**. Pokud chcete načíst všechny instanční objekty z vašeho tenanta, použijte následující dotaz:

     `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, pak můžete použít následující vzor, který má primární doménou, v ní `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogové okno Průzkumníka grafu](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Ze seznamu načíst instanční objekty získejte ta, kterou budete muset upravit. Ctrl + F můžete také použít k vyhledání aplikace z uvedených ServicePrincipals. Můžete použít následující dotaz pomocí **id objektu** zkopírovanou z Azure AD vlastností zobrazíte k příslušné Instančnímu objektu.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogové okno Průzkumníka grafu](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahuje vlastnost appRoles z instanční objekt.

    ![Dialogové okno Průzkumníka grafu](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teď je potřeba vygenerovat nové role pro vaši aplikaci. 

    i. Pod JSON je příkladem objektu appRoles. Vytvořte objekt podobné pro přidání rolí, které chcete pro vaši aplikaci.

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
    > Můžete přidat jenom nové role po **msiam_access** pro operaci patch. Také můžete přidat libovolný počet rolí chcete podle potřeby vaší organizace. Azure AD pošle **hodnota** z těchto rolí jako hodnota deklarace identity v odpověď SAML.

    j. Přejděte zpět na Graph Exploreru a změňte metodu z **získat** k **oprava**. Oprava instančního objektu, který chcete mít požadované role aktualizací appRoles vlastnost podobně jako v příkladu nahoře. Klikněte na tlačítko **spustit dotaz** k provedení operace opravy. Zpráva o úspěšném provedení potvrdí vytvoření role pro aplikaci služby Amazon Web Services.

    ![Dialogové okno Průzkumníka grafu](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Po instanční objekt služby je opravit s více rolemi, můžete přiřadit příslušné role uživatele nebo skupiny. To můžete udělat tak, že přejdete na portál a přejděte do aplikace Amazon Web Services. Klikněte na **uživatelů a skupin** kartě v horní části. 

26. Doporučujeme vytvářet nové skupiny pro každou roli AWS tak, aby přiřadíte tuto konkrétní roli v této skupině. Všimněte si, že se jedná o mapování 1: 1 pro jednu skupinu do jedné role. Poté můžete přidat členy, kteří patří do této skupiny.

27. Po vytvoření skupiny, vyberte skupinu a přiřaďte k aplikaci.

    ![Konfigurovat Single Sign-On přidat](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

> [!Note]
> Vnořené skupiny nejsou podporovány při přiřazování skupin.

28. Přiřazení role do skupiny, vyberte roli a klikněte na **přiřadit** tlačítko v dolní části stránky.

    ![Konfigurovat Single Sign-On přidat](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Mějte prosím na paměti, že budete muset aktualizovat relaci na webu Azure portal, pokud chcete zobrazit nové role.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Amazon Web Services (AWS), na přístupovém panelu, měli byste obdržet Amazon Web Services (AWS) stránka aplikace se možnost vybrat roli.

![Konfigurovat Single Sign-On přidat](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Můžete si taky ověřit odpověď SAML zobrazíte role, jenž je předán jako deklarace identity.

![Konfigurovat Single Sign-On přidat](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
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