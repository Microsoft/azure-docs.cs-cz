---
title: Použití PowerShellu ke správě seznamů ACL v Azure Data Lake Storage Gen2
description: Pomocí rutin PowerShellu můžete spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104702537"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Použití PowerShellu ke správě seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí PowerShellu získat, nastavit a aktualizovat seznamy řízení přístupu adresářů a souborů. 

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete ale také přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku. 

[Referenční informace](/powershell/module/Az.Storage/)  |  [Rekurzivní ukázky](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  seznamů ACL [Sdělte nám svůj názor](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.6.0` nebo vyšší

- Jedno z následujících oprávnění zabezpečení:

  - Zřízeným [objektem zabezpečení](../../role-based-access-control/overview.md#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.  

  - Vlastnící uživatel cílového kontejneru nebo adresáře, pro který plánujete použít nastavení seznamu ACL. Chcete-li nastavit seznamy ACL rekurzivně, zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři.
  
  - Klíč účtu úložiště

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Možnost 1: získání autorizace pomocí Azure Active Directory (AD)

> [!NOTE]
> Pokud k autorizaci přístupu používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Další informace o tom, jak se používají oprávnění seznamu řízení přístupu a důsledky jejich změny, najdete  [v tématu model řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

## <a name="get-acls"></a>Získat seznamy ACL

Získat seznam řízení přístupu k adresáři nebo souboru pomocí `Get-AzDataLakeGen2Item` rutiny.

Tento příklad získá seznam řízení přístupu kořenového adresáře **kontejneru** a pak vytiskne seznam řízení přístupu do konzoly.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Tento příklad získá seznam ACL **adresáře** a pak vytiskne seznam řízení přístupu do konzoly.

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

## <a name="set-acls"></a>Nastavení seznamů ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamů ACL](#update-acls) tohoto článku.  

Pokud se rozhodnete *nastavit* seznam řízení přístupu (ACL), musíte přidat položku pro vlastnícího uživatele, položku pro vlastnící skupinu a položku pro všechny ostatní uživatele. Další informace o vlastnícím uživateli, vlastnící skupině a všech ostatních uživatelích najdete v tématu [Uživatelé a identity](data-lake-storage-access-control.md#users-and-identities).

V této části se dozvíte, jak:

- Nastavení seznamu ACL
- Rekurzivní nastavení seznamů ACL

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

> [!NOTE]
> Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), použijte při spuštění příkazu **set-AzDataLakeGen2ItemAclObject** parametr **-DefaultScope platná** . Příklad: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

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

> [!NOTE]
> Chcete-li nastavit seznam ACL konkrétní skupiny nebo uživatele, použijte odpovídající ID objektu. Příkladem je `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` nebo `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL pro soubor](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Rekurzivní nastavení seznamů ACL

Nastavení seznamů ACL rekurzivně pomocí rutiny **set-AzDataLakeGen2AclRecursive** .

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

## <a name="update-acls"></a>Aktualizace seznamů ACL

Když *aktualizujete* seznam řízení přístupu (ACL), místo nahrazení seznamu ACL ho upravte. Do seznamu ACL můžete například přidat nový objekt zabezpečení, aniž by to ovlivnilo jiné objekty zabezpečení uvedené v seznamu ACL.  Chcete-li nahradit seznam ACL místo aktualizace, přečtěte si část [set ACL](#set-acls) v tomto článku.

Chcete-li aktualizovat seznam řízení přístupu, vytvořte nový objekt ACL s položkou seznamu ACL, kterou chcete aktualizovat, a pak tento objekt použijte v operaci aktualizovat seznam ACL. Nezískejte existující seznam ACL, stačí poskytnout položky seznamu ACL, které se mají aktualizovat.

V této části se dozvíte, jak:

- Aktualizace seznamu ACL
- Rekurzivní aktualizace seznamů ACL

### <a name="update-an-acl"></a>Aktualizace seznamu ACL

Nejdřív Získejte seznam ACL. Potom pomocí `set-AzDataLakeGen2ItemAclObject` rutiny přidejte nebo aktualizujte položku seznamu řízení přístupu. Pomocí `Update-AzDataLakeGen2Item` rutiny potvrďte seznam řízení přístupu.

Tento příklad vytvoří nebo aktualizuje seznam řízení přístupu pro uživatele v **adresáři** .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), použijte při spuštění příkazu **set-AzDataLakeGen2ItemAclObject** parametr **-DefaultScope platná** . Příklad: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Rekurzivní aktualizace seznamů ACL

Aktualizujte seznamy ACL rekurzivně pomocí rutiny  **Update-AzDataLakeGen2AclRecursive** .

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Chcete-li nastavit seznam ACL konkrétní skupiny nebo uživatele, použijte odpovídající ID objektu. Příkladem je `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` nebo `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Pokud chcete zobrazit příklad, který provede rekurzivní aktualizaci seznamů ACL v dávkách zadáním velikosti dávky, přečtěte si článek Referenční dokumentace [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

## <a name="remove-acl-entries"></a>Odebrat položky seznamu řízení přístupu

V této části se dozvíte, jak:

- Odebrat položku seznamu řízení přístupu
- Rekurzivní odebrání položek seznamu ACL

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

### <a name="remove-acl-entries-recursively"></a>Rekurzivní odebrání položek seznamu ACL

Jednu nebo více položek seznamu ACL můžete rekurzivně odebrat. Chcete-li odebrat položku seznamu řízení přístupu (ACL), vytvořte nový objekt ACL pro položku seznamu ACL, která se má odebrat, a pak tento objekt použijte v operaci odebrat seznam ACL. Nezískávat existující seznam ACL, stačí zadat položky seznamu ACL, které se mají odebrat.

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

## <a name="recover-from-failures"></a>Obnovit po selhání

Při rekurzivním upravování seznamů ACL může dojít k chybám za běhu nebo oprávnění. 

V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

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

Pokud chcete, aby byl proces dokončen bez přerušení pomocí chyb oprávnění, můžete zadat.

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

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Viz také

- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)
- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
