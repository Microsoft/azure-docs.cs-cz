---
title: Azure Data Lake Storage Gen2 PowerShell pro soubory & Seznamy AC (preview)
description: Pomocí rutin prostředí PowerShell můžete spravovat adresáře a seznamy řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 58548c5cb1aa6aba6dda09d5d420b36bb8154726
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656401"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Použití PowerShellu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 (preview)

Tento článek ukazuje, jak pomocí prostředí PowerShell vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

> [!IMPORTANT]
> Modul Prostředí PowerShell, který je součástí tohoto článku, je aktuálně ve verzi Public Preview.

[Mapování Gen1 až Gen2](#gen1-gen2-map) | [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.
> * Rozhraní .NET Framework je nainstalováno 4.7.2 nebo vyšší. Viz [Stažení rozhraní .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Verze `5.1` PowerShellu nebo vyšší.

## <a name="install-powershell-modules"></a>Instalace modulů PowerShellu

1. Pomocí následujícího příkazu ověřte, zda je `5.1` nainstalovaná verze prostředí PowerShell nebo vyšší.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Pokud chcete upgradovat verzi PowerShellu, přečtěte si [část Upgrade existujícího Prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Nainstalujte nejnovější modul **PowershellGet.** Potom zavřete a znovu otevřete konzolu PowerShell.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. Nainstalujte modul **náhledu Az.Storage.**

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   Další informace o instalaci modulů PowerShellu najdete [v tématu Instalace modulu Azure PowerShellu.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Připojení k účtu

Otevřete příkazové okno Prostředí Windows PowerShell a pak `Connect-AzAccount` se přihlaste k předplatnému Azure pomocí příkazu a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte aktivní předplatné na předplatné účtu úložiště, ve kterém chcete vytvořit a spravovat adresáře. V tomto příkladu `<subscription-id>` nahraďte zástupnou hodnotu ID předplatného.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Dále zvolte, jak chcete, aby vaše příkazy získaly autorizaci k účtu úložiště. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Možnost 1: Získání autorizace pomocí služby Azure Active Directory (AD)

S tímto přístupem systém zajišťuje, že váš uživatelský účet má příslušná přiřazení řízení přístupu (RBAC) na základě rolí a oprávnění Seznamu řízení přístupu. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Možnost 2: Získání autorizace pomocí klíče účtu úložiště

S tímto přístupem systém nekontroluje Oprávnění RBAC nebo ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete vytvořit pomocí rutiny. `New-AzDatalakeGen2FileSystem` 

Tento příklad vytvoří systém `my-file-system`souborů s názvem .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na `New-AzDataLakeGen2Item` adresář pomocí rutiny. 

Tento příklad přidá `my-directory` adresář s názvem do systému souborů.

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

Tento příklad získá adresář `Get-AzDataLakeGen2Item` pomocí rutiny a potom vytiskne hodnoty vlastností do konzoly.

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

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte `Move-AzDataLakeGen2Item` adresář pomocí rutiny.

Tento příklad přejmenuje adresář `my-directory` z názvu `my-new-directory`na název .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Parametr `-Force` použijte, pokud chcete přepsat bez výzev.

Tento příklad přesune `my-directory` adresář s názvem `my-directory-2` `my-subdirectory`do podadresáře s názvem . Tento příklad také použije masku u podadresáře.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí `Remove-AzDataLakeGen2Item` rutiny.

Tento příklad odstraní adresář `my-directory`s názvem . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

`-Force` Parametr můžete použít k odebrání souboru bez výzvy.

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte soubor z adresáře `Get-AzDataLakeGen2ItemContent` pomocí rutiny.

Tento příklad stáhne `upload.txt` soubor pojmenovaný `my-directory`z adresáře s názvem . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznam obsahu adresáře pomocí `Get-AzDataLakeGen2ChildItem` rutiny. Volitelný parametr `-OutputUserPrincipalName` můžete použít k získání názvu (namísto ID objektu) uživatelů.

V tomto příkladu je `my-directory`uveden obsah adresáře s názvem .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

V následujícím příkladu `Permissions` `Group`jsou `Owner` uvedeny `ACL`vlastnosti , , a vlastnosti každé položky v adresáři. Parametr `-FetchProperty` je vyžadován pro získání `ACL` hodnot pro vlastnost. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Chcete-li zobrazit obsah systému souborů, `-Path` vyneche parametr z příkazu.

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře `New-AzDataLakeGen2Item` pomocí rutiny.

Tento příklad odešle `upload.txt` soubor s `my-directory`názvem do adresáře s názvem . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Tento příklad nahraje stejný soubor, ale potom nastaví oprávnění, umask, hodnoty vlastností a metadata hodnoty cílového souboru. Tento příklad také vytiskne tyto hodnoty do konzoly.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Tento příklad získá soubor `Get-AzDataLakeGen2Item` pomocí rutiny a potom vytiskne hodnoty vlastností do konzoly.

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

Tento příklad odstraní soubor `upload.txt`s názvem . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

`-Force` Parametr můžete použít k odebrání souboru bez výzvy.

## <a name="manage-access-permissions"></a>Správa přístupových oprávnění

Můžete získat, nastavit a aktualizovat přístupová oprávnění k souborovým systémům, adresářům a souborům.

> [!NOTE]
> Pokud používáte Azure Active Directory (Azure AD) k autorizaci příkazů, ujistěte se, že váš objekt zabezpečení byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-permissions"></a>Získat oprávnění

Získejte seznam ACL adresáře nebo `Get-AzDataLakeGen2Item`souboru pomocí rutiny.


Tento příklad získá acl **systému souborů** a potom vytiskne acl do konzoly.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Tento příklad získá seznam ACL **adresáře**a potom vytiskne seznam ACL do konzoly.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Tento příklad získá acl **souboru** a potom vytiskne acl do konzoly.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Následující obrázek znázorňuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-or-update-permissions"></a>Nastavení nebo aktualizace oprávnění

Pomocí `set-AzDataLakeGen2ItemAclObject` rutiny vytvořte acl pro vlastnící uživatele, vlastnící skupinu nebo jiné uživatele. Potom použijte `Update-AzDataLakeGen2Item` rutinu k potvrzení acl.

Tento příklad nastaví acl v **systému souborů** pro vlastnící uživatele, vlastnící skupiny nebo jiných uživatelů a potom vytiskne acl do konzoly.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Tento příklad nastaví seznam ACL v **adresáři** pro vlastnící uživatele, vlastnící skupinu nebo jiné uživatele a potom vytiskne seznam ACL do konzoly.

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
Tento příklad nastaví acl na **soubor** pro vlastnící uživatele, vlastnící skupiny nebo jiných uživatelů a potom vytiskne acl do konzoly.

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

Následující obrázek znázorňuje výstup po nastavení acl souboru.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

V tomto příkladu vlastnící uživatel a vlastnící skupina mají pouze oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-permissions-on-all-items-in-a-file-system"></a>Nastavení oprávnění pro všechny položky v systému souborů

Parametr `Get-AzDataLakeGen2Item` a společně s rutinou `Update-AzDataLakeGen2Item` můžete rekurzivně nastavit seznam ACL všech adresářů a souborů v systému souborů. `-Recurse` 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapování Gen1 až Gen2

Následující tabulka ukazuje, jak rutiny používané pro data lake storage Gen1 map na rutiny pro úložiště datových jezer Gen2.

|Rutina úložiště datového jezera Gen1| Rutina Úložiště datového jezera Gen2| Poznámky |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Ve výchozím nastavení rutina Get-AzDataLakeGen2ChildItem uvádí pouze podřízené položky první úrovně. Parametr -Recurse rekurzivně uvádí podřízené položky. |
|Get-AzDataLakeSklad<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreMajitel položky<br>Get-AzDataLakeStorePoložkyPermission|Get-AzDataLakeGen2Položka|Výstupní položky rutiny Get-AzDataLakeGen2Item mají tyto vlastnosti: Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2Obsah|Rutina Get-AzDataLakeGen2FileContent stahuje obsah souboru do místního souboru.|
|Move-AzDataLakeStoreItem|Přesunout-AzDataLakeGen2Item||
|Nová-AzDataLakeStoreItem|Nový-AzDataLakeGen2Item|Tato rutina nahraje nový obsah souboru z místního souboru.|
|Odstranit-AzDataLakeStoreItem|Odstranit-AzDataLakeGen2Item||
|Set-AzDataLakeStoreStoreMajitel položky<br>Set-AzDataLakeStoreItemPermission<br>Sada-AzDataLakeStoreItemAcl|Aktualizace-AzDataLakeGen2Item|Rutina Update-AzDataLakeGen2Item aktualizuje pouze jednu položku a nikoli rekurzivně. Pokud chcete aktualizovat rekurzivně, seznam položek pomocí Rutina Get-AzDataLakeStoreChildItem, potom pipeline do rutiny Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Položka|Rutina Get-AzDataLakeGen2Item ohlásí chybu, pokud položka neexistuje.|



## <a name="see-also"></a>Viz také

* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Používání Azure PowerShellu s Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Rutiny prostředí PowerShell úložiště](/powershell/module/az.storage).

