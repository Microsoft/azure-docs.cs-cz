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
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8ec87a8d78f73af48b662c5971dfe1803717f88a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704544"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Postupy: konfigurace deklarace identity role vydané v tokenu SAML pro podnikové aplikace

Pomocí Azure Active Directory (Azure AD) můžete přizpůsobit typ deklarace identity role v tokenu odpovědi, který obdržíte po autorizaci aplikace.

## <a name="prerequisites"></a>Požadované součásti

- Předplatné Azure AD s instalačním programem adresáře.
- Předplatné s povoleným jednotným přihlašováním (SSO). Jednotné přihlašování je nutné nakonfigurovat u vaší aplikace.

## <a name="when-to-use-this-feature"></a>Kdy použít tuto funkci

Pokud vaše aplikace očekává předávat vlastní role v odpovědi SAML, musíte tuto funkci použít. Můžete vytvořit tolik rolí, kolik potřebujete, aby se do vaší aplikace předaly zpátky z Azure AD.

## <a name="create-roles-for-an-application"></a>Vytvoření rolí pro aplikaci

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte ikonu **Azure Active Directory** .

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

    a. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

    b. K vytvoření rolí potřebujete dostatečná oprávnění. Pro získání oprávnění vyberte **změnit oprávnění** .

      ![Tlačítko změnit oprávnění](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Vyberte ze seznamu následující oprávnění (Pokud je ještě nemáte) a vyberte **změnit oprávnění**.

      ![Seznam oprávnění a tlačítko změnit oprávnění](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Role správce cloudové aplikace a Správce aplikací nebudou v tomto scénáři fungovat, protože pro čtení a zápis do adresáře vyžadujeme oprávnění globálního správce.

    d. Přijměte souhlas. Přihlásili jste se k systému znovu.

    e. Změňte verzi na **beta**a načtěte seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Průzkumník grafů s dotazem na načtení instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > V tuto chvíli už probíhá upgrade rozhraní API, aby se zákazníci mohli ve službě setkat s přerušením.

    f. V seznamu načtených instančních objektů získejte tu, kterou potřebujete upravit. Můžete také použít kombinaci kláves CTRL + F a vyhledat aplikaci ze všech uvedených objektů služby. Vyhledejte ID objektu, které jste zkopírovali ze stránky **vlastnosti** , a použijte následující dotaz k získání objektu služby:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    například Rozbalte vlastnost **appRoles** z instančního objektu služby.

      ![Podrobnosti vlastnosti appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Pokud používáte vlastní aplikaci (ne aplikaci Azure Marketplace), zobrazí se dvě výchozí role: uživatel a msiam_access. Pro aplikaci Marketplace je msiam_access jedinou výchozí rolí. Nemusíte dělat žádné změny ve výchozích rolích.

    h. Vygenerujte nové role pro vaši aplikaci.

      Následující JSON je příkladem objektu **appRoles** . Vytvořte podobný objekt pro přidání rolí, které chcete pro vaši aplikaci.

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
      > Po msiam_access pro operaci patch můžete přidat pouze nové role. Také můžete přidat tolik rolí podle potřeb vaší organizace. Azure AD pošle hodnotu těchto rolí jako hodnotu deklarace v odpovědi SAML. Aby se vygenerovaly hodnoty GUID pro ID nových rolí, použijte webové nástroje jako [tyto](https://www.guidgenerator.com/) .

    i. Vraťte se do Průzkumníka graphu a změňte metodu z možnosti **získat** na **opravu**. Opravte objekt instančního objektu tak, aby měl požadované role, a to tak, že aktualizuje vlastnost **appRoles** , jako je ta uvedená v předchozím příkladu. Výběrem **Spustit dotaz** spusťte operaci patch. Zpráva o úspěchu potvrzuje vytvoření role.

      ![Operace opravy se zprávou o úspěchu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po opravě instančního objektu s více rolemi můžete uživatelům přiřadit příslušné role. Uživatele můžete přiřadit tak, že přejdete na portál a procházíte do aplikace. Vyberte kartu **Uživatelé a skupiny** . Tato karta obsahuje seznam všech uživatelů a skupin, které jsou už k této aplikaci přiřazené. Na nové role můžete přidat nové uživatele. Můžete také vybrat existujícího uživatele a vybrat možnost **Upravit** a změnit tak roli.

    ![Karta Uživatelé a skupiny](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Chcete-li přiřadit roli libovolnému uživateli, vyberte novou roli a vyberte tlačítko **přiřadit** v dolní části stránky.

    ![Podokno upravit přiřazení a podokno vybrat roli](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Pokud chcete zobrazit nové role, musíte aktualizovat svou relaci v Azure Portal.

8. Aktualizujte tabulku **atributů** a definujte vlastní mapování deklarace identity role.

9. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|
    | Název role  | User. assignedroles |

    >[!NOTE]
    >Pokud je hodnota deklarace identity role null, služba Azure AD tuto hodnotu v tokenu nepošle a je to výchozí nastavení pro jednotlivé návrhy.

    a. Kliknutím na ikonu **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

      ![Snímek obrazovky, který zvýrazní ikonu pro úpravy použitou k otevření atributů uživatele & dialogovém okně deklarace identity.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. V dialogu **Spravovat deklarace identity uživatelů** přidejte atribut tokenu SAML kliknutím na **Přidat novou deklaraci identity**.

      ![Tlačítko Přidat atribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Podokno přidat atribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Do pole **název** zadejte podle potřeby název atributu. V tomto příkladu se jako název deklarace používá **název role** .

    d. Pole **obor názvů** nechejte prázdné.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Vyberte **Uložit**.

10. Chcete-li otestovat aplikaci v rámci jednotného přihlašování iniciované poskytovatelem identity, přihlaste se na [přístupový panel](https://myapps.microsoft.com) a vyberte dlaždici aplikace. V tokenu SAML byste měli vidět všechny přiřazené role pro uživatele s názvem deklarace identity, který jste si předali.

## <a name="update-an-existing-role"></a>Aktualizovat existující roli

Chcete-li aktualizovat existující roli, proveďte následující kroky:

1. Otevřete [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

3. Změňte verzi na **beta**a načtěte seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Průzkumník grafů s dotazem na načtení instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. V seznamu načtených instančních objektů získejte tu, kterou potřebujete upravit. Můžete také použít kombinaci kláves CTRL + F a vyhledat aplikaci ze všech uvedených objektů služby. Vyhledejte ID objektu, které jste zkopírovali ze stránky **vlastnosti** , a použijte následující dotaz k získání objektu služby:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Rozbalte vlastnost **appRoles** z instančního objektu služby.

    ![Podrobnosti vlastnosti appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Chcete-li aktualizovat existující roli, použijte následující postup.

    ![Text žádosti o "opravu" s popisem "Description" a "DisplayName" se zvýrazní](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Změňte metodu z **Get** na **patch**.

    b. Zkopírujte existující role a vložte je pod **Text žádosti**.

    c. Aktualizujte hodnotu role tak, že podle potřeby aktualizujete Popis role, hodnotu role nebo zobrazovaný název role.

    d. Po aktualizaci všech požadovaných rolí vyberte **Spustit dotaz**.

## <a name="delete-an-existing-role"></a>Odstraní existující roli.

Chcete-li odstranit existující roli, proveďte následující kroky:

1. Otevřete [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

2. Přihlaste se k webu Průzkumník grafů pomocí přihlašovacích údajů globálního správce nebo spolusprávce pro vašeho tenanta.

3. Změňte verzi na **beta**a načtěte seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Průzkumník grafů s dotazem na načtení seznamu instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. V seznamu načtených instančních objektů získejte tu, kterou potřebujete upravit. Můžete také použít kombinaci kláves CTRL + F a vyhledat aplikaci ze všech uvedených objektů služby. Vyhledejte ID objektu, které jste zkopírovali ze stránky **vlastnosti** , a použijte následující dotaz k získání objektu služby:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Rozbalte vlastnost **appRoles** z instančního objektu služby.

    ![Podrobnosti vlastnosti appRoles z instančního objektu služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Pokud chcete odstranit existující roli, použijte následující postup.

    ![Text žádosti o "opravu" s povoleným nastavením na false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Změňte metodu z **Get** na **patch**.

    b. Zkopírujte z aplikace stávající role a vložte je pod **Text žádosti**.

    c. U role, kterou chcete odstranit, nastavte hodnotu **povoleno** na **false** .

    d. Vyberte **Spustit dotaz**.

    > [!NOTE]
    > Ujistěte se, že máte roli msiam_access a ID odpovídá vygenerované roli.

7. Po zakázání role odstraňte tento blok role z oddílu **appRoles** . Tuto metodu ponechte jako **opravu**a vyberte **Spustit dotaz**.

8. Po spuštění dotazu se role odstraní.

    > [!NOTE]
    > Aby bylo možné roli odebrat, je nutné ji zakázat.

## <a name="next-steps"></a>Další kroky

Další postup najdete v dokumentaci k [aplikaci](../saas-apps/tutorial-list.md).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
