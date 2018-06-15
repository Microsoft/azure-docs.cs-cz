---
title: 'Kurz: Azure Active Directory integrace s Amazon Web Services (AWS) připojit více účtů | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi službou Azure AD a více účtů Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: a6a7861b95f82180b72045f10db99d5bc45eed73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345652"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Kurz: Azure Active Directory integrace s více účty Amazon Web Services (AWS)

V tomto kurzu zjistěte, jak integrovat Azure Active Directory (Azure AD) s více účty Amazon Web Services (AWS).

Integrace Amazon Web Services (AWS) s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Amazon Web Services (AWS).
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k Amazon Web Services (AWS) (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí Amazon Web Services (AWS), potřebujete následující položky:

- Základní nebo prémiové předplatné služby Azure AD
- Amazon Web Services (AWS) více jednotné přihlašování povolené účty

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Amazon Web Services (AWS) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie
Při konfiguraci integrace služby Amazon Web Services (AWS) do služby Azure AD, musíte přidat do seznamu spravovaných aplikací SaaS Amazon Web Services (AWS) z galerie.

**Pokud chcete přidat Amazon Web Services (AWS) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Amazon Web Services (AWS)**, vyberte **Amazon Web Services (AWS)** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Po přidání aplikace, přejděte do **vlastnosti** stránky a zkopírujte **ID objektu**.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Amazon Web Services (AWS) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské Amazon Web Services (AWS).

V Amazon Web Services (AWS), přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Amazon Web Services (AWS).

**Pokud chcete konfigurovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. Na portálu Azure na **Amazon Web Services (AWS)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na **Amazon Web Services (AWS) domény a adresy URL** části uživatel nemusí provádět žádné kroky, protože aplikace je už předem integrováno s Azure.

    ![Amazon Web Services (AWS) domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Aplikace Software Amazon Web Services (AWS) očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování v atributu](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Musíte nakonfigurovat zřizování uživatelů ve službě Azure AD se načíst všechny role z konzoly AWS. Najdete v následujících zřizování kroků.

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování v atributu](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. V **Namespace** textovému poli, zadejte hodnotu oboru názvů, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

6. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. V okně jiný prohlížeč přihlašování k webu společnosti Amazon Web Services (AWS) jako správce.

9. Klikněte na tlačítko **AWS domovské**.
   
    ![Konfigurace domovské jednotné přihlašování][11]

10. Klikněte na tlačítko **IAM** (identita a správa přístupu). 
   
    ![Konfiguraci Identity přihlášení][12]

11. Klikněte na tlačítko **zprostředkovatelů Identity**a potom klikněte na **vytvoření zprostředkovatele**. 
   
    ![Konfigurace zprostředkovatele přihlášení][13]

12. Na **konfigurace zprostředkovatele** dialogové okno stránky, proveďte následující kroky: 
   
    ![Konfigurovat jednotné přihlašování v dialogovém okně][14]
 
    a. Jako **typ zprostředkovatele**, vyberte **SAML**.

    b. V **název zprostředkovatele** textovému poli, zadejte název zprostředkovatele (například: *službou WAAD*).

    c. Nahrát váš stažené **soubor metadat** z portálu Azure, klikněte na tlačítko **zvolit soubor**.

    d. Klikněte na tlačítko **dalším krokem**.

13. Na **ověřte informace o poskytovateli** dialogové okno stránky, klikněte na tlačítko **vytvořit**. 
    
    ![Konfigurovat jednotné přihlašování ověření][15]

14. Klikněte na tlačítko **role**a potom klikněte na **vytvořit role**. 
    
    ![Konfiguraci rolí přihlášení][16]

15. Na **vytvořit role** proveďte následující kroky:  
    
    ![Konfigurace důvěryhodnosti přihlášení][19] 

    a. Vyberte **SAML 2.0 federation** pod **vyberte typ důvěryhodné entity**.

    b. V části **vyberte oddíl SAML 2.0 zprostředkovatele**, vyberte **SAML zprostředkovatele** jste dříve vytvořili (například: *službou WAAD*)

    c. Vyberte **Povolit programová a přístup ke konzole pro správu AWS**.
  
    d. Klikněte na tlačítko **Další: oprávnění**.

16. Na **připojit zásady oprávnění** dialogové okno, klikněte na tlačítko **Další: Zkontrolujte**.  
    
    ![Nakonfigurujte zásady přihlášení][33]

17. Na **zkontrolujte** dialogové okno, proveďte následující kroky:   
    
    ![Konfigurace zkontrolujte přihlášení][34] 

    a. V **název Role** textovému poli, zadejte název Role.

    b. V **popis Role** textovému poli, zadejte popis.

    a. Klikněte na tlačítko **vytvořit roli**.

    b. Vytvořit tolik role podle potřeby a jejich namapování na zprostředkovatele Identity.

18. Odhlaste se z aktuálního účtu AWS a přihlaste se s jiný účet, ve které chcete nakonfigurovat jednotné přihlašování na s Azure AD.

19. Proveďte krok 9 do kroku 17 vytvořit víc rolí, které chcete instalační program pro tento účet. Pokud máte více než dva účty, proveďte stejný postup pro všechny účty pro vytváření rolí pro ně.

20. Po vytvoření všechny role v účtech se objeví v **role** seznamu pro tyto účty.

    ![Instalace role](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Je potřeba zaznamenat všechny Role informace a důvěryhodné entity pro všechny role mezi všechny účty, které je potřeba mapovat ručně s aplikací Azure AD. 

22. Klikněte na role zkopírovat **Role informace** a **důvěryhodné entity** hodnoty. Je nutné tyto hodnoty pro všechny role, které je potřeba vytvořit ve službě Azure AD.

    ![Instalace role](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Proveďte předchozí krok pro všechny role v všechny účty a všechny z nich ukládání ve formátu **Role informace, důvěryhodné entity** v programu Poznámkový blok. 

24. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    a. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.

    b. Musíte mít dostatečná oprávnění pro vytváření rolí. Klikněte na **oprávnění k úpravě** získat požadovaná oprávnění. 

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Vyberte následující oprávnění ze seznamu (Pokud nemáte tyto již) a klikněte na "Upravit oprávnění" 

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. To vás vyzve k znovu přihlásit a přijměte souhlasu. Po přijetí souhlasu, se protokolují do Průzkumníka graf znovu.

    e. Změna verze rozevíracího seznamu pro **beta**. Pokud chcete načíst všechny objekty služby z vašeho klienta, použijte následující dotaz:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Pokud používáte více adresářů, můžete použít následující vzor, který má primární doménu v ní `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Můžete použít následující dotaz pomocí **id objektu** který jste zkopírovali ze stránky Azure AD vlastnosti získat k příslušné instanční objekt.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Vlastnost appRoles extrahujte z hlavní objektu služby. 

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teď je potřeba generovat nové role pro vaši aplikaci. 

    i. Níže JSON je příkladem appRoles objektu. Vytvořte objekt podobné k přidání rolí, které chcete pro vaši aplikaci. 

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
    > Můžete přidat pouze nové role po **msiam_access** pro operace opravy. Také můžete přidat libovolný počet rolí tak, jak chcete podle potřeby vaší organizace. Azure AD bude odesílat **hodnotu** z těchto rolí jako hodnota deklarace identity v odpověď SAML.
    
    j. Přejděte zpět do vaší aplikace Explorer graf a změňte metodu z **získat** k **oprava**. Oprava Service Principal objekt, který chcete mít požadovaných rolí aktualizací appRoles vlastnost podobné výše uvedeném v příkladu. Klikněte na tlačítko **spustit dotaz** k provedení operace opravy. Zpráva o úspěšném provedení potvrdí vytváření role pro vaši aplikaci Amazon Web Services.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Po objektu služby je nainstalována oprava s více rolí, můžete přiřadit uživatele nebo skupiny do příslušných rolí. To lze provést tak, že přejdete na portál a přejdou do aplikace Amazon Web Services. Klikněte na **uživatelů a skupin** karty v horní části. 

26. Doporučujeme vytvořit nové skupiny pro každou roli AWS, takže můžete přiřadit tento konkrétní roli v této skupině. Všimněte si, že se jedná o mapování 1: 1 pro jednu skupinu k jedné roli. Poté můžete přidat členy, kteří patří do dané skupiny.

27. Po vytvoření skupiny, vyberte skupinu a přiřaďte k aplikaci. 

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Chcete-li přiřadit roli do skupiny, vyberte roli a klikněte na **přiřadit** tlačítko v dolní části stránky.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Upozorňujeme, že je potřeba aktualizovat relaci na portálu Azure zobrazíte nové role.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Amazon Web Services (AWS) na přístupovém panelu, měli byste obdržet stránky aplikace Amazon Web Services (AWS) s možností vyberte roli.

![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Můžete si taky ověřit odpověď SAML zobrazíte role předávány jako deklarace identity.

![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

