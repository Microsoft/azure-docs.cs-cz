---
title: Použití spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k rozhraní Azure AD Graph API
description: Tento kurz vás provede použitím spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k rozhraní Azure AD Graph API.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: daveba
ms.openlocfilehash: 57e719c6ef75b08d8c188d2d2d344867bbf590b1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623090"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k rozhraní Azure AD Graph API

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak použít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup k rozhraní Azure AD Graph API za účelem načtení jeho členství ve skupinách. Spravované identity pro prostředky Azure se spravují automaticky v Azure a umožňují vám ověřovat přístup ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu.  

V tomto kurzu se budete dotazovat na členství identity virtuálního počítače ve skupinách služby Azure AD. Informace o skupinách se často používají při rozhodování o autorizaci. Spravovaná identita virtuálního počítače je uvnitř služby Azure AD reprezentovaná **instančním objektem**. 

> [!div class="checklist"]
> * Připojení k Azure AD
> * Přidání identity virtuálního počítače do skupiny ve službě Azure AD 
> * Udělení přístupu k Azure AD Graphu identitě virtuálního počítače 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure AD Graphu

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Instalace nejnovější verze Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Abyste identitě virtuálního počítače mohli udělit přístup k Azure AD Graphu, musí být ve službě Azure AD váš účet přiřazený k roli **globálního správce**.

## <a name="connect-to-azure-ad"></a>Připojení k Azure AD

Kvůli tomu, abyste virtuální počítač přiřadili do skupiny a udělili mu oprávnění k načtení jeho členství ve skupinách, se musíte připojit ke službě Azure AD.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Přidání identity virtuálního počítače do skupiny ve službě Azure AD

Když jste u virtuálního počítače s Linuxem povolili spravovanou identitu přiřazenou systémem, vytvořil se ve službě Azure AD instanční objekt.  Virtuální počítač je potřeba přidat do nějaké skupiny. Postup přidání virtuálního počítače do skupiny ve službě Azure AD najdete v následujícím článku:

- [Přidání členů skupiny](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Udělení přístupu k rozhraní Azure AD Graph API virtuálnímu počítači

Když použijete spravované identity pro prostředky Azure, může kód získat přístupové tokeny sloužící k ověření přístupu k prostředkům, které podporují ověřování Azure AD. Rozhraní Microsoft Azure AD Graph API podporuje ověřování Azure AD. V tomto kroku udělíte instančnímu objektu identity virtuálního počítače přístup k Azure AD Graphu, aby se mohl dotazovat na členství ve skupinách. Instanční objekty mají udělený přístup k Microsoft nebo Azure AD Graphu prostřednictvím **oprávnění aplikace**. Typ oprávnění aplikace, který potřebujete udělit, závisí na entitě, ke které chcete v MS nebo Azure AD Graphu získat přístup.

V tomto kurzu udělíte identitě virtuálního počítače schopnost dotazování členství ve skupinách pomocí oprávnění aplikace `Directory.Read.All`. K udělení tohoto oprávnění budete potřebovat uživatelský účet, který má ve službě Azure AD přiřazenou roli globálního správce. Normálně byste oprávnění aplikace udělili tak, že byste přešli na registraci vaší aplikace na webu Azure Portal a přidali oprávnění tam. Spravované identity prostředků Azure ale ve službě Azure AD neregistrují objekty aplikací, pouze instanční objekty. K registraci oprávnění aplikace použijete nástroj pro příkazový řádek Azure AD PowerShell. 

Azure AD Graph:
- ID aplikace instančního objektu (používá se při udělování oprávnění aplikace): 00000002-0000-0000-c000-000000000000
- ID prostředku (používá se při žádosti o přístupový token ze spravovaných identit prostředků Azure): https://graph.windows.net
- Odkaz na obor oprávnění: [Odkaz na oprávnění Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Udělení oprávnění aplikace přes CURL

1. Načtením tokenu učiňte žádosti CURL:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Budete muset načíst `objectId` vašeho virtuálního počítače a poznamenat si ho. V dalších krocích ho použijete k tomu, abyste virtuálnímu počítači udělili oprávnění ke čtení jeho členství ve skupinách. `<ACCESS TOKEN>` nahraďte přístupovým tokenem, který jste získali v předchozím kroku.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Pomocí ID aplikace Azure AD Graphu (00000002-0000-0000-c000-000000000000) načtěte a poznamenejte `objectId` pro `odata.type: Microsoft.DirectoryServices.ServicePrincipal` a `id` pro oprávnění role aplikace `Directory.Read.All`.  `<ACCESS TOKEN>` nahraďte dříve získaným přístupovým tokenem.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpověď:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Teď udělte instančnímu objektu virtuálního počítače přístup ke čtení objektů adresáře Azure AD pomocí rozhraní Azure AD Graph API.  Hodnota `id` je hodnota oprávnění role aplikace `Directory.Read.All` a `resourceId` je `objectId` instančního objektu `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (tyto hodnoty jste si poznamenali v předchozím kroku).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Získání přístupového tokenu pomocí identity virtuálního počítače k volání služby Azure AD Graph 

K dokončení tohoto postupu budete potřebovat klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](../../virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu přejděte ke svému linuxovému virtuálnímu počítači a v části **Přehled** klikněte na **Připojit**.  
2. **Připojte** se vybraným klientem SSH k virtuálnímu počítači. 
3. V okně terminálu požádejte nástrojem CURL místní spravované identity o koncový bod prostředků Azure, abyste získali přístupový token pro Azure AD Graph.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   V odpovědi je přístupový token, který potřebujete pro přístup k Azure AD Graphu.

   Odpověď:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Pomocí ID objektu instančního objektu virtuálního počítače (hodnota, kterou jste získali v předchozích krocích) se můžete dotazovat rozhraní Azure AD Graph API a načíst jeho členství ve skupinách. `<OBJECT-ID>` nahraďte ID objektu instančního objektu virtuálního počítače a `<ACCESS-TOKEN>` dříve získaným přístupovým tokenem:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Odpověď:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak využít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup k Azure AD Graphu.  Další informace o Azure AD Graphu najdete zde:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)