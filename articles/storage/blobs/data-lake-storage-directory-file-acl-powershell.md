---
title: 'Použití PowerShellu ke správě dat: Azure Data Lake Storage Gen2'
description: Pomocí rutin PowerShellu můžete spravovat adresáře a soubory v účtech úložiště, které mají povolený hierarchický obor názvů.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe7ad949d7301a035eb17d2b4d8d678dfb2e0546
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650191"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Použití PowerShellu ke správě adresářů a souborů v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí PowerShellu vytvářet a spravovat adresáře a soubory v účtech úložiště, které mají hierarchický obor názvů.

Další informace o tom, jak získat, nastavit a aktualizovat seznamy řízení přístupu (ACL) adresářů a souborů, najdete v tématu [použití PowerShellu ke správě seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Referenční informace](/powershell/module/Az.Storage/)  |  Mapování Gen1 na [Gen2](#gen1-gen2-map)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- .NET Framework 4.7.2 nebo větší je nainstalovaná. Viz [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- Verze prostředí PowerShell `5.1` nebo vyšší.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

1. Pomocí následujícího příkazu ověřte, že verze prostředí PowerShell, která je nainstalovaná, je `5.1` nebo vyšší.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) .

2. Nainstalujte modul **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) .

## <a name="connect-to-the-account"></a>Připojit k účtu

Vyberte způsob, jakým mají příkazy získat autorizaci k účtu úložiště. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Možnost 1: získání autorizace pomocí Azure Active Directory (Azure AD)

V rámci tohoto přístupu systém zajistí, že váš uživatelský účet má odpovídající přiřazení Azure na základě role (Azure RBAC) a oprávnění ACL.

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

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Možnost 2: získání autorizace pomocí klíče účtu úložiště

V rámci tohoto přístupu systém nekontroluje oprávnění Azure RBAC nebo ACL. Získejte kontext účtu úložiště pomocí klíče účtu.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete ho vytvořit pomocí `New-AzStorageContainer` rutiny. 

Tento příklad vytvoří kontejner s názvem `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář pomocí `New-AzDataLakeGen2Item` rutiny. 

Tento příklad přidá adresář s názvem `my-directory` do kontejneru.

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

Tento příklad načte adresář pomocí `Get-AzDataLakeGen2Item` rutiny a pak vytiskne hodnoty vlastností do konzoly.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Chcete-li získat kořenový adresář kontejneru, vynechejte `-Path` parametr.

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář pomocí `Move-AzDataLakeGen2Item` rutiny.

V tomto příkladu se přejmenuje adresář z názvu `my-directory` na název `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Použijte `-Force` parametr, pokud chcete přepsat bez výzvy.

Tento příklad přesune adresář s názvem `my-directory` do podadresáře `my-directory-2` s názvem `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí `Remove-AzDataLakeGen2Item` rutiny.

Tento příklad odstraní adresář s názvem `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Pomocí `-Force` parametru můžete soubor odebrat bez výzvy.

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte si soubor z adresáře pomocí `Get-AzDataLakeGen2ItemContent` rutiny.

Tento příklad stáhne soubor s názvem `upload.txt` z adresáře s názvem `my-directory` .

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznamte se s obsahem adresáře pomocí `Get-AzDataLakeGen2ChildItem` rutiny. `-OutputUserPrincipalName`K získání názvu (místo ID objektu) uživatelů můžete použít volitelný parametr.

Tento příklad vypíše obsah adresáře s názvem `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Následující příklad zobrazí seznam `ACL` vlastností, `Permissions` , a `Group` `Owner` jednotlivých položek v adresáři. Pro `-FetchProperty` získání hodnot vlastnosti je vyžadován parametr `ACL` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Chcete-li zobrazit obsah kořenového adresáře kontejneru, vynechejte `-Path` parametr.

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře pomocí `New-AzDataLakeGen2Item` rutiny.

Tento příklad nahraje soubor s názvem `upload.txt` do adresáře s názvem `my-directory` . 

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
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Pokud chcete nahrát soubor do kořenového adresáře kontejneru, vynechejte `-Path` parametr.

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Tento příklad načte soubor pomocí `Get-AzDataLakeGen2Item` rutiny a pak vytiskne hodnoty vlastností do konzoly.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Odstranění souboru

Odstraňte soubor pomocí `Remove-AzDataLakeGen2Item` rutiny.

Tento příklad odstraní soubor s názvem `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Pomocí `-Force` parametru můžete soubor odebrat bez výzvy.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Mapování Gen1 na Gen2

Následující tabulka ukazuje, jak rutiny používané pro Data Lake Storage Gen1 mapují rutiny pro Data Lake Storage Gen2.

|Rutina Data Lake Storage Gen1| Rutina Data Lake Storage Gen2| Poznámky |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Ve výchozím nastavení rutina Get-AzDataLakeGen2ChildItem uvádí pouze podřízené položky první úrovně. Parametr-rekurze vypisuje rekurzivně podřízené položky. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Výstupní položky rutiny Get-AzDataLakeGen2Item mají tyto vlastnosti: seznam ACL, vlastník, skupina, oprávnění.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Rutina Get-AzDataLakeGen2FileContent stahuje obsah souboru do místního souboru.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Tato rutina nahraje nový obsah souboru z místního souboru.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Rutina Update-AzDataLakeGen2Item aktualizuje pouze jednu položku, a ne rekurzivně. Pokud chcete rekurzivně aktualizovat, vypište položky pomocí rutiny Get-AzDataLakeStoreChildItem a potom kanál k rutině Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Rutina Get-AzDataLakeGen2Item ohlásí chybu, pokud položka neexistuje.|

## <a name="see-also"></a>Viz také

- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)
