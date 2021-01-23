---
title: Nastavit rekurzivní seznamy ACL pro Azure Data Lake Storage Gen2 | Microsoft Docs
description: Seznamy řízení přístupu můžete rekurzivně přidat, aktualizovat a odebrat pro existující podřízené položky nadřazeného adresáře.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 626e626cbd8fa86bd0366516cbaf5a54789f3988
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741039"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Nastavení seznamů řízení přístupu (ACL) pro Azure Data Lake Storage Gen2 rekurzivně

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete také nyní přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku.

[Knihovny](#libraries)  |  [Ukázky](#code-samples)  |  [Osvědčené postupy](#best-practice-guidelines)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Správná oprávnění ke spuštění rekurzivního procesu seznamu ACL. Správné oprávnění zahrnuje jednu z následujících možností: 

  - Zřízeným [objektem zabezpečení](../../role-based-access-control/overview.md#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.   

  - Vlastnící uživatel cílového kontejneru nebo adresáře, do kterého plánujete použít rekurzivní proces seznamu ACL. To zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři. 

- Porozumění způsobu použití seznamů ACL u adresářů a souborů. Viz [řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

V části **nastavení projektu** v tomto článku najdete pokyny k instalaci PowerShellu, sady .NET SDK a sady Python SDK.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte potřebné knihovny.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Pomocí následujícího příkazu ověřte, že verze prostředí PowerShell, která je nainstalovaná, je `5.1` nebo vyšší.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) .
    
2. Nainstalujte modul **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Otevřete [Azure Cloud Shell](../../cloud-shell/overview.md)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](/cli/azure/install-azure-cli) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pomocí následujícího příkazu ověřte, že verze rozhraní příkazového řádku Azure, která je nainstalovaná, je `2.14.0` nebo vyšší.

   ```azurecli
    az --version
   ```
   Pokud je vaše verze rozhraní příkazového řádku Azure nižší než `2.14.0` , nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

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

### <a name="java"></a>[Java](#tab/java)

Začněte tím, že otevřete [tuto stránku](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) a získáte nejnovější verzi knihovny Java. Pak otevřete soubor *pom.xml* v textovém editoru. Přidejte element závislosti, který odkazuje na tuto verzi.

Pokud plánujete ověřování klientské aplikace pomocí Azure Active Directory (AD), přidejte závislost do klientské knihovny tajného kódu Azure. Viz  [Přidání balíčku tajné klientské knihovny do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Dále přidejte tyto příkazy import do souboru kódu.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Stáhněte si [klientskou knihovnu Azure Data Lake Storage pro Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D).

2. Nainstalujte knihovnu, kterou jste stáhli pomocí [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Přidejte tyto příkazy pro import do horní části souboru kódu.

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

#### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

V rámci tohoto přístupu systém zajistí, že váš uživatelský účet má odpovídající přiřazení Azure na základě role (Azure RBAC) a oprávnění ACL. 

V následující tabulce jsou uvedeny všechny podporované role a jejich nastavení seznamu ACL.

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

1. Otevřete příkazové okno prostředí Windows PowerShell a pak se přihlaste k předplatnému Azure pomocí `Connect-AzAccount` příkazu a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, ve kterém chcete vytvářet a spravovat adresáře. V tomto příkladu nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```
3. Získejte kontext účtu úložiště.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

#### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

V rámci tohoto přístupu systém nekontroluje oprávnění Azure RBAC nebo ACL. Získejte kontext účtu úložiště pomocí klíče účtu.

```powershell
$ctx = New-AzStorageContext -StorageAccountName "<storage-account-name>" -StorageAccountKey "<storage-account-key>"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pokud používáte Azure CLI místně, spusťte příkaz Login.

   ```azurecli
   az login
   ```

   Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

   V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu. Pak se přihlaste pomocí přihlašovacích údajů k účtu v prohlížeči.

   Další informace o různých metodách ověřování najdete v tématu [autorizace přístupu k objektům blob nebo Queue data z Azure pomocí Azure CLI](./authorize-data-operations-cli.md).

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

> [!NOTE]
> Příklad uvedený v tomto článku ukazuje autorizaci Azure Active Directory (AD). Další informace o metodách autorizace najdete v tématu [autorizace přístupu k objektům blob nebo Queue data z Azure pomocí Azure CLI](./authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , která představuje účet úložiště.

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

Tento příklad vytvoří instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) pomocí ID klienta, tajného klíče klienta a ID tenanta.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

#### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Tento přístup je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) pomocí klíče účtu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="java"></a>[Java](#tab/java)

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

#### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Azure Active Directory (Azure AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="python"></a>[Python](#tab/python)

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro Python](https://pypi.org/project/azure-identity/) .

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md). V rámci tohoto procesu budete muset k objektu zabezpečení přiřadit jednu z následujících rolí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) . 

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Nahraďte `storage_account_name` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

- Nahraďte `storage_account_key` hodnotu zástupného symbolu vaším klíčem pro přístup k účtu úložiště.

---

## <a name="set-an-acl-recursively"></a>Rekurzivní nastavení seznamu ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamu rekurzivního přístupu ACL](#update-an-acl-recursively) tohoto článku.  

Pokud se rozhodnete *nastavit* seznam řízení přístupu (ACL), musíte přidat položku pro vlastnícího uživatele, položku pro vlastnící skupinu a položku pro všechny ostatní uživatele. Další informace o vlastnícím uživateli, vlastnící skupině a všech ostatních uživatelích najdete v tématu [Uživatelé a identity](data-lake-storage-access-control.md#users-and-identities). 

Tato část obsahuje příklady nastavení seznamu ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nastavte seznam ACL rekurzivně pomocí rutiny **set-AzDataLakeGen2AclRecursive** .

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

> [!NOTE]
> Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), použijte při spuštění příkazu **set-AzDataLakeGen2ItemAclObject** parametr **-DefaultScope platná** . Příklad: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Pokud chcete zobrazit příklad, který rekurzivně nastavuje seznam ACL v dávkách zadáním velikosti dávky, přečtěte si článek s odkazem [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rekurzivní nastavení seznamu ACL pomocí příkazu [AZ Storage FS Access set-rerecursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu, přidejte `default:` k jednotlivým položkám předponu. Příkladem je `default:user::rwx` nebo `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Nastavte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL. 

Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), můžete vlastnost [PathAccessControlItem. DefaultScope platná](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) [nastavit na](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) **hodnotu true**. 

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má nastavit výchozí seznam řízení přístupu. Tento parametr se používá v konstruktoru třídy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Položky seznamu řízení přístupu poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině jenom oprávnění ke čtení a spouštění a všem ostatním uživatelům se neudělí žádný přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Příklad, který v dávkách nastavuje rekurzivní seznamy ACL zadáním velikosti dávky, najdete v [ukázce](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Nastavte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. setAccessControlRecursive** . Předá tuto metodu [seznam](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) objektů [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Každý [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definuje položku seznamu ACL. 

Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), můžete zavolat metodu **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předat hodnotu **true**. 

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má nastavit výchozí seznam řízení přístupu. Tento parametr se používá v každém volání metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Položky seznamu řízení přístupu poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině jenom oprávnění ke čtení a spouštění a všem ostatním uživatelům se neudělí žádný přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Nastavte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient.set_access_control_recursive** .

Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), přidejte řetězec `default:` na začátek každého řetězce položky seznamu ACL. 

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . 

Tato metoda přijímá logický parametr s názvem `is_default_scope` , který určuje, zda se má nastavit výchozí seznam řízení přístupu. Pokud je tento parametr `True` , seznam položek seznamu ACL předchází řetězci `default:` . 

Položky seznamu řízení přístupu poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině jenom oprávnění ke čtení a spouštění a všem ostatním uživatelům se neudělí žádný přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit. Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

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

> [!NOTE]
> Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), použijte při spuštění příkazu **set-AzDataLakeGen2ItemAclObject** parametr **-DefaultScope platná** . Příklad: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Pokud chcete zobrazit příklad, který provede rekurzivní aktualizaci seznamů ACL v dávkách zadáním velikosti dávky, přečtěte si článek Referenční dokumentace [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rekurzivní aktualizace seznamu ACL pomocí příkazu  [AZ Storage FS Access Update – rekurzivní](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu, přidejte `default:` k jednotlivým položkám předponu. Například `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL. 

Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), můžete vlastnost [PathAccessControlItem. DefaultScope platná](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) [nastavit na](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) **hodnotu true**. 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má aktualizovat výchozí seznam ACL. Tento parametr se používá v konstruktoru třídy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Pokud chcete zobrazit příklad, který provede rekurzivní aktualizaci seznamů ACL v dávkách zadáním velikosti dávky, přečtěte si [ukázku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. updateAccessControlRecursive** .  Předá tuto metodu [seznam](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) objektů [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Každý [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definuje položku seznamu ACL. 

Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), můžete **setDefaultScope** metodu [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předat hodnotu **true**. 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má aktualizovat výchozí seznam ACL. Tento parametr se používá při volání metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient.update_access_control_recursive** . Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), přidejte řetězec `default:` na začátek každého řetězce položky seznamu ACL. 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu.

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `is_default_scope` , který určuje, zda se má aktualizovat výchozí seznam ACL. Pokud je tento parametr `True` , aktualizovaná položka seznamu ACL předá řetězec `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

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

> [!NOTE]
> Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), použijte při spuštění příkazu **set-AzDataLakeGen2ItemAclObject** parametr **-DefaultScope platná** . Příklad: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Příklad, který v dávkách odebírá rekurzivní seznamy ACL zadáním velikosti dávky, najdete v článku reference [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ Storage FS Access Remove-rekurzivní](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) odeberte položky seznamu ACL. 

Tento příklad odebere položku seznamu řízení přístupu z kořenového adresáře kontejneru.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Chcete-li odebrat **výchozí** položku seznamu řízení přístupu (ACL), přidejte `default:` k jednotlivým položkám předponu. Například `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL. 

Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), můžete vlastnost [PathAccessControlItem. DefaultScope platná](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) [nastavit na](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) **hodnotu true**. 

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda má být položka odebrána z výchozího seznamu ACL. Tento parametr se používá v konstruktoru třídy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Pokud chcete zobrazit příklad, který v dávkách odebírá rekurzivní seznamy ACL zadáním velikosti dávky, přečtěte si [ukázku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient. removeAccessControlRecursive** . Předá tuto metodu [seznam](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) objektů [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Každý [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definuje položku seznamu ACL. 

Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), můžete **setDefaultScope** metodu [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předat hodnotu **true**.  

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda má být položka odebrána z výchozího seznamu ACL. Tento parametr se používá při volání metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient.remove_access_control_recursive** . Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), přidejte řetězec `default:` na začátek řetězce položky seznamu řízení přístupu. 

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `is_default_scope` , který určuje, zda má být položka odebrána z výchozího seznamu ACL. Pokud je tento parametr `True` , aktualizovaná položka seznamu ACL předá řetězec `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

---

## <a name="recover-from-failures"></a>Obnovit po selhání

Může dojít k chybám za běhu nebo oprávnění. V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento příklad vrátí výsledky do proměnné a poté kanály neúspěšných položek do naformátované tabulky.

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

Pokud chcete zobrazit příklad, který rekurzivně nastavuje seznam ACL v dávkách zadáním velikosti dávky, přečtěte si článek s odkazem [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

V případě selhání můžete vrátit token pokračování nastavením `--continue-on-failure` parametru na `false` . Po vyřešení chyb můžete proces obnovit z bodu selhání tak, že znovu spustíte příkaz a pak nastavíte `--continuation` parametr na token pro pokračování. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu `null` parametru tokenu pokračování. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Příklad, který v dávkách nastavuje rekurzivní seznamy ACL zadáním velikosti dávky, najdete v [ukázce](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu `null` parametru tokenu pokračování. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu ``None`` parametru tokenu pokračování. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

---

Pokud chcete, aby byl proces dokončen bez přerušení pomocí chyb oprávnění, můžete zadat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

V tomto příkladu se používá `ContinueOnFailure` parametr, aby provádění pokračovalo i v případě, že při operaci dojde k chybě oprávnění. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Pokud chcete zobrazit příklad, který rekurzivně nastavuje seznam ACL v dávkách zadáním velikosti dávky, přečtěte si článek s odkazem [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li zajistit, že proces bude dokončen bez přerušení, nastavte `--continue-on-failure` parametr na `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Chcete-li zajistit, že proces bude dokončen bez přerušení, předejte objekt **AccessControlChangedOptions** a nastavte vlastnost **ContinueOnFailure** tohoto objektu na hodnotu ``true`` .

Tento příklad nastavuje rekurzivní položky seznamů ACL. Pokud tento kód narazí na chybu oprávnění, zaznamená toto selhání a pokračuje v provádění. Tento příklad vytiskne počet selhání konzoly. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Příklad, který v dávkách nastavuje rekurzivní seznamy ACL zadáním velikosti dávky, najdete v [ukázce](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Chcete-li zajistit, že proces bude dokončen bez přerušení, zavolejte metodu **setContinueOnFailure** objektu [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předejte hodnotu **true**.

Tento příklad nastavuje rekurzivní položky seznamů ACL. Pokud tento kód narazí na chybu oprávnění, zaznamená toto selhání a pokračuje v provádění. Tento příklad vytiskne počet selhání konzoly. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

### <a name="python"></a>[Python](#tab/python)

Chcete-li zajistit, že proces bude dokončen bez přerušení, nepředejte token pro pokračování do metody **DataLakeDirectoryClient.set_access_control_recursive** .

Tento příklad nastavuje rekurzivní položky seznamů ACL. Pokud tento kód narazí na chybu oprávnění, zaznamená toto selhání a pokračuje v provádění. Tento příklad vytiskne počet selhání konzoly. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

---

## <a name="resources"></a>Zdroje a prostředky

Tato část obsahuje odkazy na knihovny a ukázky kódu.

#### <a name="libraries"></a>Knihovny

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure CLI](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Ukázky kódů

- PowerShell: [Ukázka souboru Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: [Ukázka](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [Ukázka souboru Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Ukázka souboru Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Pokyny k osvědčeným postupům

V této části najdete některé doporučené postupy pro rekurzivní nastavení seznamů ACL. 

#### <a name="handling-runtime-errors"></a>Zpracování chyb běhového prostředí

K běhové chybě může dojít z mnoha důvodů (například při výpadku nebo potížích s připojením klienta). Pokud dojde k chybě za běhu, restartujte rekurzivní proces seznamu ACL. Seznamy řízení přístupu (ACL) lze znovu použít na položky, aniž by došlo k negativnímu dopadu. 

#### <a name="handling-permission-errors-403"></a>Zpracování chyb oprávnění (403)

Pokud při spuštění rekurzivního procesu seznamu ACL narazíte na výjimku řízení přístupu, váš [objekt zabezpečení](../../role-based-access-control/overview.md#security-principal) služby AD nemusí mít dostatečná oprávnění k použití seznamu ACL pro jednu nebo více podřízených položek v hierarchii adresáře. Pokud dojde k chybě oprávnění, proces se zastaví a poskytne se token pro pokračování. Opravte problém s oprávněním a pak použijte token pro pokračování ke zpracování zbývající datové sady. Adresáře a soubory, které již byly úspěšně zpracovány, nemusí být zpracovány znovu. Můžete také zvolit restartování rekurzivního procesu seznamu ACL. Seznamy řízení přístupu (ACL) lze znovu použít na položky, aniž by došlo k negativnímu dopadu. 

#### <a name="credentials"></a>Přihlašovací údaje 

Doporučujeme zřídit objekt zabezpečení služby Azure AD, kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového účtu úložiště nebo kontejneru. 

#### <a name="performance"></a>Výkon 

Pokud chcete snížit latenci, doporučujeme spustit rekurzivní proces seznamu ACL na virtuálním počítači Azure, který je umístěný ve stejné oblasti jako váš účet úložiště. 

#### <a name="acl-limits"></a>Omezení seznamu ACL

Maximální počet seznamů řízení přístupu, které můžete použít u adresáře nebo souboru, je 32 seznamů ACL a 32 výchozích seznamů ACL. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

## <a name="see-also"></a>Viz také

- [Řízení přístupu ve službě Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
- [Známé problémy](data-lake-storage-known-issues.md)
