---
title: Použití rozhraní příkazového řádku Azure k nastavení seznamů ACL v Azure Data Lake Storage Gen2
description: Pomocí Azure CLI můžete spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají hierarchický obor názvů.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563160"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Použití rozhraní příkazového řádku Azure ke správě seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak používat rozhraní příkazového [řádku Azure Command-Line](/cli/azure/) k získání, nastavení a aktualizaci seznamů řízení přístupu adresářů a souborů.

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete ale také přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku.

[Referenční informace](/cli/azure/storage/fs/access)  |  [Ukázky](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.14.0` nebo vyšší

- Jedno z následujících oprávnění zabezpečení:

  - Zřízený Azure Active Directory (Azure AD) [instanční objekt zabezpečení](../../role-based-access-control/overview.md#security-principal) , kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.  

  - Vlastnící uživatel cílového kontejneru nebo adresáře, pro který plánujete použít nastavení seznamu ACL. Chcete-li nastavit seznamy ACL rekurzivně, zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři.
  
  - Klíč účtu úložiště

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Ujistěte se, že máte nainstalovanou správnou verzi rozhraní příkazového řádku Azure CLI.

1. Otevřete [Azure Cloud Shell](../../cloud-shell/overview.md)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](/cli/azure/install-azure-cli) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pomocí následujícího příkazu ověřte, že verze rozhraní příkazového řádku Azure, která je nainstalovaná, je `2.14.0` nebo vyšší.

   ```azurecli
    az --version
   ```

   Pokud je vaše verze rozhraní příkazového řádku Azure nižší než `2.14.0` , nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Připojit k účtu

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
> Příklad uvedený v tomto článku ukazuje autorizaci Azure Active Directory (Azure AD). Další informace o metodách autorizace najdete v tématu [autorizace přístupu k objektům blob nebo Queue data z Azure pomocí Azure CLI](./authorize-data-operations-cli.md).

## <a name="get-acls"></a>Získat seznamy ACL

Získat seznam řízení přístupu k **adresáři** pomocí `az storage fs access show` příkazu.

Tento příklad získá seznam ACL adresáře a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Pomocí příkazu Získejte přístupová oprávnění k **souboru** `az storage fs access show` . 

Tento příklad získá seznam řízení přístupu k souboru a poté vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Následující obrázek ukazuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Nastavení seznamů ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamů ACL](#update-acls) tohoto článku.  

Pokud se rozhodnete *nastavit* seznam řízení přístupu (ACL), musíte přidat položku pro vlastnícího uživatele, položku pro vlastnící skupinu a položku pro všechny ostatní uživatele. Další informace o vlastnícím uživateli, vlastnící skupině a všech ostatních uživatelích najdete v tématu [Uživatelé a identity](data-lake-storage-access-control.md#users-and-identities).

V této části se dozvíte, jak:

- Nastavení seznamu ACL
- Rekurzivní nastavení seznamů ACL

### <a name="set-an-acl"></a>Nastavení seznamu ACL

Pomocí `az storage fs access set` příkazu nastavte seznam řízení přístupu (ACL) **adresáře**. 

Tento příklad nastavuje seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Tento příklad nastaví *výchozí* seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access set`K nastavení seznamu ACL **souboru** použijte příkaz. 

Tento příklad nastavuje seznam řízení přístupu pro soubor pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Chcete-li nastavit seznam ACL konkrétní skupiny nebo uživatele, použijte odpovídající ID objektu. Příkladem je `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` nebo `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Rekurzivní nastavení seznamů ACL

Rekurzivní nastavení seznamů ACL pomocí příkazu [AZ Storage FS Access set-rerecursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu, přidejte `default:` k jednotlivým položkám předponu. Příkladem je `default:user::rwx` nebo `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Aktualizace seznamů ACL

Když *aktualizujete* seznam řízení přístupu (ACL), místo nahrazení seznamu ACL ho upravte. Do seznamu ACL můžete například přidat nový objekt zabezpečení, aniž by to ovlivnilo jiné objekty zabezpečení uvedené v seznamu ACL.  Chcete-li nahradit seznam ACL místo aktualizace, přečtěte si část [set ACL](#set-acls) v tomto článku.

Chcete-li aktualizovat seznam řízení přístupu, vytvořte nový objekt ACL s položkou seznamu ACL, kterou chcete aktualizovat, a pak tento objekt použijte v operaci aktualizovat seznam ACL. Nezískejte existující seznam ACL, stačí poskytnout položky seznamu ACL, které se mají aktualizovat.

V této části se dozvíte, jak:

- Aktualizace seznamu ACL
- Rekurzivní aktualizace seznamů ACL

### <a name="update-an-acl"></a>Aktualizace seznamu ACL

Dalším způsobem, jak toto oprávnění nastavit, je použít `az storage fs access set` příkaz. 

Aktualizujte seznam řízení přístupu (ACL) adresáře nebo souboru nastavením `-permissions` parametru na krátký tvar seznamu ACL.

Tento příklad aktualizuje seznam řízení přístupu k **adresáři**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Tento příklad aktualizuje seznam řízení přístupu k **souboru**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Chcete-li aktualizovat seznam řízení přístupu konkrétní skupiny nebo uživatele, použijte jejich odpovídající ID objektu. Příkladem je `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` nebo `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Vlastnícího uživatele a skupinu adresáře nebo souboru můžete také aktualizovat nastavením `--owner` `group` parametrů nebo na ID entity nebo hlavní název uživatele (UPN) uživatele.

Tento příklad změní vlastníka adresáře.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Tento příklad změní vlastníka souboru. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Rekurzivní aktualizace seznamů ACL

Rekurzivní aktualizace seznamů ACL pomocí příkazu  [AZ Storage FS Access Update – rekurzivní](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) .

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu, přidejte `default:` k jednotlivým položkám předponu. Například, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Rekurzivní odebrání položek seznamu ACL

Jednu nebo více položek seznamu ACL můžete rekurzivně odebrat. Chcete-li odebrat položku seznamu řízení přístupu (ACL), vytvořte nový objekt ACL pro položku seznamu ACL, která se má odebrat, a pak tento objekt použijte v operaci odebrat seznam ACL. Nezískávat existující seznam ACL, stačí zadat položky seznamu ACL, které se mají odebrat.

Pomocí příkazu [AZ Storage FS Access Remove-rekurzivní](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) odeberte položky seznamu ACL.

Tento příklad odebere položku seznamu řízení přístupu z kořenového adresáře kontejneru.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Chcete-li odebrat **výchozí** položku seznamu řízení přístupu (ACL), přidejte `default:` k jednotlivým položkám předponu. Například, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Obnovit po selhání

Při rekurzivním upravování seznamů ACL může dojít k chybám za běhu nebo oprávnění. V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

V případě selhání můžete vrátit token pokračování nastavením `--continue-on-failure` parametru na `false` . Po vyřešení chyb můžete proces obnovit z bodu selhání tak, že znovu spustíte příkaz a pak nastavíte `--continuation` parametr na token pro pokračování.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Pokud chcete, aby byl proces dokončen bez přerušení pomocí chyb oprávnění, můžete zadat.

Chcete-li zajistit, že proces bude dokončen bez přerušení, nastavte `--continue-on-failure` parametr na `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Viz také

- [ukázky](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-cli-extensions/issues)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)