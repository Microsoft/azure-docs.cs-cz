---
title: Použití .NET k nastavení seznamů ACL v Azure Data Lake Storage Gen2
description: Pomocí .NET můžete spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654094"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Použití rozhraní .NET ke správě seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak používat .NET k získání, nastavení a aktualizaci seznamů řízení přístupu adresářů a souborů.

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete ale také přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku.

[Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Ukázka rekurzivního [seznamu ACL](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  Reference k rozhraní [API](/dotnet/api/azure.storage.files.datalake)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.6.0` nebo vyšší

- Jedno z následujících oprávnění zabezpečení:

  - Zřízeným [objektem zabezpečení](../../role-based-access-control/overview.md#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.  

  - Vlastnící uživatel cílového kontejneru nebo adresáře, pro který plánujete použít nastavení seznamu ACL. Chcete-li nastavit seznamy ACL rekurzivně, zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři.
  
  - Klíč účtu úložiště

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že nainstalujete balíček NuGet pro [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

1. Otevřete příkazové okno (například prostředí Windows PowerShell).

2. Z adresáře projektu nainstalujte balíček Azure. Storage. Files. datalake Preview pomocí `dotnet add package` příkazu.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Pak přidejte tyto příkazy using do horní části souboru kódu.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , která představuje účet úložiště. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

> [!NOTE]
> Pokud k autorizaci přístupu používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Další informace o tom, jak se používají oprávnění seznamu řízení přístupu a důsledky jejich změny, najdete  [v tématu model řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu.

Tento příklad vytvoří instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) pomocí klíče účtu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Nastavení seznamů ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamů ACL](#update-acls) tohoto článku.  

Pokud se rozhodnete *nastavit* seznam řízení přístupu (ACL), musíte přidat položku pro vlastnícího uživatele, položku pro vlastnící skupinu a položku pro všechny ostatní uživatele. Další informace o vlastnícím uživateli, vlastnící skupině a všech ostatních uživatelích najdete v tématu [Uživatelé a identity](data-lake-storage-access-control.md#users-and-identities).

V této části se dozvíte, jak:

- Nastavení seznamu ACL adresáře
- Nastavení seznamu ACL souboru
- Rekurzivní nastavení seznamů ACL

### <a name="set-the-acl-of-a-directory"></a>Nastavení seznamu ACL adresáře

Získání seznamu řízení přístupu (ACL) adresáře voláním metody [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) a nastavením seznamu ACL voláním metody [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Tento příklad načte a nastaví seznam řízení přístupu k adresáři s názvem `my-directory` . Řetězec přiřadí `user::rwx,group::r-x,other::rw-` vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Můžete také získat a nastavit seznam ACL kořenového adresáře kontejneru. Chcete-li získat kořenový adresář, předejte prázdný řetězec ( `""` ) do metody [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="set-the-acl-of-a-file"></a>Nastavení seznamu ACL souboru

Získání seznamu řízení přístupu (ACL) souboru zavoláním metody [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) a nastavením seznamu ACL voláním metody [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

Tento příklad získá a nastaví seznam řízení přístupu k souboru s názvem `my-file.txt` . Řetězec přiřadí `user::rwx,group::r-x,other::rw-` vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Rekurzivní nastavení seznamů ACL

Nastavení seznamů ACL rekurzivně voláním metody **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL.

Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), můžete vlastnost [PathAccessControlItem. DefaultScope platná](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) [nastavit na](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) **hodnotu true**. 

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má nastavit výchozí seznam řízení přístupu. Tento parametr se používá v konstruktoru třídy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Položky seznamu řízení přístupu poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině jenom oprávnění ke čtení a spouštění a všem ostatním uživatelům se neudělí žádný přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Příklad, který v dávkách nastavuje rekurzivní seznamy ACL zadáním velikosti dávky, najdete v [ukázce](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

## <a name="update-acls"></a>Aktualizace seznamů ACL

Když *aktualizujete* seznam řízení přístupu (ACL), místo nahrazení seznamu ACL ho upravte. Do seznamu ACL můžete například přidat nový objekt zabezpečení, aniž by to ovlivnilo jiné objekty zabezpečení uvedené v seznamu ACL.  Chcete-li nahradit seznam ACL místo aktualizace, přečtěte si část [set ACL](#set-acls) v tomto článku.

V této části se dozvíte, jak:

- Aktualizace seznamu ACL
- Rekurzivní aktualizace seznamů ACL

### <a name="update-an-acl"></a>Aktualizace seznamu ACL

Nejprve získejte seznam ACL adresáře voláním metody [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Zkopírujte seznam položek seznamu ACL do nového [seznamu](/dotnet/api/system.collections.generic.list-1) objektů [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Pak vyhledejte položku, kterou chcete aktualizovat, a nahraďte ji v seznamu. Nastavte seznam ACL voláním metody [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Tento příklad aktualizuje kořenový seznam řízení přístupu kontejneru tím, že nahrazuje položku seznamu řízení přístupu pro všechny ostatní uživatele. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Rekurzivní aktualizace seznamů ACL

Chcete-li seznam ACL aktualizovat rekurzivně, vytvořte nový objekt ACL s položkou seznamu ACL, kterou chcete aktualizovat, a pak tento objekt použijte v operaci aktualizovat seznam ACL. Nezískejte existující seznam ACL, stačí poskytnout položky seznamu ACL, které se mají aktualizovat.

Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL.

Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), můžete vlastnost [PathAccessControlItem. DefaultScope platná](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) [nastavit na](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) **hodnotu true**.

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má aktualizovat výchozí seznam ACL. Tento parametr se používá v konstruktoru třídy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Pokud chcete zobrazit příklad, který provede rekurzivní aktualizaci seznamů ACL v dávkách zadáním velikosti dávky, přečtěte si [ukázku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

## <a name="remove-acl-entries"></a>Odebrat položky seznamu řízení přístupu

Můžete odebrat jednu nebo více položek seznamu řízení přístupu. V této části se dozvíte, jak:

- Odebrat položku seznamu řízení přístupu
- Rekurzivní odebrání položek seznamu ACL

### <a name="remove-an-acl-entry"></a>Odebrat položku seznamu řízení přístupu

Nejprve získejte seznam ACL adresáře voláním metody [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Zkopírujte seznam položek seznamu ACL do nového [seznamu](/dotnet/api/system.collections.generic.list-1) objektů [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Pak vyhledejte položku, kterou chcete odebrat, a zavolejte metodu [Remove](/dotnet/api/system.collections.ilist.remove) kolekce. Nastavte aktualizovaný seznam řízení přístupu voláním metody [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Tento příklad aktualizuje kořenový seznam řízení přístupu kontejneru tím, že nahrazuje položku seznamu řízení přístupu pro všechny ostatní uživatele. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Rekurzivní odebrání položek seznamu ACL

Chcete-li odebrat položky seznamu ACL rekurzivně, vytvořte nový objekt ACL pro položku seznamu ACL, která se má odebrat, a pak tento objekt použijte v operaci odebrat seznam ACL. Nezískávat existující seznam ACL, stačí zadat položky seznamu ACL, které se mají odebrat. 

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Předá tuto metodu [seznam](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Každý [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definuje položku seznamu ACL. 

Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), můžete vlastnost [PathAccessControlItem. DefaultScope platná](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) [nastavit na](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) **hodnotu true**. 

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda má být položka odebrána z výchozího seznamu ACL. Tento parametr se používá v konstruktoru třídy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Pokud chcete zobrazit příklad, který v dávkách odebírá rekurzivní seznamy ACL zadáním velikosti dávky, přečtěte si [ukázku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

## <a name="recover-from-failures"></a>Obnovit po selhání

Při rekurzivním upravování seznamů ACL může dojít k chybám za běhu nebo oprávnění. V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu `null` parametru tokenu pokračování. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Příklad, který v dávkách nastavuje rekurzivní seznamy ACL zadáním velikosti dávky, najdete v [ukázce](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

Pokud chcete, aby byl proces dokončen bez přerušení pomocí chyb oprávnění, můžete zadat.

Chcete-li zajistit, že proces bude dokončen bez přerušení, předejte objekt **AccessControlChangedOptions** a nastavte vlastnost **ContinueOnFailure** tohoto objektu na hodnotu ``true`` .

Tento příklad nastavuje rekurzivní položky seznamů ACL. Pokud tento kód narazí na chybu oprávnění, zaznamená toto selhání a pokračuje v provádění. Tento příklad vytiskne počet selhání konzoly.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Příklad, který v dávkách nastavuje rekurzivní seznamy ACL zadáním velikosti dávky, najdete v [ukázce](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Viz také

- [Referenční dokumentace k rozhraní API](/dotnet/api/azure.storage.files.datalake)
- [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-net/issues)
- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)