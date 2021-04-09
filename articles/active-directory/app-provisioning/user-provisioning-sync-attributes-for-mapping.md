---
title: Synchronizovat atributy s Azure AD pro mapování
description: Při konfiguraci zřizování uživatelů pro aplikace SaaS použijte funkci rozšíření adresáře a přidejte zdrojové atributy, které se ve výchozím nastavení nesynchronizují.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579429"
---
# <a name="syncing-extension-attributes-attributes"></a>Synchronizace atributů atributů rozšíření

Při přizpůsobování mapování atributů pro zřizování uživatelů můžete zjistit, že atribut, který chcete mapovat, se nezobrazí v seznamu **zdrojového atributu** . V tomto článku se dozvíte, jak přidat chybějící atribut tak, že ho synchronizujete z místní služby Active Directory (AD) do služby Azure Active Directory (Azure AD) nebo když ve službě Azure AD vytvoříte atributy rozšíření jenom pro Cloud uživatele. 

Azure AD musí obsahovat všechna data potřebná k vytvoření profilu uživatele při zřizování uživatelských účtů z Azure AD do aplikace SaaS. V některých případech je možné, že budete potřebovat synchronizovat atributy z místní služby AD do Azure AD. Azure AD Connect automaticky synchronizuje určité atributy do Azure AD, ale ne všechny atributy. Kromě toho nemusí být některé atributy (například SAMAccountName) synchronizované ve výchozím nastavení k dispozici pomocí Graph API Azure AD. V těchto případech můžete použít funkci rozšíření adresář Azure AD Connect k synchronizaci atributu do Azure AD. Tímto způsobem bude atribut viditelný pro Graph API Azure AD a službu Azure AD Provisioning. Pokud jsou data potřebná pro zřizování ve službě Active Directory, ale nejsou k dispozici pro zřizování z důvodu výše popsaných výše, můžete použít Azure AD Connect k vytvoření atributů rozšíření. 

I když většina uživatelů je pravděpodobně hybridními uživateli, kteří jsou synchronizovaný ze služby Active Directory, můžete také vytvořit rozšíření pouze pro cloudové uživatele bez použití Azure AD Connect. Pomocí PowerShellu nebo Microsoft Graph můžete roztáhnout schéma pouze uživatele cloudu. 

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Vytvoření atributu rozšíření pomocí Azure AD Connect

1. Otevřete Průvodce Azure AD Connect, zvolte úlohy a pak zvolte **přizpůsobit možnosti synchronizace**.

   ![Stránka další úlohy Průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Přihlaste se jako globální správce Azure AD. 

3. Na stránce **volitelné funkce** vyberte možnost **synchronizace atributů rozšíření adresáře**.
 
   ![Stránka volitelných funkcí Průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Vyberte atributy, které chcete pro Azure AD zvětšit.
   > [!NOTE]
   > Hledání v části **dostupné atributy** rozlišuje velká a malá písmena.

   ![Snímek obrazovky zobrazující stránku výběr rozšíření adresářových souborů](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Dokončete průvodce Azure AD Connect a umožněte spuštění úplný cyklus synchronizace. Po dokončení cyklu se schéma rozšíří a nové hodnoty se synchronizují mezi místními službami AD a Azure AD.
 
6. V Azure Portal při [úpravách mapování atributů uživatele](customize-application-attributes.md)bude seznam **zdrojových atributů** nyní obsahovat přidaný atribut ve formátu `<attributename> (extension_<appID>_<attributename>)` . Vyberte atribut a namapujte ho k cílové aplikaci pro zřizování.

   ![Stránka pro výběr rozšíření adresáře průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Možnost zřízení referenčních atributů z místní služby AD, jako je **ManagedBy** nebo **DN/rozlišující jméno**, není dnes podporována. Tuto funkci si můžete vyžádat na [uživatelském hlasu](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Vytvoření atributu rozšíření v rámci jenom cloudového uživatele
Zákazníci můžou použít Microsoft Graph a PowerShell k rozšiřování schématu uživatele. Tyto atributy rozšíření se ve většině případů automaticky zjišťují, ale zákazníci s více než 1000 instančními objekty můžou najít rozšíření chybějící v seznamu zdrojového atributu. Pokud se atribut, který vytvoříte pomocí následujících kroků, nezobrazuje automaticky v seznamu zdrojového atributu, ověřte pomocí grafu, že byl atribut rozšíření úspěšně vytvořen a pak jej přidejte do schématu [ručně](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes). Při vytváření požadavků na graf níže klikněte na Další informace a ověřte oprávnění potřebná k provedení požadavků. K vytvoření požadavků můžete použít [Průzkumníka grafů](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) . 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Vytvoření atributu rozšíření v cloudu jen pro uživatele pomocí Microsoft Graph
K rozšiřování schématu uživatelů budete muset použít aplikaci. Seznamte se s aplikacemi ve vašem tenantovi, abyste identifikovali ID aplikace, kterou chcete použít k rozšiřování schématu uživatele. [Další informace](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Vytvořte atribut Extension. Nahraďte níže uvedenou vlastnost **ID** **identifikátorem** , který jste získali v předchozím kroku. Bude nutné použít atribut **"ID"** , nikoli "appId". [Další informace](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

Předchozí požadavek vytvořil atribut rozšíření ve formátu "extension_appID_extensionName". Aktualizujte uživatele pomocí atributu Extension. [Další informace](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Zkontrolujte uživatele a ujistěte se, že byl atribut úspěšně aktualizován. [Další informace](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Vytvoření atributu rozšíření v cloudu jenom pro uživatele pomocí PowerShellu
Vytvořte vlastní rozšíření pomocí PowerShellu a přiřaďte mu hodnotu. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Další kroky

* [Definujte, kdo je v oboru pro zřizování.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
