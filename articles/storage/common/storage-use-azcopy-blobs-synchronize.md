---
title: Synchronizace s Azure Blob Storage pomocí AzCopy v10 za účelem | Microsoft Docs
description: Tento článek obsahuje kolekci AzCopy ukázkových příkazů, které vám pomůžou synchronizovat s úložištěm objektů BLOB v Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502925"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Synchronizace s Azure Blob Storage pomocí AzCopy

Místní úložiště můžete synchronizovat s úložištěm objektů BLOB v Azure pomocí nástroje příkazového řádku AzCopy v10 za účelem. 

Obsah místního systému souborů můžete synchronizovat s kontejnerem objektů BLOB. Můžete také synchronizovat kontejnery a virtuální adresáře mezi sebou. Synchronizace je jedním ze způsobů. Jinými slovy, jste si zvolili, který z těchto dvou koncových bodů je zdroj a který je cílový. Synchronizace používá také server k rozhraní API serveru. Příklady uvedené v této části také fungují s účty, které mají hierarchický obor názvů. 

> [!NOTE]
> Aktuální verze AzCopy se nesynchronizuje mezi ostatními zdroji a cíli (například: File Storage nebo Amazon Web Services (AWS) S3).

Pokud chcete zobrazit příklady pro jiné typy úloh, jako je například nahrávání souborů, stahování objektů BLOB nebo kopírování objektů BLOB mezi účty, podívejte se na odkazy v části [Další kroky](#next-steps) v tomto článku.

## <a name="get-started"></a>Začínáme

V článku Začínáme [s AzCopy](storage-use-azcopy-v10.md) si můžete stáhnout AzCopy a seznamte se s různými způsoby, jak můžete službě úložiště poskytnout autorizační přihlašovací údaje.

> [!NOTE] 
> V příkladech v tomto článku se předpokládá, že jste zadali autorizační přihlašovací údaje pomocí Azure Active Directory (Azure AD).
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy. Příklad: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Pokyny

- Příkaz [synchronizovat](storage-ref-azcopy-sync.md) porovná názvy souborů a poslední upravená časová razítka. Nastavením `--delete-destination` volitelného příznaku na hodnotu `true` nebo `prompt` odstraňte soubory v cílovém adresáři, pokud už tyto soubory ve zdrojovém adresáři neexistují.

- Pokud jste příznak nastavili `--delete-destination` na `true` , AzCopy odstraní soubory bez zadání výzvy. Pokud chcete, aby se zobrazila výzva před tím, než AzCopy odstraní soubor, nastavte `--delete-destination` příznak na `prompt` .

- Pokud máte v plánu nastavit `--delete-destination` příznak na `prompt` nebo `false` , zvažte použití příkazu [copy](storage-ref-azcopy-copy.md) místo příkazu [synchronizovat](storage-ref-azcopy-sync.md) a nastavte `--overwrite` parametr na `ifSourceNewer` . Příkaz [copy](storage-ref-azcopy-copy.md) spotřebovává méně paměti a snižuje náklady na fakturaci, protože operace kopírování nemusí indexovat zdroj nebo cíl před přesunutím souborů. 

- Chcete-li zabránit nechtěnému odstranění, před použitím příznaku zajistěte, aby byla funkce [obnovitelného odstranění](../blobs/soft-delete-blob-overview.md) povolena `--delete-destination=prompt|true` .

- Počítač, na kterém spouštíte příkaz synchronizovat, by měl mít přesné systémové hodiny, protože čas poslední změny je kritický při určování, jestli se má soubor přenést. Pokud má váš systém značnou časovou škálu, vyhněte se úpravám souborů v cílovém umístění, a to až do doby, kdy plánujete spustit synchronizaci příkazu.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizace kontejneru se změnami v místním systému souborů

V tomto případě je cílem kontejner a zdrojem je místní systém souborů. 

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Příklad**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizace místního systému souborů s použitím změn kontejneru

V tomto případě je místní systém souborů cílový a kontejner je zdroj.

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

**Syntax**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Příklad**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Aktualizace kontejneru se změnami v jiném kontejneru

Prvním kontejnerem, který se zobrazí v tomto příkazu, je zdroj. Druhá je cílová.

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Příklad**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Aktualizace adresáře se změnami v adresáři v jiném kontejneru

Prvním adresářem, který se zobrazí v tomto příkazu, je zdroj. Druhá je cílová.

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Příklad**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Synchronizovat s volitelnými příznaky

Operaci synchronizace můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.

|Scenario|Příznak|
|---|---|
|Určete, jak mají být při stahování ověřovány striktní hodnoty hash MD5.|**--check-MD5** = \[ Kontrolujte \| \| FailIfDifferent FailIfDifferentOrMissing přihlášení \|\]|
|Vyloučení souborů na základě vzoru.|**--Exclude-Path**|
|Zadejte, jak chcete, aby byly položky protokolu související s synchronizací.|**--úroveň protokolu** = \[ \| \| informace o chybě upozornění \| none\]|

Úplný seznam příznaků najdete v tématu [Možnosti](storage-ref-azcopy-sync.md#options).

> [!NOTE]
> `--recursive`Příznak je ve výchozím nastavení nastaven na hodnotu `true` . `--exclude-pattern`Příznaky a se `--include-pattern` vztahují pouze na názvy souborů, nikoli na jiné části cesty k souboru. 

## <a name="next-steps"></a>Další kroky

Další příklady najdete v těchto článcích:

- [Příklady: Odeslat](storage-use-azcopy-blobs-upload.md)
- [Příklady: Stáhnout](storage-use-azcopy-blobs-download.md)
- [Příklady: Kopírování mezi účty](storage-use-azcopy-blobs-copy.md)
- [Příklady: Kbelíky Amazon S3](storage-use-azcopy-s3.md)
- [Příklady: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Příklady: soubory Azure](storage-use-azcopy-files.md)
- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

V těchto článcích najdete postup konfigurace nastavení, optimalizace výkonu a řešení potíží:

- [Nastavení konfigurace AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optimalizace výkonu AzCopy](storage-use-azcopy-optimize.md)
- [Řešení potíží s AzCopy v10 za účelem v Azure Storage pomocí souborů protokolu](storage-use-azcopy-configure.md)

