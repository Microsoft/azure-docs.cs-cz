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
ms.openlocfilehash: 94b451f66d286426f6dd2cc556e8c6785c3f743e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
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

5. Po přidání aplikace, přejděte do **vlastnosti** stránky a zkopírujte **ID objektu**.

    ![Stránky vlastností](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.PNG)

6. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

    a. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.

    b. Musíte mít dostatečná oprávnění pro vytváření rolí. Klikněte na **oprávnění k úpravě** získat požadovaná oprávnění. 

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Vyberte následující oprávnění ze seznamu (Pokud nemáte tyto již) a klikněte na "Upravit oprávnění" 

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. To vás vyzve k znovu přihlásit a přijměte souhlasu. Po přijetí souhlasu, se protokolují do systému znovu.

    e. Změnit na verzi **beta** a načtení seznamu objektů služby z vašeho klienta pomocí následující dotaz:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Pokud používáte více adresářů, pak postupujte podle tohoto vzoru `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Vyhledejte **id objektu**, který jste zkopírovali ze stránky vlastností a používat následující dotaz k získání do příslušného objektu služby.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Vlastnost appRoles extrahujte z hlavní objektu služby. 

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Pokud používáte vlastní aplikace (bez galerie), zobrazí dvě výchozí role - msiam_access a uživatele. V případě aplikace Galerie msiam_access je pouze výchozí role. Není nutné žádné změny ve výchozích rolí.

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
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ]
    }
    ```
    > [!Note]
    > Můžete přidat pouze nové role po **msiam_access** pro operace opravy. Také můžete přidat libovolný počet rolí tak, jak chcete podle potřeby vaší organizace. Azure AD bude odesílat **hodnotu** z těchto rolí jako hodnota deklarace identity v odpověď SAML.
    
    j. Přejděte zpět do vaší aplikace Explorer graf a změňte metodu z **získat** k **oprava**. Oprava objekt zabezpečení služby do mají požadovaných rolí aktualizací appRoles vlastnost podobné výše uvedeném v příkladu. Klikněte na tlačítko **spustit dotaz** k provedení operace opravy. Zpráva o úspěšném provedení potvrdí vytvoření role.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po objektu služby je nainstalována oprava s více rolí, můžete přiřadit uživatele do odpovídajících rolí. To lze provést tak, že přejdete na portál a přejdete na příslušné aplikace. Klikněte na **uživatelů a skupin** karty v horní části. Rutina Vypíše seznam všech uživatelů a skupin, které jsou již přiřazena k aplikaci. Můžete přidat nové uživatele na novou roli a můžete také vybrat stávajícího uživatele a klikněte na tlačítko **upravit** Chcete-li změnit roli.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Chcete-li přiřadit roli s žádným uživatelem, vyberte novou roli a klikněte na **přiřadit** tlačítko v dolní části stránky.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Upozorňujeme, že je potřeba aktualizovat relaci na portálu Azure zobrazíte nové role.

8. Po přiřazení role pro uživatele, je potřeba aktualizovat **atributy** můžete definovat vlastní mapování **role** deklarací identity.

9. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|    
    | Název role      | User.assignedrole |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Nakonfigurujte jeden atribut přihlášení](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, podle potřeby. V tomto příkladu jsme použili **název Role** jako deklarace identity názvu.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

10. K testování aplikace v rozšíření IDP iniciované jednotné přihlašování v protokolu do přístupového panelu (https://myapps.microsoft.com) a klikněte na dlaždici vaší aplikace. V tokenu SAML měli byste vidět všechny přiřazené role pro uživatele s názvem deklarace identity, které jste zadali.

## <a name="update-existing-role"></a>Aktualizovat existující roli

Chcete-li aktualizovat existující roli, proveďte následující kroky –

1. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.
    
3. Změnit na verzi **beta** a načtení seznamu objektů služby z vašeho klienta pomocí následující dotaz:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Pokud používáte více adresářů, pak postupujte podle tohoto vzoru `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Vyhledejte **id objektu**, který jste zkopírovali ze stránky vlastností a používat následující dotaz k získání do příslušného objektu služby.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Vlastnost appRoles extrahujte z hlavní objektu služby.
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Pokud chcete aktualizovat existující roli, postupujte podle následujících kroků:

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Změnit metodu z **získat** k **oprava**.

    * Zkopírujte existující role a vložte je do **text žádosti**.

    * Aktualizujte hodnotu role aktualizací **popis Role**, **hodnota Role** nebo **Role displayname** podle potřeby.

    * Po dokončení aktualizace všechny požadované role, klikněte na tlačítko **spustit dotaz**.
        
## <a name="delete-existing-role"></a>Odstraňte existující roli

Pokud chcete odstranit existující roli, proveďte následující kroky:

1. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

2. Přihlaste se k webu Explorer grafu pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho klienta.

3. Změnit na verzi **beta** a načtení seznamu objektů služby z vašeho klienta pomocí následující dotaz:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Pokud používáte více adresářů, pak postupujte podle tohoto vzoru `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Ze seznamu načtených objekty služby získáte, které budete muset upravit. Ctrl + F můžete použít také k vyhledání aplikace z uvedených ServicePrincipals. Vyhledejte **id objektu**, který jste zkopírovali ze stránky vlastností a používat následující dotaz k získání do příslušného objektu služby.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Vlastnost appRoles extrahujte z hlavní objektu služby.
    
    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Pokud chcete odstranit existující roli, postupujte podle následujících kroků:

    ![Dialogové okno Průzkumníka grafu](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Změnit metodu z **získat** k **oprava**.

    * Zkopírujte existující role z aplikace a vložte je do **text žádosti**.
        
    * Nastavte **hodnotu IsEnabled** hodnotu **false** pro roli, která chcete odstranit

    * Klikněte na tlačítko **spuštění dotazu**.
    
    > [!NOTE] 
    > Ujistěte se, že máte **msiam_access** je v roli generovaného odpovídající role uživatele a id.
    
7. Jakmile role je zakázaná, odstranit tento blok role z části appRoles, zachovat jako metodu **oprava** a klikněte na tlačítko **spustit dotaz**.
    
8. Po spuštění dotazu se odstraní roli.
    
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
