---
title: Konfigurace deklarace identity role pro podnikové aplikace Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Naučte se konfigurovat deklaraci identity role vydanou v tokenu SAML pro podnikové aplikace v Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 076054b7240712fec57d0908615ce7abbfb62013
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582226"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Postupy: konfigurace deklarace identity role vydané v tokenu SAML pro podnikové aplikace

Pomocí Azure Active Directory (Azure AD) můžete přizpůsobit typ deklarace identity role v tokenu odpovědi, který obdržíte po autorizaci aplikace.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure AD s instalačním programem adresáře.
- Předplatné s povoleným jednotným přihlašováním (SSO). Jednotné přihlašování je nutné nakonfigurovat u vaší aplikace.

## <a name="when-to-use-this-feature"></a>Kdy použít tuto funkci

Tuto funkci použijte, pokud vaše aplikace očekává vlastní role v odpovědi SAML vrácené službou Azure AD. Můžete vytvořit tolik rolí, kolik potřebujete.

## <a name="create-roles-for-an-application"></a>Vytvoření rolí pro aplikaci

1. V <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span></a>v levém podokně vyberte ikonu **Azure Active Directory** .

    ![Ikona Azure Active Directory][1]

2. Vyberte **podnikové aplikace**. Pak vyberte **všechny aplikace**.

    ![Podokno podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace][3]

4. Do vyhledávacího pole zadejte název vaší aplikace a pak z panelu výsledek vyberte svou aplikaci. Kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Aplikace v seznamu výsledků](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po přidání aplikace přejdete na stránku **vlastnosti** a zkopírujte ID objektu.

    ![Stránka vlastností](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otevřete [průzkumníka Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) v jiném okně a proveďte následující kroky:

    1. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    1. K vytvoření rolí potřebujete dostatečná oprávnění. Pro získání oprávnění vyberte **změnit oprávnění** .

        ![Tlačítko změnit oprávnění](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > Role správce cloudové aplikace a Správce aplikací nebudou v tomto scénáři fungovat, protože pro čtení a zápis do adresáře vyžadujeme oprávnění globálního správce.

    1. Vyberte ze seznamu následující oprávnění (Pokud je ještě nemáte) a vyberte **změnit oprávnění**.

        ![Seznam oprávnění a tlačítko změnit oprávnění](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. Přijměte souhlas. Přihlásili jste se k systému znovu.

    1. Změňte verzi na **beta** a načtěte seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

        `https://graph.microsoft.com/beta/servicePrincipals`

        Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![Průzkumník grafů s dotazem na načtení instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. V seznamu načtených instančních objektů získejte tu, kterou potřebujete upravit. Můžete také použít kombinaci kláves CTRL + F a vyhledat aplikaci ze všech uvedených objektů služby. Vyhledejte ID objektu, které jste zkopírovali ze stránky **vlastnosti** , a použijte následující dotaz k získání objektu služby:

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. Rozbalte vlastnost **appRoles** z instančního objektu služby.

        ![Podrobnosti vlastnosti appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        Pokud používáte vlastní aplikaci (ne aplikaci Azure Marketplace), zobrazí se dvě výchozí role: uživatel a msiam_access. Pro aplikaci Marketplace je msiam_access jedinou výchozí rolí. Nemusíte dělat žádné změny ve výchozích rolích.

    1. Vygenerujte nové role pro vaši aplikaci.

        Následující JSON je příkladem objektu **appRoles** . Vytvořte podobný objekt pro přidání rolí, které chcete pro vaši aplikaci.

        ```json
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

        Po msiam_access pro operaci patch můžete přidat pouze nové role. Také můžete přidat tolik rolí podle potřeb vaší organizace. Azure AD pošle hodnotu těchto rolí jako hodnotu deklarace v odpovědi SAML. Aby se vygenerovaly hodnoty GUID pro ID nových rolí, použijte webové nástroje jako [tyto](https://www.guidgenerator.com/) .

    1. Vraťte se do Průzkumníka graphu a změňte metodu z možnosti **získat** na **opravu**. Opravte objekt instančního objektu tak, aby měl požadované role, a to tak, že aktualizuje vlastnost **appRoles** , jako je ta uvedená v předchozím příkladu. Výběrem **Spustit dotaz** spusťte operaci patch. Zpráva o úspěchu potvrzuje vytvoření role.

        ![Operace opravy se zprávou o úspěchu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. Po opravě instančního objektu s více rolemi můžete uživatelům přiřadit příslušné role. Uživatele můžete přiřadit tak, že přejdete na portál a procházíte do aplikace. Vyberte kartu **Uživatelé a skupiny** . Tato karta obsahuje seznam všech uživatelů a skupin, které jsou už k této aplikaci přiřazené. Na nové role můžete přidat nové uživatele. Můžete také vybrat existujícího uživatele a vybrat možnost **Upravit** a změnit tak roli.

    ![Karta Uživatelé a skupiny](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Chcete-li přiřadit roli libovolnému uživateli, vyberte novou roli a vyberte tlačítko **přiřadit** v dolní části stránky.

    ![Podokno upravit přiřazení a podokno vybrat roli](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    Pokud chcete zobrazit nové role, aktualizujte svou relaci v Azure Portal.

1. Aktualizujte tabulku **atributů** a definujte vlastní mapování deklarace identity role.

1. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|
    | Název role  | User. assignedroles |

    Pokud je hodnota deklarace identity role null, služba Azure AD tuto hodnotu v tokenu nepošle a je to výchozí nastavení pro jednotlivé návrhy.

    1. Kliknutím na ikonu **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

        ![Snímek obrazovky, který zvýrazní ikonu pro úpravy použitou k otevření atributů uživatele & dialogovém okně deklarace identity.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. V dialogu **Spravovat deklarace identity uživatelů** přidejte atribut tokenu SAML kliknutím na **Přidat novou deklaraci identity**.

        ![Tlačítko Přidat atribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        ![Podokno přidat atribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. Do pole **název** zadejte podle potřeby název atributu. V tomto příkladu se jako název deklarace používá **název role** .

    1. Pole **obor názvů** nechejte prázdné.

    1. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    1. Vyberte **Uložit**.

10. Chcete-li otestovat aplikaci v rámci jednotného přihlašování iniciované poskytovatelem identity, přihlaste se na [přístupový panel](https://myapps.microsoft.com) a vyberte dlaždici aplikace. V tokenu SAML byste měli vidět všechny přiřazené role pro uživatele s názvem deklarace identity, který jste si předali.

## <a name="update-an-existing-role"></a>Aktualizovat existující roli

Chcete-li aktualizovat existující roli, proveďte následující kroky:

1. Otevřete [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

1. Změňte verzi na **beta** a načtěte seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Průzkumník grafů s dotazem na načtení instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. V seznamu načtených instančních objektů získejte tu, kterou potřebujete upravit. Můžete také použít kombinaci kláves CTRL + F a vyhledat aplikaci ze všech uvedených objektů služby. Vyhledejte ID objektu, které jste zkopírovali ze stránky **vlastnosti** , a použijte následující dotaz k získání objektu služby:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Rozbalte vlastnost **appRoles** z instančního objektu služby.

    ![Podrobnosti vlastnosti appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. Chcete-li aktualizovat existující roli, použijte následující postup.

    ![Text žádosti o "opravu" s popisem "Description" a "DisplayName" se zvýrazní](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. Změňte metodu z **Get** na **patch**.

    1. Zkopírujte existující role a vložte je pod **Text žádosti**.

    1. Aktualizujte hodnotu role tak, že podle potřeby aktualizujete Popis role, hodnotu role nebo zobrazovaný název role.

    1. Po aktualizaci všech požadovaných rolí vyberte **Spustit dotaz**.

## <a name="delete-an-existing-role"></a>Odstraní existující roli.

Chcete-li odstranit existující roli, proveďte následující kroky:

1. Otevřete [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

1. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

1. Změňte verzi na **beta** a načtěte seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Průzkumník grafů s dotazem na načtení seznamu instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. V seznamu načtených instančních objektů získejte tu, kterou potřebujete upravit. Můžete také použít kombinaci kláves CTRL + F a vyhledat aplikaci ze všech uvedených objektů služby. Vyhledejte ID objektu, které jste zkopírovali ze stránky **vlastnosti** , a použijte následující dotaz k získání objektu služby:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Rozbalte vlastnost **appRoles** z instančního objektu služby.

    ![Podrobnosti vlastnosti appRoles z instančního objektu služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. Pokud chcete odstranit existující roli, použijte následující postup.

    ![Text žádosti o "opravu" s povoleným nastavením na false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. Změňte metodu z **Get** na **patch**.

    1. Zkopírujte z aplikace stávající role a vložte je pod **Text žádosti**.

    1. U role, kterou chcete odstranit, nastavte hodnotu **povoleno** na **false** .

    1. Vyberte **Spustit dotaz**.

    Ujistěte se, že máte roli msiam_access a ID odpovídá vygenerované roli.

1. Po zakázání role odstraňte tento blok role z oddílu **appRoles** . Tuto metodu ponechte jako **opravu** a vyberte **Spustit dotaz**.

1. Po spuštění dotazu se role odstraní.

    Aby bylo možné roli odebrat, je nutné ji zakázat.

## <a name="next-steps"></a>Další kroky

Další postup najdete v dokumentaci k [aplikaci](../saas-apps/tutorial-list.md).

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
