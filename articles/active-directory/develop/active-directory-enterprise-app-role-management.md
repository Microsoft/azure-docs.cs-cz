---
title: Konfigurace deklarace role vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD | Dokumentace Microsoftu
description: Další informace o konfiguraci role deklarace identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory
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
ms.date: 01/07/2019
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: a354d5d0242bfdbb618eb325c8157a7bed3d3b64
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263652"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Postup: Konfigurace deklarace role vystavených v tokenu SAML pro podnikové aplikace

Pomocí Azure Active Directory (Azure AD) můžete přizpůsobit typ deklarace identity pro roli deklarací identity v tokenu odpovědi, že se zobrazí po autorizovat aplikaci.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure AD s nastavení adresáře.
- Předplatné, které má jednotné přihlašování (SSO) povolena. Je nutné nakonfigurovat jednotné přihlašování s vaší aplikací.

## <a name="when-to-use-this-feature"></a>Pokud chcete používat tuto funkci

Pokud vaše aplikace očekává, že vlastní role se mají předat odpověď SAML, budete muset použít tuto funkci. Můžete vytvořit tolik role, jako je potřeba předávat zpět z Azure AD pro vaši aplikaci.

## <a name="create-roles-for-an-application"></a>Vytvořte role pro aplikaci

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Ikona služby Azure Active Directory][1]

2. Vyberte **podnikové aplikace**. Potom vyberte **všechny aplikace**.

    ![Podokno podnikových aplikací][2]

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    !["Nové aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte název vaší aplikace a pak vyberte svoji aplikaci na panelu výsledků. Vyberte **přidat** tlačítko pro přidání aplikace.

    ![Aplikace v seznamu výsledků](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po přidání aplikace, přejděte **vlastnosti** stránce a zkopírujte ID objektu.

    ![Stránky vlastností](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otevřít [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně a proveďte následující kroky:

    a. Přihlaste se k webu Graph Exploreru pomocí globální správce nebo coadmin přihlašovacích údajů pro vašeho tenanta.

    b. Potřebujete udělit oprávnění pro vytvoření role. Vyberte **změnit oprávnění** oprávnění.

      ![Tlačítko "Upravit oprávnění"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Vyberte ze seznamu (pokud tyto ještě nemáte) a vyberte následující oprávnění **upravit oprávnění**.

      ![Seznam oprávnění a tlačítko "Upravit oprávnění"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Role správce cloudové aplikace a aplikace správce nebude fungovat v tomto scénáři jsme potřebujete oprávnění globálního správce adresáře pro čtení a zápisu.

    d. Přijměte souhlasu. Jste přihlášení k systému znovu.

    e. Změnit na verzi, aby **beta**a načíst seznam instanční objekty z vašeho tenanta pomocí následujícího dotazu:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Graph Explorer dialogovém okně s dotazem pro načtení instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Už jsme se v současnosti inovaci rozhraní API tak, aby zákazníkům můžou zobrazovat některých přerušení služby.

    f. V seznamu načtených instanční objekty získejte ten, který budete muset upravit. Ctrl + F můžete použít také pro hledání aplikací ze všech objektů uvedené služby. Vyhledejte ID objektu, který jste zkopírovali **vlastnosti** stránky a použijte tento dotaz pro získání instančnímu objektu služby:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Dotaz pro získání instanční objekt, který budete muset upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahovat **appRoles** vlastnost z objektu instančního objektu služby.

      ![Podrobnosti o vlastnost appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Pokud používáte vlastní aplikace (ne aplikace Azure Marketplace), zobrazí se dvě výchozí role: uživateli a msiam_access. Pro aplikace Marketplace msiam_access je pouze výchozí role. Není nutné provádět žádné změny ve výchozích rolí.

    h. Generovat nové role pro vaši aplikaci.

      Následující kód JSON je příkladem **appRoles** objektu. Vytvořte objekt podobné pro přidání rolí, které chcete pro vaši aplikaci.

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
      > Po msiam_access pro operaci patch můžete přidat jenom nové role. Také můžete přidat tolik role podle potřeb vaší organizace. Azure AD pošle hodnotu z těchto rolí jako hodnota deklarace identity v odpověď SAML. Ke generování identifikátoru GUID hodnoty pro ID nové role používat webové nástroje, například [to](https://www.guidgenerator.com/)

    i. Přejděte zpět do Průzkumníku graf a změňte metodu z **získat** k **oprava**. Oprava instanční objekt mít požadovaných rolí aktualizací **appRoles** vlastnost podobný jako v předchozím příkladu. Vyberte **spustit dotaz** k provedení operace opravy. Zpráva o úspěšném provedení potvrdí vytvoření role.

      ![Operace opravy se zpráva o úspěchu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po instanční objekt služby je opravit s více rolemi, můžete přiřadit příslušné role uživatele. Když přejdete na portál a procházení k aplikaci můžete přiřadit uživatele. Vyberte **uživatelů a skupin** kartu. Tato karta obsahuje všechny uživatele a skupiny, které jsou už přiřazené k aplikaci. Můžete přidat nové uživatele na nové role. Můžete také vybrat existující uživatele a vybrat **upravit** změnit roli.

    ![Karta "Uživatele a skupiny"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Přiřadit roli s žádným uživatelem, vyberte novou roli a vyberte **přiřadit** tlačítko v dolní části stránky.

    ![Podokna "Upravit přiřazení" a "Vybrat roli"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Bude nutné aktualizovat vaše relace na webu Azure Portal najdete v nové role.

8. Aktualizace **atributy** můžete definovat vlastní mapování deklarace role.

9. V **atributy uživatele a deklarace identity** část **jednotného přihlašování** dialogového okna zadejte nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky.

    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|
    | Název role  | user.assignedroles |

    a. Klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

      ![Tlačítko "Přidat atribut"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Vyberte **přidat atribut** otevřít **spravovat deklarace identity uživatelů** podokně.

      ![Tlačítko "Přidat atribut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Podokno "Přidat atribut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. V **název** podle potřeby zadejte název atributu. Tento příklad používá **název Role** jako název deklarace identity.

    d. Nechte **Namespace** pole prázdné.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Vyberte **Uložit**.

10. K testování aplikace v aplikaci jednotné přihlašování iniciované poskytovatelem identity, přihlaste se k [přístupového panelu](https://myapps.microsoft.com) a vyberte dlaždici vaší aplikace. V tokenu SAML měli byste vidět všechny přiřazené role pro uživatele s názvem deklarace identity, která jste udělili.

## <a name="update-an-existing-role"></a>Aktualizuje existující roli.

Pokud chcete aktualizovat existující role, postupujte následovně:

1. Otevřít [Azure AD Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer).

2. Přihlaste se k webu Graph Exploreru pomocí globální správce nebo coadmin přihlašovacích údajů pro vašeho tenanta.

3. Změnit na verzi, aby **beta**a načíst seznam instanční objekty z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer dialogovém okně s dotazem pro načtení instančních objektů](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. V seznamu načtených instanční objekty získejte ten, který budete muset upravit. Ctrl + F můžete použít také pro hledání aplikací ze všech objektů uvedené služby. Vyhledejte ID objektu, který jste zkopírovali **vlastnosti** stránky a použijte tento dotaz pro získání instančnímu objektu služby:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz pro získání instanční objekt, který budete muset upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahovat **appRoles** vlastnost z objektu instančního objektu služby.

    ![Podrobnosti o vlastnost appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Pokud chcete aktualizovat existující role, použijte následující kroky.

    ![Text žádosti pro "Opravy" s "Popis" a "displayname" zvýrazněnou](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Změnit metodu z **získat** k **oprava**.

    b. Existující role kopírování a vložení v rámci **tělo požadavku**.

    c. Když aktualizujete popis role, hodnota role nebo role zobrazovaný název, podle potřeby aktualizujte hodnotu role.

    d. Po aktualizaci všech požadovaných rolí vyberte **spustit dotaz**.

## <a name="delete-an-existing-role"></a>Odstranit existující roli

Pokud chcete odstranit existující role, postupujte následovně:

1. Otevřít [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

2. Přihlaste se k webu Graph Exploreru pomocí globální správce nebo coadmin přihlašovacích údajů pro vašeho tenanta.

3. Změnit na verzi, aby **beta**a načíst seznam instanční objekty z vašeho tenanta pomocí následujícího dotazu:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Rozhraní Graph Exploreru dialogovém okně s dotazem pro načtení seznamu objektů služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. V seznamu načtených instanční objekty získejte ten, který budete muset upravit. Ctrl + F můžete použít také pro hledání aplikací ze všech objektů uvedené služby. Vyhledejte ID objektu, který jste zkopírovali **vlastnosti** stránky a použijte tento dotaz pro získání instančnímu objektu služby:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz pro získání instanční objekt, který budete muset upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahovat **appRoles** vlastnost z objektu instančního objektu služby.

    ![Podrobnosti o appRoles vlastnosti z objektu instančního objektu služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Pokud chcete odstranit existující role, použijte následující kroky.

    ![Text žádosti pro "Opravy" s IsEnabled nastavena na hodnotu false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Změnit metodu z **získat** k **oprava**.

    b. Kopírování existujících rolí z aplikace a vložení v rámci **tělo požadavku**.

    c. Nastavte **IsEnabled** hodnota, která se **false** pro roli, kterou chcete odstranit.

    d. Vyberte **spuštění dotazu**.

    > [!NOTE]
    > Ujistěte se, že máte roli msiam_access a je v roli generované odpovídající ID.

7. Po zakázání role odstraňte tento blok role z **appRoles** oddílu. Zachovat metodu jako **oprava**a vyberte **spustit dotaz**.

8. Po spuštění dotazu se odstraní roli.

    > [!NOTE]
    > Role je potřeba zakázat předtím, než je možné odebrat.

## <a name="next-steps"></a>Další postup

Další pokyny najdete v článku [dokumentace k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png