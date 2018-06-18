---
title: Konfigurace role deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory | Microsoft Docs
description: Naučte se konfigurovat role deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory
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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5aa716f91a3155e81ef8dc7c436b4a9a5811238b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34723248"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Konfigurace role deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory

Pomocí služby Azure Active Directory (Azure AD) můžete přizpůsobit typ deklarace identity pro roli v odpovědi tokenu deklarací, které se zobrazí po autorizovat aplikaci.

## <a name="prerequisites"></a>Požadavky
- Předplatné služby Azure AD s nastavení adresáře.
- Předplatné, které má jednotné přihlašování (SSO) povolen. Je nutné nakonfigurovat jednotné přihlašování s vaší aplikací.

## <a name="when-to-use-this-feature"></a>Při použití této funkce

Pokud vaše aplikace očekává vlastní role být předán odpověď SAML, budete muset použít tuto funkci. Můžete vytvořit libovolný počet rolí, jako je třeba předat zpět z Azure AD vaší aplikace.

## <a name="create-roles-for-an-application"></a>Vytvořte role pro aplikaci

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Azure Active Directory – ikona][1]

2. Vyberte **podnikové aplikace, které**. Potom vyberte **všechny aplikace**.

    ![Podokno podnikových aplikací][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    !["Novou aplikaci" tlačítko][3]

4. Do vyhledávacího pole zadejte název vaší aplikace a pak vyberte aplikaci z panelu výsledek. Vyberte **přidat** tlačítko Přidat aplikaci.

    ![Aplikace v seznamu výsledků](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po přidání aplikace, přejděte na **vlastnosti** stránky a zkopírujte ID objektu.

    ![Stránky vlastností](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně a proveďte následující kroky:

    a. Přihlaste se k webu Explorer grafu pomocí globální správce nebo coadmin přihlašovacích údajů pro vašeho klienta.

    b. Potřebujete dostatečná oprávnění pro vytváření rolí. Vyberte **oprávnění k úpravě** získat oprávnění. 

      ![Tlačítko "Upravit oprávnění."](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Vyberte ze seznamu (Pokud nemáte tyto již) a vyberte následující oprávnění **upravit oprávnění**. 

      ![Seznam oprávnění a tlačítko "Upravit oprávnění"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Přijměte souhlasu. Jste přihlášeni k systému znovu.

    e. Změnit na verzi **beta**a načtení seznamu objektů služby z vašeho klienta s použitím následujícího dotazu:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Graf Explorer dialogové okno, pomocí dotazu pro načítání objekty služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. V seznamu objektů načtených služby získáte ten, který budete muset upravit. Ctrl + F můžete také použít k vyhledání aplikace z všechny objekty uvedené služby. Vyhledejte ID objektu, který jste zkopírovali ze **vlastnosti** stránky a používají následující dotaz pro přístup k objektu služby:
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Dotaz pro získání objektu služby, které budete muset upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahování **appRoles** vlastnost z objektu zabezpečení služby. 

      ![Podrobnosti o vlastnost appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Pokud používáte vlastní aplikaci (ne aplikace Azure Marketplace), zobrazí dvě výchozí role: uživateli a msiam_access. Pro aplikace Marketplace msiam_access je pouze výchozí role. Nepotřebujete žádné změny ve výchozích rolí.

    h. Generovat nové role pro vaši aplikaci. 

      Následujícím kódu JSON je příkladem **appRoles** objektu. Vytvořte objekt podobné k přidání rolí, které chcete pro vaši aplikaci. 

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
      > Pouze můžete přidat nové role po msiam_access pro operace opravy. Také můžete přidat libovolný počet rolí se potřeby vaší organizace. Azure AD odešle hodnotu tyto role jako hodnota deklarace identity v odpovědi SAML. Generovat identifikátor GUID hodnoty pro ID nové role pomocí nástroje pro web jako [to](https://www.guidgenerator.com/)
    
    i. Přejděte zpět do Průzkumníka graf a změňte metodu z **získat** k **oprava**. Oprava objekt zabezpečení služby tak, aby měl požadované role aktualizací **appRoles** vlastnost zobrazený v předchozím příkladu. Vyberte **spustit dotaz** k provedení operace opravy. Zpráva o úspěšném provedení potvrdí vytvoření role.

      ![Operace opravy se zprávou úspěch](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po objektu služby je nainstalována oprava s více rolí, můžete přiřadit uživatele do odpovídajících rolí. Uživatele můžete přiřadit tak, že přejdete na portál a procházení k aplikaci. Vyberte **uživatelů a skupin** kartě. Tato karta obsahuje všechny uživatele a skupiny, které jsou již přiřazena k aplikaci. Můžete přidat nové uživatele na nové role. Můžete také vybrat stávajícího uživatele a vyberte **upravit** Chcete-li změnit roli.

    ![Karta "Uživatelé a skupiny"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Přiřadit roli s žádným uživatelem, vyberte novou roli a vyberte **přiřadit** tlačítko v dolní části stránky.

    ![Podokna "Upravit přiřazení" a "Vybrat roli"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Budete muset aktualizovat relaci na portálu Azure zobrazíte nové role.

8. Aktualizace **atributy** můžete definovat vlastní mapování deklarace role.

9. V **uživatelské atributy** části **jednotného přihlašování** dialogu nastavte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky.
    
    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|    
    | Název role      | User.assignedrole |

    a. Vyberte **přidat atribut** otevřete **přidat atribut** podokně.

      ![Tlačítko "Přidat atribut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["Přidat atribut" podokno](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. V **název** podle potřeby zadejte název atributu. Tento příklad používá **název Role** jako název deklarace identity.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** pole prázdné.
    
    e. Vyberte **Ok**.

10. K testování aplikace v jednotné přihlašování, se spouští pomocí zprostředkovatele identity, přihlaste se k [přístupový Panel](https://myapps.microsoft.com) a vyberte dlaždici vaší aplikace. V tokenu SAML měli byste vidět všechny přiřazené role pro uživatele s názvem deklarace identity, které jste zadali.

## <a name="update-an-existing-role"></a>Aktualizuje existující roli.

Pokud chcete aktualizovat existující roli, proveďte následující kroky:

1. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Přihlaste se k webu Explorer grafu pomocí globální správce nebo coadmin přihlašovacích údajů pro vašeho klienta.
    
3. Změnit na verzi **beta**a načtení seznamu objektů služby z vašeho klienta s použitím následujícího dotazu:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graf Explorer dialogové okno, pomocí dotazu pro načítání objekty služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. V seznamu objektů načtených služby získáte ten, který budete muset upravit. Ctrl + F můžete také použít k vyhledání aplikace z všechny objekty uvedené služby. Vyhledejte ID objektu, který jste zkopírovali ze **vlastnosti** stránky a používají následující dotaz pro přístup k objektu služby:
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz pro získání objektu služby, které budete muset upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrahování **appRoles** vlastnost z objektu zabezpečení služby.
    
    ![Podrobnosti o vlastnost appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Chcete-li aktualizovat existující roli, použijte následující kroky.

    ![Text žádosti pro "PATCH," s "Popis" a "displayname" zvýrazněná](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. Změnit metodu z **získat** k **oprava**.

    b. Zkopírujte existující role a vložte je v části **text žádosti**.

    c. Aktualizujte hodnotu role aktualizací popis role, hodnota role nebo role zobrazovaný název podle potřeby.

    d. Po aktualizaci všechny požadované role, vyberte **spustit dotaz**.
        
## <a name="delete-an-existing-role"></a>Odstraňte existující roli

Pokud chcete odstranit existující roli, proveďte následující kroky:

1. Otevřete [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) v jiném okně.

2. Přihlaste se k webu Explorer grafu pomocí globální správce nebo coadmin přihlašovacích údajů pro vašeho klienta.

3. Změnit na verzi **beta**a načtení seznamu objektů služby z vašeho klienta s použitím následujícího dotazu:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Pokud používáte více adresářů, postupujte podle tohoto vzoru: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graf Explorer dialogové okno, s dotaz pro načtení seznamu objektů služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. V seznamu objektů načtených služby získáte ten, který budete muset upravit. Ctrl + F můžete také použít k vyhledání aplikace z všechny objekty uvedené služby. Vyhledejte ID objektu, který jste zkopírovali ze **vlastnosti** stránky a používají následující dotaz pro přístup k objektu služby:
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Dotaz pro získání objektu služby, které budete muset upravit](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrahování **appRoles** vlastnost z objektu zabezpečení služby.
    
    ![Podrobnosti o appRoles vlastnost z objektu zabezpečení služby](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Pokud chcete odstranit existující roli, použijte následující postup.

    ![Text žádosti pro "PATCH," s hodnotu IsEnabled nastavena na hodnotu false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Změnit metodu z **získat** k **oprava**.

    b. Zkopírujte existující role z aplikace a vložte je v části **text žádosti**.
        
    c. Nastavte **hodnotu IsEnabled** hodnotu **false** pro roli, kterou chcete odstranit.

    d. Vyberte **spuštění dotazu**.
    
    > [!NOTE] 
    > Ujistěte se, že máte msiam_access role, a je v roli generovaného odpovídající ID.
    
7. Po zakázání role je odstranit tento blok role z **appRoles** části. Zachovat jako metodu **oprava**a vyberte **spustit dotaz**.
    
8. Po spuštění dotazu je odstranit roli.
    
    > [!NOTE]
    > Roli musí být zakázána předtím, než může být odebrán. 

## <a name="next-steps"></a>Další postup

Další pokyny najdete v tématu [dokumentace k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png