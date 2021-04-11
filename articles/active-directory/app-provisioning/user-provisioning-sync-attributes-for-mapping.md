---
title: Synchronizovat atributy pro Azure Active Directory pro mapování
description: Při konfiguraci zřizování uživatelů pomocí aplikací Azure Active Directory a SaaS použijte funkci rozšíření adresáře a přidejte zdrojové atributy, které nejsou synchronizované ve výchozím nastavení.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120788"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Synchronizují se atributy rozšíření pro zřizování aplikací.

Azure Active Directory (Azure AD) musí obsahovat všechna data (atributy) požadovaná k vytvoření profilu uživatele při zřizování uživatelských účtů z Azure AD do [aplikace SaaS](../saas-apps/tutorial-list.md). Při přizpůsobování mapování atributů pro zřizování uživatelů můžete najít atribut, který chcete mapovat, v seznamu **zdrojového atributu** se nezobrazí. V tomto článku se dozvíte, jak přidat chybějící atribut.

Pro uživatele ve službě Azure AD můžete [vytvářet rozšíření schématu pomocí PowerShellu nebo Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

Pro uživatele v místní službě Active Directory je nutné synchronizovat uživatele do Azure AD. Uživatele a atributy můžete synchronizovat pomocí [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect automaticky synchronizuje určité atributy do Azure AD, ale ne všechny atributy. Kromě toho nemusí být některé atributy (například SAMAccountName) synchronizované ve výchozím nastavení k dispozici pomocí Graph API Azure AD. V těchto případech můžete [použít funkci rozšíření adresář Azure AD Connect k synchronizaci atributu do Azure AD](#create-an-extension-attribute-using-azure-ad-connect). Tímto způsobem bude atribut viditelný pro Graph API Azure AD a službu Azure AD Provisioning.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Vytvoření atributu rozšíření v rámci jenom cloudového uživatele
K rozšiřování schématu uživatele pro uživatele v Azure AD můžete použít Microsoft Graph a PowerShell. Tyto atributy rozšíření jsou ve většině případů automaticky zjišťovány.

Pokud máte více než 1000 instančních objektů, můžete najít rozšíření chybějící v seznamu zdrojového atributu. Pokud se atribut, který jste vytvořili, automaticky nezobrazí, ověřte, že byl atribut vytvořen, a přidejte jej do schématu ručně. K ověření, že byla vytvořena, použijte Microsoft Graph a [Průzkumník grafů](/graph/graph-explorer/graph-explorer-overview.md). Chcete-li jej přidat do schématu ručně, přečtěte si téma [úpravy seznamu podporovaných atributů](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Vytvoření atributu rozšíření v cloudu jen pro uživatele pomocí Microsoft Graph
Schéma uživatelů Azure AD můžete roztáhnout pomocí [Microsoft Graph](/graph/overview.md). 

Nejdřív Seznamte aplikace ve vašem tenantovi, abyste získali ID aplikace, na které právě pracujete. Další informace najdete v tématu [seznam extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Dále vytvořte atribut rozšíření. Nahraďte níže uvedenou vlastnost **ID** **identifikátorem** , který jste získali v předchozím kroku. Bude nutné použít atribut **"ID"** , nikoli "appId". Další informace najdete v tématu [Create extensionProperty]/Graph/API/Application-post-extensionproperty.MD? View = Graph-REST-1.0&tabulátory = http&Preserve-View = true).

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

Předchozí požadavek vytvořil atribut rozšíření ve formátu `extension_appID_extensionName` . Nyní můžete aktualizovat uživatele pomocí tohoto atributu rozšíření. Další informace najdete v tématu [aktualizace uživatele](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Nakonec ověřte atribut pro uživatele. Další informace najdete v tématu [získání uživatele](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

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

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

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


## <a name="next-steps"></a>Další kroky

* [Definujte, kdo je v oboru pro zřizování.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
