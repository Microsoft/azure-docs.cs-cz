---
title: Konfigurace deklarace Role, které jsou vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory | Microsoft Docs
description: Naučte se konfigurovat Role deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5520781c9f687e5e32ad1d8c38922a456bb5e3ce
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Konfigurace Role deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory

Tato funkce umožňuje uživatelům umožnit přizpůsobení typ deklarace identity pro deklarace identity "role" v odpovědi tokenu přijal při autorizaci aplikace pomocí služby Azure AD.

## <a name="prerequisites"></a>Požadavky
- Předplatné služby Azure AD s nastavení adresáře
- Jednotné přihlašování povolené předplatné
- Musíte nakonfigurovat jednotné přihlašování s vaší aplikací

## <a name="when-to-use-this-feature"></a>Při použití této funkce

Pokud vaše aplikace očekává vlastní role být předán odpověď SAML, budete muset použít tuto funkci. Tato funkce umožňuje vytvořit tolik role, jako je třeba předat zpět z Azure AD vaší aplikace.

## <a name="steps-to-use-this-feature"></a>Kroky při použití této funkce

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte název vaší aplikace, vyberte aplikaci výsledek panelu a klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Aplikace v seznamu výsledků](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po přidání aplikace, přejděte do **vlastnosti** stránky a zkopírujte **ID objektu**

    ![Stránky vlastností](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    a. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.

    b. Změnit na verzi **beta** a načtení seznamu objektů služby z vašeho klienta pomocí následující dotaz:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Pokud používáte více adresářů, pak postupujte podle tohoto vzoru `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Vyhledejte **id objektu**, který jste zkopírovali ze stránky vlastností a používat následující dotaz k získání do příslušného objektu služby.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Vlastnost appRoles extrahujte z hlavní objektu služby.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Je nutné generovat nové role pro vaši aplikaci nyní. Role služby Azure AD generátor si můžete stáhnout z [zde](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Otevřete generátor Azure AD a proveďte následující kroky –

    ![Generátor Azure AD](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Zadejte **název Role**, **popis Role**, a **hodnota Role**. Klikněte na tlačítko **přidat** přidejte roli
    
    Jakmile jste přidali všechny požadované role, klikněte na tlačítko **generování**
    
    Kopírovat obsah kliknutím **kopírování obsahu**

    > [!NOTE] 
    > Ujistěte se, že máte **msiam_access** je v roli generovaného odpovídající role uživatele a id.

    g. Přejděte zpět do vaší Průzkumníka grafu. Změnit metodu z **získat** k **oprava**. Oprava objekt zabezpečení služby do mají potřeby appRoles aktualizací appRoles vlastnost zkopírovaný hodnotami. Klikněte na tlačítko **spuštění dotazu**.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Tady je příklad appRoles objektu. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. Po objektu služby je nainstalována oprava s více rolí, jsme můžete přiřadit uživatele k příslušné role. To lze provést tak, že přejdete na portál a přejdete na příslušné aplikace. Potom kliknutím na **uživatelů a skupin** karty v horní části. Tento proces se zobrazí seznam všech uživatelů nebo skupin.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Chcete-li přiřadit roli s žádným uživatelem, právě vybrat konkrétní uživatele nebo skupinu a klikněte na **přiřadit** tlačítko v dolní části stránky.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Kliknutím na tlačítko, která přináší automaticky otevíraného okna Vybrat roli z různých rolích, které jsou definovány pro příslušné instanční objekt.

    c. Vyberte požadované role a klikněte na Odeslat.

8. Po přiřazení role pro uživatele, je potřeba aktualizovat **atributy** můžete definovat vlastní mapování **role** deklarací identity.

9. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|    
    | Název role      | User.assignedrole |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Nakonfigurujte jeden atribut přihlášení](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

10. K testování aplikace v rozšíření IDP iniciované jednotné přihlašování v protokolu do přístupového panelu (https://myapps.microsoft.com) a klikněte na dlaždici vaší aplikace. V tokenu SAML byste měli vidět všechny přiřazené role pro uživatele s názvem deklarace identity, které jste zadali.

## <a name="update-existing-role"></a>Aktualizovat existující roli

1. Chcete-li aktualizovat existující roli, proveďte následující kroky –

    a. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    b. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.
    
    c. Změnit na verzi **beta** a načtení seznamu objektů služby z vašeho klienta pomocí následující dotaz:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Pokud používáte více adresářů, pak postupujte podle tohoto vzoru `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Vyhledejte **id objektu**, který jste zkopírovali ze stránky vlastností a používat následující dotaz k získání do příslušného objektu služby.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Vlastnost appRoles extrahujte z hlavní objektu služby.
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Pokud chcete aktualizovat existující roli, postupujte podle následujících kroků:

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Změnit metodu z **získat** k **oprava**.

    * Zkopírujte existující role z aplikace a vložte je do **text žádosti**.
    
    * Aktualizujte hodnotu role nahrazením **popis Role**, **hodnota Role**, a **Role displayname** podle požadavků vaší organizace.
    
    * Po dokončení aktualizace všechny požadované role, klikněte na tlačítko **spustit dotaz**.
        
## <a name="delete-existing-role"></a>Odstraňte existující roli

1. Pokud chcete odstranit existující roli, proveďte následující kroky –

    a. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    b. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.

    c. Změnit na verzi **beta** a načtení seznamu objektů služby z vašeho klienta pomocí následující dotaz:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Pokud používáte více adresářů, pak postupujte podle tohoto vzoru `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Vyhledejte **id objektu**, který jste zkopírovali ze stránky vlastností a používat následující dotaz k získání do příslušného objektu služby.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Vlastnost appRoles extrahujte z hlavní objektu služby.
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Pokud chcete odstranit existující roli, postupujte podle následujících kroků:

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Změnit metodu z **získat** k **oprava**.

    Zkopírujte existující role z aplikace a vložte je do **text žádosti**.
    
    Nastavte **hodnotu IsEnabled** hodnotu **false** pro roli, které chcete odstranit

    Klikněte na tlačítko **spuštění dotazu**.
    
    > [!NOTE] 
    > Ujistěte se, že máte **msiam_access** je v roli generovaného odpovídající role uživatele a id.
    
    g. Až to proces výše, zachovat jako metodu **oprava** a vložte obsah remianing role **text žádosti** a klikněte na tlačítko **spustit dotaz**.
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. Po spuštění dotazu se odstraní roli.
    
    > [!NOTE]
    > Roli musí být nejprve zakázána předtím, než může být odebrán. 

## <a name="next-steps"></a>Další kroky

Odkazovat [dokumentace k aplikaci ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) pro další kroky.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
