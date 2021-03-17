---
title: Synchronizovat atributy s Azure AD pro mapování
description: Naučte se synchronizovat atributy z vaší místní služby Active Directory do Azure AD. Při konfiguraci zřizování uživatelů pro aplikace SaaS použijte funkci rozšíření adresáře a přidejte zdrojové atributy, které se ve výchozím nastavení nesynchronizují.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.author: kenwith
ms.openlocfilehash: 0f8369c80a7a219b159f31aacb7d10a0dd009d00
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418670"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizace atributu z místní služby Active Directory do Azure AD kvůli zřizování pro aplikaci

Při přizpůsobování mapování atributů pro zřizování uživatelů můžete zjistit, že atribut, který chcete mapovat, se nezobrazí v seznamu **zdrojového atributu** . V tomto článku se dozvíte, jak přidat chybějící atribut synchronizací z místní služby Active Directory (AD) do Azure Active Directory (Azure AD).

Azure AD musí obsahovat všechna data potřebná k vytvoření profilu uživatele při zřizování uživatelských účtů z Azure AD do aplikace SaaS. V některých případech je možné, že budete potřebovat synchronizovat atributy z místní služby AD do Azure AD. Azure AD Connect automaticky synchronizuje určité atributy do Azure AD, ale ne všechny atributy. Kromě toho je možné, že některé atributy (například SAMAccountName), které jsou synchronizovány ve výchozím nastavení, nemusí být vystaveny pomocí rozhraní Microsoft Graph API. V těchto případech můžete použít funkci rozšíření adresář Azure AD Connect k synchronizaci atributu do Azure AD. Tímto způsobem bude atribut viditelný pro rozhraní Microsoft Graph API a službu Azure AD Provisioning.

Pokud jsou data potřebná pro zřizování ve službě Active Directory, ale nejsou k dispozici pro zřizování z důvodu výše popsaných výše, můžete k vytváření atributů rozšíření použít Azure AD Connect nebo PowerShell. 
 
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

## <a name="create-an-extension-attribute-using-powershell"></a>Vytvoření atributu rozšíření pomocí PowerShellu
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
