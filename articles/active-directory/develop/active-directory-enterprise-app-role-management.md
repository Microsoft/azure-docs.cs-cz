---
title: Konfigurace deklarace role pro podnikové aplikace Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak nakonfigurovat deklaraci role vydanou v tokenu SAML pro podnikové aplikace ve službě Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8db27819b7eef6cdf05ea3f6645ae930ebc4ef58
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884745"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Postup: Konfigurace deklarace role vydané v tokenu SAML pro podnikové aplikace

Pomocí Služby Azure Active Directory (Azure AD) můžete přizpůsobit typ deklarace deklarace deklarace deklarace v tokenu odpovědi, který obdržíte po autorizaci aplikace.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure AD s nastavením adresáře.
- Předplatné, které má povoleno jednotné přihlašování (SSO). Je nutné nakonfigurovat přihlašovat pomocí aplikace.

## <a name="when-to-use-this-feature"></a>Kdy tuto funkci použít

Pokud vaše aplikace očekává, že vlastní role, které mají být předány v odpovědi SAML, je třeba použít tuto funkci. Můžete vytvořit tolik rolí, kolik potřebujete předat zpět z Azure AD do vaší aplikace.

## <a name="create-roles-for-an-application"></a>Vytvoření rolí pro aplikaci

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte ikonu **Služby Azure Active Directory.**

    ![Ikona služby Azure Active Directory][1]

2. Vyberte **podnikové aplikace**. Pak vyberte **všechny aplikace**.

    ![Podokno podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko "Nová aplikace"][3]

4. Do vyhledávacího pole zadejte název aplikace a vyberte aplikaci z panelu výsledků. Chcete-li přidat aplikaci, vyberte tlačítko **Přidat.**

    ![Aplikace v seznamu výsledků](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po přidání aplikace přejděte na stránku **Vlastnosti** a zkopírujte ID objektu.

    ![Stránka Vlastnosti](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otevřete [Aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně a postupujte takto:

    a. Přihlaste se k webu Průzkumníka grafů pomocí přihlašovacích údajů globálního správce nebo coadminu pro vašeho tenanta.

    b. K vytvoření rolí potřebujete dostatečná oprávnění. Chcete-li získat oprávnění, vyberte **možnost Upravit oprávnění.**

      ![Tlačítko "upravit oprávnění"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Ze seznamu vyberte následující oprávnění (pokud je ještě nemáte) a vyberte **Změnit oprávnění**.

      ![Seznam oprávnění a tlačítko "Změnit oprávnění"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Role Správce cloudových aplikací a Správce aplikací nebude v tomto scénáři fungovat, protože potřebujeme oprávnění globálního správce pro čtení a zápis adresáře.

    d. Přijměte souhlas. Jste znovu přihlášeni k systému.

    e. Změňte verzi na **beta**verzi a načíst seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Pokud používáte více adresářů, postupujte podle tohoto vzoru:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Dialogové okno Průzkumníka grafů s dotazem na načítání instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Jsme již v procesu upgradu api, takže zákazníci mohou vidět některé narušení služby.

    f. Ze seznamu načtených instančních objektů získáte ten, který je třeba upravit. Můžete také použít Ctrl + F pro vyhledávání v aplikaci ze všech uvedených instančních objektů. Vyhledejte ID objektu, který jste zkopírovali ze stránky **Vlastnosti,** a pomocí následujícího dotazu se přestaňte k instančnímu objektu:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahujte vlastnost **appRoles** z objektu instančního objektu.

      ![Podrobnosti o vlastnosti appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Pokud používáte vlastní aplikaci (ne aplikaci Azure Marketplace), uvidíte dvě výchozí role: uživatel a msiam_access. Pro aplikaci Marketplace je msiam_access jedinou výchozí rolí. Ve výchozích rolích není nutné provádět žádné změny.

    h. Vygenerujte nové role pro vaši aplikaci.

      Následující JSON je příkladem objektu **appRoles.** Vytvořte podobný objekt a přidejte role, které chcete pro vaši aplikaci.

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
      > Nové role můžete přidat pouze po msiam_access pro operaci opravy. Můžete také přidat tolik rolí, kolik vaše organizace potřebuje. Azure AD odešle hodnotu těchto rolí jako hodnotu deklarace v odpovědi SAML. Chcete-li generovat hodnoty GUID pro ID nových rolí, použijte webové nástroje, jako [je tento](https://www.guidgenerator.com/)

    i. Vraťte se do průzkumníka grafů a změňte metodu z **GET** na **PATCH**. Oprava objektu instančního objektu mít požadované role aktualizací **appRoles** vlastnost, jako je uveden v předchozím příkladu. Chcete-li spustit operaci opravy, vyberte **spustit dotaz.** Zpráva o úspěchu potvrzuje vytvoření role.

      ![Operace opravy se zprávou o úspěchu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po opravě objektu zabezpečení služby s více rolemi můžete přiřadit uživatele k příslušným rolím. Uživatele můžete přiřadit tak, že přejdete na portál a projdete v aplikaci. Vyberte kartu **Uživatelé a skupiny.** Na této kartě jsou uvedeni všichni uživatelé a skupiny, které jsou již k aplikaci přiřazeny. Do nových rolí můžete přidat nové uživatele. Můžete také vybrat existujícího uživatele a změnit roli výběrem **možnosti Upravit.**

    ![Karta Uživatelé a skupiny](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Chcete-li roli přiřadit libovolnému uživateli, vyberte novou roli a v dolní části stránky vyberte tlačítko **Přiřadit.**

    ![Podokno "Upravit přiřazení" a podokno Vybrat roli](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Chcete-li zobrazit nové role, musíte aktualizovat relaci na webu Azure Portal.

8. Aktualizujte tabulku **Atributy** a definujte vlastní mapování deklarace role.

9. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** přidejte atribut tokenu SAML následujícím postupem, jak je znázorněno v následující tabulce:

    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|
    | Název role  | user.assignedroles |

    >[!NOTE]
    >Pokud je hodnota deklarace role null, pak Azure AD nebude odesílat tuto hodnotu v tokenu a to je výchozí podle návrhu.

    a. Kliknutím **Edit** na Upravit ikonu otevřete dialogové **okno Atributy uživatele & deklarace identity.**

      ![Tlačítko Přidat atribut](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. V dialogovém okně **Spravovat deklarace identity uživatelů** přidejte atribut tokenu SAML kliknutím na **Přidat novou deklaraci identity**.

      ![Tlačítko Přidat atribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Podokno Přidat atribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Do pole **Název** zadejte podle potřeby název atributu. Tento příklad používá **název role** jako název deklarace.

    d. Pole **Obor názvů** ponechejte prázdné.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Vyberte **Uložit**.

10. Chcete-li aplikaci otestovat v jediném přihlášení, které inicioval poskytovatel identity, přihlaste se na [přístupový panel](https://myapps.microsoft.com) a vyberte dlaždici aplikace. V tokenu SAML byste měli vidět všechny přiřazené role pro uživatele s názvem deklarace, který jste zadali.

## <a name="update-an-existing-role"></a>Aktualizace existující role

Chcete-li aktualizovat existující roli, proveďte následující kroky:

1. Sem [otevřete aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Přihlaste se k webu Průzkumníka grafů pomocí přihlašovacích údajů globálního správce nebo coadminu pro vašeho tenanta.

3. Změňte verzi na **beta**verzi a načíst seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogové okno Průzkumníka grafů s dotazem na načítání instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Ze seznamu načtených instančních objektů získáte ten, který je třeba upravit. Můžete také použít Ctrl + F pro vyhledávání v aplikaci ze všech uvedených instančních objektů. Vyhledejte ID objektu, který jste zkopírovali ze stránky **Vlastnosti,** a pomocí následujícího dotazu se přestaňte k instančnímu objektu:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahujte vlastnost **appRoles** z objektu instančního objektu.

    ![Podrobnosti o vlastnosti appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Chcete-li aktualizovat existující roli, použijte následující kroky.

    ![Tělo požadavku na "PATCH", se zvýrazněným popisem a "zobrazovaným názvem"](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Změňte metodu z **GET** na **PATCH**.

    b. Zkopírujte existující role a vložte je do části **Tělo požadavku**.

    c. Podle potřeby aktualizujte hodnotu role aktualizací popisu role, hodnoty role nebo zobrazovaného názvu role.

    d. Po aktualizaci všech požadovaných rolí vyberte **spustit dotaz**.

## <a name="delete-an-existing-role"></a>Odstranění existující role

Chcete-li odstranit existující roli, proveďte následující kroky:

1. Otevřete [aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

2. Přihlaste se k webu Průzkumníka grafů pomocí přihlašovacích údajů globálního správce nebo coadminu pro vašeho tenanta.

3. Změňte verzi na **beta**verzi a načíst seznam instančních objektů z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogové okno Průzkumníka grafů s dotazem na načtení seznamu instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Ze seznamu načtených instančních objektů získáte ten, který je třeba upravit. Můžete také použít Ctrl + F pro vyhledávání v aplikaci ze všech uvedených instančních objektů. Vyhledejte ID objektu, který jste zkopírovali ze stránky **Vlastnosti,** a pomocí následujícího dotazu se přestaňte k instančnímu objektu:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz na získání instančního objektu, který je třeba upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahujte vlastnost **appRoles** z objektu instančního objektu.

    ![Podrobnosti o vlastnosti appRoles z objektu instančního objektu](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Chcete-li odstranit existující roli, použijte následující kroky.

    ![Požádat o tělo pro "PATCH", s IsEnabled nastavena na false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Změňte metodu z **GET** na **PATCH**.

    b. Zkopírujte existující role z aplikace a vložte je do **části Tělo požadavku**.

    c. Nastavte hodnotu **IsEnabled** na **hodnotu false** pro roli, kterou chcete odstranit.

    d. Vyberte **Spustit dotaz**.

    > [!NOTE]
    > Ujistěte se, že máte roli msiam_access a ID odpovídá generované roli.

7. Po zakázání role odstraňte tento blok role z části **appRoles.** Ponechte metodu jako **PATCH**a vyberte **spustit dotaz**.

8. Po spuštění dotazu je role odstraněna.

    > [!NOTE]
    > Role musí být zakázána před odebráním.

## <a name="next-steps"></a>Další kroky

Další kroky najdete v [dokumentaci k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
