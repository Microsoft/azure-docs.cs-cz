---
title: Použití rozhraní příkazového řádku Azure ke správě dat (Azure Data Lake Storage Gen2)
description: Pomocí Azure CLI můžete spravovat adresáře a soubory v účtech úložiště, které mají hierarchický obor názvů.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650350"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Použití rozhraní příkazového řádku Azure ke správě adresářů a souborů v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak používat rozhraní příkazového [řádku Azure Command-Line](/cli/azure/) k vytváření a správě adresářů a souborů v účtech úložiště, které mají hierarchický obor názvů.

Další informace o tom, jak získat, nastavit a aktualizovat seznamy řízení přístupu (ACL) adresářů a souborů, najdete v tématu použití rozhraní příkazového [řádku Azure ke správě seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Ukázky](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.6.0` nebo vyšší

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Ujistěte se, že máte nainstalovanou správnou verzi rozhraní příkazového řádku Azure CLI.

1. Otevřete [Azure Cloud Shell](../../cloud-shell/overview.md)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](/cli/azure/install-azure-cli) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pomocí následujícího příkazu ověřte, že verze rozhraní příkazového řádku Azure, která je nainstalovaná, je `2.6.0` nebo vyšší.

   ```azurecli
    az --version
   ```

   Pokud je vaše verze rozhraní příkazového řádku Azure nižší než `2.6.0` , nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

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

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete ho vytvořit pomocí `az storage fs create` příkazu. 

Tento příklad vytvoří kontejner s názvem `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Zobrazit vlastnosti kontejneru

Vlastnosti kontejneru můžete vytisknout do konzoly pomocí `az storage fs show` příkazu.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Výpis obsahu kontejneru

Seznamte se s obsahem adresáře pomocí `az storage fs file list` příkazu.

V tomto příkladu je uveden seznam obsahu kontejneru s názvem `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Odstranění kontejneru

Odstraňte kontejner pomocí `az storage fs delete` příkazu.

Tento příklad odstraní kontejner s názvem `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář pomocí `az storage fs directory create` příkazu. 

Tento příklad přidá adresář s názvem `my-directory` do kontejneru s názvem `my-file-system` , který je umístěn v účtu s názvem `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Zobrazit vlastnosti adresáře

Vlastnosti adresáře lze vytisknout do konzoly pomocí `az storage fs directory show` příkazu.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář pomocí `az storage fs directory move` příkazu.

Tento příklad přejmenuje adresář z názvu `my-directory` na název `my-new-directory` ve stejném kontejneru.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Tento příklad přesune adresář do kontejneru s názvem `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí `az storage fs directory delete` příkazu.

Tento příklad odstraní adresář s názvem `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Zjistit, jestli adresář existuje

Zjistěte, zda v kontejneru existuje konkrétní adresář pomocí `az storage fs directory exists` příkazu.

Tento příklad ukáže, zda adresář s názvem `my-directory` existuje v `my-file-system` kontejneru. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte si soubor z adresáře pomocí `az storage fs file download` příkazu.

Tento příklad stáhne soubor s názvem `upload.txt` z adresáře s názvem `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznamte se s obsahem adresáře pomocí `az storage fs file list` příkazu.

Tento příklad vypíše obsah adresáře s názvem `my-directory` , který se nachází v `my-file-system` kontejneru účtu úložiště s názvem `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře pomocí `az storage fs directory upload` příkazu.

Tento příklad nahraje soubor s názvem `upload.txt` do adresáře s názvem `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Vlastnosti souboru můžete vytisknout do konzoly pomocí `az storage fs file show` příkazu.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Přejmenování nebo přesunutí souboru

Přejmenujte nebo přesuňte soubor pomocí `az storage fs file move` příkazu.

Tento příklad přejmenuje soubor z názvu `my-file.txt` na název `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Odstranění souboru

Odstraňte soubor pomocí `az storage fs file delete` příkazu.

Tento příklad odstraní soubor s názvem `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Viz také

- [ukázky](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-cli-extensions/issues)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Použití rozhraní příkazového řádku Azure ke správě seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)