---
title: Azure Data Lake Storage Gen2 PowerShell pro soubory & seznamy řízení přístupu
description: Pomocí rutin PowerShellu můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8083d42d9ce79bcf31e3875f2ff5d5f06970a7ff
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131509"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Použití PowerShellu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí PowerShellu vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

[Referenční informace](https://docs.microsoft.com/powershell/module/Az.Storage/)  |  Mapování Gen1 na [Gen2](#gen1-gen2-map)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Předpoklady

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> * .NET Framework 4.7.2 nebo větší je nainstalovaná. Viz [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Verze prostředí PowerShell `5.1` nebo vyšší.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

1. Pomocí následujícího příkazu ověřte, že verze prostředí PowerShell, která je nainstalovaná, je `5.1` nebo vyšší.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) .
    
2. Nainstalujte modul **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) .

## <a name="connect-to-the-account"></a>Připojit k účtu

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

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Možnost 2: získání autorizace pomocí klíče účtu úložiště

V rámci tohoto přístupu systém nekontroluje oprávnění Azure RBAC nebo ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
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

## <a name="manage-access-control-lists-acls"></a>Správa seznamů řízení přístupu (ACL)

Můžete získat, nastavit a aktualizovat přístupová oprávnění adresářů a souborů.

> [!NOTE]
> Pokud k autorizaci příkazů používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma  [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Získat seznam ACL

Získat seznam řízení přístupu k adresáři nebo souboru pomocí `Get-AzDataLakeGen2Item` rutiny.

Tento příklad získá seznam řízení přístupu kořenového adresáře **kontejneru** a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

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

![Získat výstup ACL pro adresář](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Nastavení seznamu ACL

Pomocí `set-AzDataLakeGen2ItemAclObject` rutiny vytvořte seznam ACL pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele. Potom pomocí `Update-AzDataLakeGen2Item` rutiny potvrďte seznam řízení přístupu.

Tento příklad nastavuje seznam řízení přístupu v kořenovém adresáři **kontejneru** pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Tento příklad nastavuje seznam řízení přístupu v **adresáři** pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Tento příklad nastavuje seznam řízení přístupu pro **soubor** pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL pro soubor](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>Přidat nebo aktualizovat položku seznamu řízení přístupu

Nejdřív Získejte seznam ACL. Potom pomocí `set-AzDataLakeGen2ItemAclObject` rutiny přidejte nebo aktualizujte položku seznamu řízení přístupu. Pomocí `Update-AzDataLakeGen2Item` rutiny potvrďte seznam řízení přístupu.

Tento příklad vytvoří nebo aktualizuje seznam řízení přístupu pro uživatele v **adresáři** .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Odebrat položku seznamu řízení přístupu

Tento příklad odebere položku z existujícího seznamu ACL.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="set-an-acl-recursively-preview"></a>Rekurzivní nastavení seznamu ACL (Preview)

Seznamy ACL můžete přidat, aktualizovat a odebrat rekurzivně na existujících podřízených položkách nadřazeného adresáře bez nutnosti provádět tyto změny jednotlivě pro každou podřízenou položku. Další informace najdete v tématu [rekurzivní nastavení seznamů řízení přístupu (ACL) pro Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

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

* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)
