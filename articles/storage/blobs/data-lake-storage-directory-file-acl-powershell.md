---
title: Použití PowerShellu pro soubory & seznamů ACL v Azure Data Lake Storage Gen2 (Preview)
description: Pomocí rutin PowerShellu můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 71f90fb361e8fc45ee2ce8672990965fca801a49
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533934"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Použití PowerShellu pro soubory & seznamů ACL v Azure Data Lake Storage Gen2 (Preview)

V tomto článku se dozvíte, jak pomocí PowerShellu vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

> [!IMPORTANT]
> Modul PowerShellu, který je vybraný v tomto článku, je momentálně ve verzi Public Preview.

[Mapování Gen1 na Gen2](#gen1-gen2-map) | [poskytnutí zpětné vazby](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Předpoklady

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> * .NET Framework 4.7.2 nebo větší je nainstalovaná. Viz [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Verze PowerShellu `5.1` nebo vyšší.

## <a name="install-powershell-modules"></a>Nainstalovat moduly PowerShellu

1. Ověřte, že verze prostředí PowerShell, která je nainstalovaná, `5.1` nebo vyšší, pomocí následujícího příkazu. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) .
    
2. Nainstalujte nejnovější modul **PowershellGet** . Pak zavřete a znovu otevřete konzolu PowerShellu.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Nainstalujte modul **AZ. Storage** Preview.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) .

## <a name="connect-to-the-account"></a>Připojit k účtu

1. Otevřete okno příkazového řádku Windows PowerShellu.

2. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

3. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, ve kterém chcete vytvářet a spravovat adresáře.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Nahraďte hodnotu zástupného symbolu `<subscription-id>` číslem ID vašeho předplatného.

4. Získejte účet úložiště.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.

   * Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.

5. Získejte kontext účtu úložiště.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete ho vytvořit pomocí rutiny `New-AzDatalakeGen2FileSystem`. 

Tento příklad vytvoří systém souborů s názvem `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář pomocí rutiny `New-AzDataLakeGen2Item`. 

Tento příklad přidá adresář s názvem `my-directory` do systému souborů.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Tento příklad přidá stejný adresář, ale také nastaví oprávnění, umask, hodnoty vlastností a hodnoty metadat. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Zobrazit vlastnosti adresáře

Tento příklad načte adresář pomocí rutiny `Get-AzDataLakeGen2Item` a pak vytiskne hodnoty vlastností do konzoly.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář pomocí rutiny `Move-AzDataLakeGen2Item`.

Tento příklad přejmenuje adresář z názvu `my-directory` na název `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

Tento příklad přesune adresář s názvem `my-directory` do podadresáře `my-directory-2` s názvem `my-subdirectory`. Tento příklad používá také umask k podadresáři.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí rutiny `Remove-AzDataLakeGen2Item`.

Tento příklad odstraní adresář s názvem `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

K odebrání souboru bez výzvy můžete použít parametr `-Force`.

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte si soubor z adresáře pomocí rutiny `Get-AzDataLakeGen2ItemContent`.

Tento příklad stáhne soubor s názvem `upload.txt` z adresáře s názvem `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznamte se s obsahem adresáře pomocí rutiny `Get-AzDataLakeGen2ChildItem`.

Tento příklad vypíše obsah adresáře s názvem `my-directory`. 

Chcete-li zobrazit obsah systému souborů, vynechejte parametr `-Path` z příkazu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Tento příklad vypíše obsah adresáře s názvem `my-directory` a obsahuje seznamy ACL v seznamu. Také používá parametr `-Recurse` k vypsání obsahu všech podadresářů.

Chcete-li zobrazit obsah systému souborů, vynechejte parametr `-Path` z příkazu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře pomocí rutiny `New-AzDataLakeGen2Item`.

Tento příklad nahraje soubor s názvem `upload.txt` do adresáře s názvem `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Tento příklad nahraje stejný soubor, ale pak nastaví oprávnění, umask, hodnoty vlastností a hodnoty metadat cílového souboru. Tento příklad také vytiskne tyto hodnoty do konzoly.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Tento příklad načte soubor pomocí rutiny `Get-AzDataLakeGen2Item` a pak vytiskne hodnoty vlastností do konzoly.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Odstranění souboru

Odstraňte soubor pomocí rutiny `Remove-AzDataLakeGen2Item`.

Tento příklad odstraní soubor s názvem `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

K odebrání souboru bez výzvy můžete použít parametr `-Force`.

## <a name="manage-access-permissions"></a>Správa přístupových oprávnění

Můžete získat, nastavit a aktualizovat přístupová oprávnění adresářů a souborů.

### <a name="get-directory-and-file-permissions"></a>Získat oprávnění pro adresáře a soubory

Získejte seznam řízení přístupu k adresáři nebo souboru pomocí rutiny `Get-AzDataLakeGen2Item`.

Tento příklad získá seznam ACL **adresáře**a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Tento příklad získá seznam řízení přístupu k **souboru** a poté vytiskne seznam řízení přístupu do konzoly.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Následující obrázek ukazuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Nastavení oprávnění adresáře a souboru

Pomocí rutiny `New-AzDataLakeGen2ItemAclObject` můžete vytvořit seznam řízení přístupu pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele. Potom pomocí rutiny `Update-AzDataLakeGen2Item` potvrďte seznam řízení přístupu.

Tento příklad nastavuje seznam řízení přístupu v **adresáři** pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Tento příklad nastavuje seznam řízení přístupu pro **soubor** pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizovat oprávnění adresáře a souboru

Seznam ACL adresáře nebo souboru získáte pomocí rutiny `Get-AzDataLakeGen2Item`. Potom pomocí rutiny `New-AzDataLakeGen2ItemAclObject` vytvořte novou položku seznamu řízení přístupu. Pomocí rutiny `Update-AzDataLakeGen2Item` použijte nový seznam řízení přístupu.

Tento příklad poskytuje uživateli oprávnění zapisovat a spustit pro adresář.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
Tento příklad poskytuje uživateli oprávnění zapisovat a spustit pro soubor.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Nastavení oprávnění pro všechny položky v systému souborů

Můžete použít `Get-AzDataLakeGen2Item` a parametr `-Recurse` spolu s rutinou `Update-AzDataLakeGen2Item` k rekurzivnímu nastavení seznamu ACL všech adresářů a souborů v systému souborů. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapování Gen1 na Gen2

Následující tabulka ukazuje, jak rutiny používané pro Data Lake Storage Gen1 mapují rutiny pro Data Lake Storage Gen2.

|Rutina Data Lake Storage Gen1| Rutina Data Lake Storage Gen2|
|--------|---------|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|
|Get-AzDataLakeStoreItem <br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission<br>Get-AzDataLakeStoreItemContent<br>New-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|
|Get-AzDataLakeStoreItemContent|New-AzDataLakeGen2Item|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item|
|Set-AzDataLakeStoreItemOwner <br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update – AzDataLakeGen2Item|

## <a name="see-also"></a>Další informace najdete v tématech

* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Použití Azure PowerShell s Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)

