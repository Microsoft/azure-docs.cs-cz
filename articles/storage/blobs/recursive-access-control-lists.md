---
title: Nastavit rekurzivní seznamy ACL pro Azure Data Lake Storage Gen2 | Microsoft Docs
description: Seznamy řízení přístupu můžete rekurzivně přidat, aktualizovat a odebrat pro existující podřízené položky nadřazeného adresáře.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/06/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: f9f0983bdb5e8763d13eeab8ea21bef7fb9ef47f
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803326"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Nastavení seznamů řízení přístupu (ACL) pro Azure Data Lake Storage Gen2 rekurzivně

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete také nyní přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku.

> [!NOTE]
> Možnost nastavit seznamy přístupu rekurzivně je ve verzi Public Preview a je dostupná ve všech oblastech.  

[Knihovny](#libraries)  |  [Ukázky](#code-samples)  |  [Osvědčené postupy](#best-practice-guidelines)  |  [Sdělte nám svůj názor](#provide-feedback)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.

- Správná oprávnění ke spuštění rekurzivního procesu seznamu ACL. Správné oprávnění zahrnuje jednu z následujících možností: 

  - Zřízeným [objektem zabezpečení](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.   

  - Vlastnící uživatel cílového kontejneru nebo adresáře, do kterého plánujete použít rekurzivní proces seznamu ACL. To zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři. 

- Porozumění způsobu použití seznamů ACL u adresářů a souborů. Viz [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

V části **nastavení projektu** v tomto článku najdete pokyny k instalaci PowerShellu, sady .NET SDK a sady Python SDK.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte potřebné knihovny.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ujistěte se, že je nainstalováno rozhraní .NET Framework. Viz [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Pomocí následujícího příkazu ověřte, že verze prostředí PowerShell, která je nainstalovaná, je `5.1` nebo vyšší.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell) .
    
3. Nainstalujte nejnovější verzi modulu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Zavřete a znovu otevřete konzolu PowerShellu.

5. Nainstalujte modul **AZ. Storage** Preview.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) .

### <a name="net"></a>[.NET](#tab/dotnet)

1. Otevřete příkazové okno (například prostředí Windows PowerShell).

2. Z adresáře projektu nainstalujte balíček Azure. Storage. Files. datalake Preview pomocí `dotnet add package` příkazu.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Přidejte tyto příkazy using do horní části souboru kódu.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Stáhněte si [klientskou knihovnu Azure Data Lake Storage pro Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Nainstalujte knihovnu, kterou jste stáhli pomocí [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Přidejte tyto příkazy pro import do horní části souboru kódu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Připojení k účtu

Můžete se připojit pomocí Azure Active Directory (AD) nebo pomocí klíče účtu. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otevřete příkazové okno prostředí Windows PowerShell a pak se přihlaste k předplatnému Azure pomocí `Connect-AzAccount` příkazu a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, ve kterém chcete vytvářet a spravovat adresáře. V tomto příkladu nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Dále vyberte způsob, jakým mají příkazy získat autorizaci k účtu úložiště. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Možnost 1: získání autorizace pomocí Azure Active Directory (AD)

V rámci tohoto přístupu systém zajistí, že váš uživatelský účet má odpovídající přiřazení Azure na základě role (Azure RBAC) a oprávnění ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

V následující tabulce jsou uvedeny všechny podporované role a jejich nastavení seznamu ACL.

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Možnost 2: získání autorizace pomocí klíče účtu úložiště

V rámci tohoto přístupu systém nekontroluje oprávnění Azure RBAC nebo ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , která představuje účet úložiště.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

Po instalaci balíčku přidejte tento příkaz using do horní části souboru kódu.

```csharp
using Azure.Identity;
```

Získejte ID klienta, tajný klíč klienta a ID tenanta. Pokud to chcete provést, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md). V rámci tohoto procesu budete muset k objektu zabezpečení přiřadit jednu z následujících rolí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) . 

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

Tento příklad vytvoří instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) pomocí ID klienta, tajného klíče klienta a ID tenanta.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Tento přístup je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) pomocí klíče účtu.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="python"></a>[Python](#tab/python)

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro Python](https://pypi.org/project/azure-identity/) .

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md). V rámci tohoto procesu budete muset k objektu zabezpečení přiřadit jednu z následujících rolí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) . 

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Nahraďte `storage_account_name` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

- Nahraďte `storage_account_key` hodnotu zástupného symbolu vaším klíčem pro přístup k účtu úložiště.

---

## <a name="set-an-acl-recursively"></a>Rekurzivní nastavení seznamu ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamu rekurzivního přístupu ACL](#update-an-acl-recursively) tohoto článku.   

Tato část obsahuje příklady nastavení seznamu ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nastavte seznam ACL rekurzivně pomocí `Set-AzDataLakeGen2AclRecursive` rutiny.

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Nastavte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL.

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Nastavte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient.set_access_control_recursive** .

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Rekurzivní aktualizace seznamu ACL

Když *aktualizujete* seznam řízení přístupu (ACL), místo nahrazení seznamu ACL ho upravte. Do seznamu ACL můžete například přidat nový objekt zabezpečení, aniž by to ovlivnilo jiné objekty zabezpečení uvedené v seznamu ACL.  Chcete-li nahradit seznam ACL místo aktualizace, přečtěte si část [Nastavení rekurzivního přístupu ACL](#set-an-acl-recursively) v tomto článku. 

Chcete-li aktualizovat seznam řízení přístupu, vytvořte nový objekt ACL s položkou seznamu ACL, kterou chcete aktualizovat, a pak tento objekt použijte v operaci aktualizovat seznam ACL. Nezískejte existující seznam ACL, stačí poskytnout položky seznamu ACL, které se mají aktualizovat.

Tato část obsahuje příklady, jak aktualizovat seznam ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rekurzivní aktualizace seznamu ACL pomocí rutiny **Update-AzDataLakeGen2AclRecursive** 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** . 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient.update_access_control_recursive** . 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Rekurzivní odebrání položek seznamu ACL

Jednu nebo více položek seznamu ACL můžete rekurzivně odebrat. Chcete-li odebrat položku seznamu řízení přístupu (ACL), vytvořte nový objekt ACL pro položku seznamu ACL, která se má odebrat, a pak tento objekt použijte v operaci odebrat seznam ACL. Nezískávat existující seznam ACL, stačí zadat položky seznamu ACL, které se mají odebrat. 

Tato část obsahuje příklady odebrání seznamu ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Odeberte položky seznamu ACL pomocí rutiny **Remove-AzDataLakeGen2AclRecursive** . 

Tento příklad odebere položku seznamu řízení přístupu z kořenového adresáře kontejneru.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . 

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient.remove_access_control_recursive** . 

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Obnovit po selhání

Může dojít k chybám za běhu nebo oprávnění. V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vrátí výsledky do proměnné. Neúspěšné položky kanálu pro formátovanou tabulku

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Na základě výstupu tabulky můžete opravit všechny chyby oprávnění a potom pokračovat v provádění pomocí tokenu pro pokračování.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu ``null`` parametru tokenu pokračování. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu ``None`` parametru tokenu pokračování. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Zdroje a prostředky

Tato část obsahuje odkazy na knihovny a ukázky kódu.

#### <a name="libraries"></a>Knihovny

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Ukázky kódů

- PowerShell: [Ukázka souboru Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: [Ukázka souboru Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Ukázka souboru Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Pokyny k osvědčeným postupům

V této části najdete některé doporučené postupy pro rekurzivní nastavení seznamů ACL. 

#### <a name="handling-runtime-errors"></a>Zpracování chyb běhového prostředí

K běhové chybě může dojít z mnoha důvodů (například při výpadku nebo potížích s připojením klienta). Pokud dojde k chybě za běhu, restartujte rekurzivní proces seznamu ACL. Seznamy řízení přístupu (ACL) lze znovu použít na položky, aniž by došlo k negativnímu dopadu. 

#### <a name="handling-permission-errors-403"></a>Zpracování chyb oprávnění (403)

Pokud při spuštění rekurzivního procesu seznamu ACL narazíte na výjimku řízení přístupu, váš [objekt zabezpečení](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) služby AD nemusí mít dostatečná oprávnění k použití seznamu ACL pro jednu nebo více podřízených položek v hierarchii adresáře. Pokud dojde k chybě oprávnění, proces se zastaví a poskytne se token pro pokračování. Opravte problém s oprávněním a pak použijte token pro pokračování ke zpracování zbývající datové sady. Adresáře a soubory, které již byly úspěšně zpracovány, nemusí být zpracovány znovu. Můžete také zvolit restartování rekurzivního procesu seznamu ACL. Seznamy řízení přístupu (ACL) lze znovu použít na položky, aniž by došlo k negativnímu dopadu. 

#### <a name="credentials"></a>Přihlašovací údaje 

Doporučujeme zřídit objekt zabezpečení služby Azure AD, kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového účtu úložiště nebo kontejneru. 

#### <a name="performance"></a>Výkon 

Pokud chcete snížit latenci, doporučujeme spustit rekurzivní proces seznamu ACL na virtuálním počítači Azure, který je umístěný ve stejné oblasti jako váš účet úložiště. 

#### <a name="acl-limits"></a>Omezení seznamu ACL

Maximální počet seznamů řízení přístupu, které můžete použít u adresáře nebo souboru, je 32 seznamů ACL a 32 výchozích seznamů ACL. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Poskytnutí zpětné vazby nebo hlášení problémů

Můžete zadat svůj názor nebo ohlásit problém na  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>Viz také

- [Řízení přístupu ve službě Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Známé problémy](data-lake-storage-known-issues.md)


