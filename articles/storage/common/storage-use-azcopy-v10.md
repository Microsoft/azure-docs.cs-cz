---
title: Kopírování nebo přesun dat do služby Azure Storage pomocí AzCopy v10 (Preview) | Dokumentace Microsoftu
description: Použití AzCopy v10 k přesunutí nebo zkopírování dat z objektu blob, data lake a obsah souboru nebo nástroj příkazového řádku (Preview). Kopírování dat do služby Azure Storage z místních souborů nebo kopírování dat v rámci nebo mezi účty úložiště. Snadno migrate data do služby Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ad3e96af95d952956af02acfd87d6d317bc29ed0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574973"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>Přenos dat pomocí AzCopy v10 (Preview)

AzCopy v10 je nástroj příkazového řádku pro kopírování dat do nebo z úložiště objektů Blob v Microsoft Azure a soubor (Preview). Nabízí přepracovaný rozhraní příkazového řádku AzCopy v10 a přenese nové architektury pro spolehlivé data. Pomocí AzCopy můžete kopírovat data mezi systémem souborů a účtem úložiště nebo mezi účty úložiště.

## <a name="whats-new-in-azcopy-v10"></a>Co je nového v AzCopy v10

- Synchronizuje systémy souborů do služby Azure Blob storage a naopak. Použití `azcopy sync <source> <destination>`. Ideální pro scénáře přírůstkového kopírování.
- Podporuje rozhraní API pro Azure Data Lake Storage Gen2. Použití `myaccount.dfs.core.windows.net` jako identifikátor URI pro volání rozhraní API Data Lake Storage Gen2.
- Podporuje kopírování celý účet (pouze služby Blob service) na jiný účet.
- Používá nový [Vložit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API pro podporu kopírování pro účet. Přenos dat je rychlejší, protože přenosy do klienta není povinné.
- Obsahuje seznam nebo odebere soubory a objekty BLOB v zadané cestě.
- Podporuje jeden vzor zástupných znaků v cestě a – vyloučení příznaky.
- Vytvoří pořadí úloh a soubor protokolu související s každou instancí nástroje AzCopy. Můžete zobrazit a restartujte předchozí úlohy a pokračování úlohy. AzCopy taky automaticky zopakuje přenos po selhání.
- Vylepšení funkce obecné informace o výkonu.

## <a name="download-and-install-azcopy"></a>Stáhněte a nainstalujte nástroje AzCopy

### <a name="latest-preview-version-v10"></a>Nejnovější verze preview (v10)

Stáhněte si nejnovější verzi AzCopy ve verzi preview:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (cíl)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Nejnovější produkční verzi (v8.1)

Stáhněte si [nejnovější produkční verzi AzCopy pro Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy podporuje služba Table storage (v7.3)

Stáhněte si [AzCopy v7.3 podporuje kopírování dat do a ze služby Microsoft Azure Table storage](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Kroky po instalaci

AzCopy v10 nevyžaduje instalaci. Otevřete aplikaci upřednostňované příkazového řádku a přejděte do složky, kde `azcopy.exe` nachází. V případě potřeby můžete přidat umístění složky AzCopy do systémové cesty pro snadné použití.

## <a name="authentication-options"></a>Možnosti ověřování

Při ověřování pomocí služby Azure Storage, AzCopy v10 podporuje následující možnosti:
- **Azure Active Directory** (pro podporované **službách objektů Blob a Data Lake Storage Gen2**). Použití ```.\azcopy login``` se přihlásit pomocí Azure Active Directory.  Uživatel by měl mít [přiřazenou roli "Přispěvatel dat objektu Blob úložiště"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) k zápisu do úložiště objektů Blob pomocí ověřování Azure Active Directory. Ověřování pomocí spravované identity pro prostředky Azure, použijte `azcopy login --identity`.
- **Sdílené přístupové tokeny podpis [podporované pro služby objektů Blob a souboru]**. Připojte token sdíleného přístupového podpisu (SAS) do cesty objektu blob na příkazový řádek, který ho použít. Můžete generovat tokeny SAS pomocí webu Azure portal, [Průzkumníka služby Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [Powershellu](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), nebo jiné nástroje podle vašeho výběru. Další informace najdete v tématu [příklady](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Začínáme

> [!TIP]
> **Dáváte přednost grafického uživatelského rozhraní?**
>
> [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), klient pro stolní počítače, která zjednodušuje správu daty Azure Storage teď používá AzCopy k urychlení přenos dat do a z Azure Storage.
>
> Povolit AzCopy v Průzkumníku služby Storage v rámci **ve verzi Preview** nabídky.
> ![Povolte AzCopy jako modul přenosu v Průzkumníku služby Storage](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 má svým zdokumentovaných syntaxi. Když se přihlásíte do Azure Active Directory, Obecná syntaxe vypadá takto:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Zde je, jak získat seznam dostupných příkazů:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Pokud chcete zobrazit stránku nápovědy a příklady pro konkrétní příkaz, spusťte následující příkaz:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Vytvoření objektů blob v kontejneru nebo ve sdílené složce 

**Vytvořte kontejner objektů blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Vytvoření sdílené složky**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Vytvořte kontejner objektů blob pomocí Azure Data Lake Storage Gen2**

Pokud jste povolili hierarchické obory názvů na vašem účtu úložiště objektů Blob, můžete použít následující příkaz Vytvořit nový kontejner objektů blob pro nahrávání souborů.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Kopírování dat do služby Azure Storage

Pomocí příkazu kopírování přenos dat ze zdroje do cíle. Zdroj nebo cíl mohou být a:
- Místní systém souborů
- Azure Blob nebo virtuální adresář nebo kontejner identifikátoru URI
- Soubor nebo adresář/sdílená složka Azure identifikátoru URI
- Identifikátor URI systému souborů a adresářů a souborů Azure Data Lake Storage Gen2

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Následující příkaz nahraje všechny soubory ve složce `C:\local\path` rekurzivně do kontejneru `mycontainer1`, vytváření `path` adresáře v kontejneru:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Následující příkaz nahraje všechny soubory ve složce `C:\local\path` (bez recursing do podadresáře) do kontejneru `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Pokud chcete najít další příklady, použijte následující příkaz:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Kopírovat data mezi dva účty úložiště

Kopírování dat mezi dva účty úložiště používá [Vložit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API a nepoužívá šířky pásma sítě klientského počítače. Data se zkopíruje mezi dvěma servery služby Azure Storage přímo, zatímco AzCopy jednoduše orchestruje operace kopírování. Tato možnost je aktuálně k dispozici pouze pro úložiště objektů Blob.

Chcete-li kopírovat data mezi dva účty úložiště, použijte následující příkaz:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Tento příkaz se vytvořit výčet všech kontejnerů objektů blob a zkopírujte je do cílového účtu. V současné době podporuje AzCopy v10 kopírování jenom objekty BLOB bloku mezi dva účty úložiště. Přeskočí všechny ostatní objekty účtu z úložiště, (například doplňovací objekty BLOB, objekty BLOB stránky, soubory, tabulky a fronty).

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopírovat obraz virtuálního pevného disku do účtu úložiště

AzCopy v10 ve výchozím nastavení odesílá data do objektů BLOB bloku. Nicméně pokud má zdrojový soubor `.vhd` rozšíření, AzCopy v10 budou ve výchozím nastavení nahrávání do objektů blob stránky. Tato akce není v tuto chvíli konfigurovatelné.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Synchronizace: přírůstkové kopírování a delete (pouze úložiště objektů Blob)

Příkaz Synchronizovat Synchronizuje obsah zdrojového adresáře do jiného adresáře v cíli, porovnání názvy souborů a časové razítko poslední změny. Tato operace zahrnuje volitelné odstranění cílové soubory, pokud jsou ve zdroji neexistuje při `--delete-destination=prompt|true` příznak je k dispozici. Ve výchozím nastavení je zakázáno odstranit chování. 

> [!NOTE] 
> Použití `--delete-destination` příznak opatrně. Povolit [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkce před povolením chování při odstraňování synchronizované abyste zabránili nechtěnému odstranění ve vašem účtu. 
>
> Když `--delete-destination` je nastavena na hodnotu true, AzCopy odstraní soubory, které neexistují ve zdroji z cílového bez jakékoli výzvy uživateli. Pokud chcete být vyzváni k potvrzení, použít `--delete-destination=prompt`.

K synchronizaci vašeho místního systému souborů do účtu úložiště, použijte následující příkaz:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Také můžete synchronizovat kontejner objektů blob až místního systému souborů:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Tento příkaz přírůstkové synchronizace zdroje do cílového umístění podle naposledy upravené časové razítko. Pokud přidáte nebo odstraníte soubor ve zdroji, AzCopy v10 bude totéž v cílovém umístění. Před odstraněním AzCopy zobrazí výzvu k potvrzení.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Konfigurace nastavení proxy serveru pro AzCopy v10, nastavte https_proxy proměnných prostředí pomocí následujícího příkazu:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimalizace propustnosti

Nastavte proměnnou prostředí AZCOPY_CONCURRENCY_VALUE konfigurovat počet souběžných požadavků a k řízení spotřeby propustnost výkonu a prostředků. Ve výchozím nastavení je hodnota nastavena na 300. Snížení hodnoty bude omezovat šířku pásma a využití procesoru podle AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Změnit umístění souboru protokolu

Můžete změnit umístění souboru protokolu v případě potřeby nebo zabránili zaplnění disku s operačním systémem.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Změnit výchozí úroveň protokolování 

Ve výchozím nastavení je AzCopy úroveň protokolu nastavena na informace. Pokud chcete snížit úroveň podrobností protokolu k ušetřit místo na disku, přepíše nastavení pomocí ``--log-level`` možnost. Dostupné úrovně jsou: LADĚNÍ, informace, upozornění, chyby, PANIKOU a závažná chyba.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte protokoly chyb

Následující příkaz získá všechny chyby se stavem UPLOADFAILED z 04dc9ca9-158f-7945-5933-564021086c79 protokolu:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Řešení potíží

AzCopy v10 vytvoří soubory protokolů a soubory plán pro každou úlohu. Protokoly můžete prozkoumat a vyřešit potenciální problémy. Tyto protokoly budou obsahovat stav selhání (UPLOADFAILED COPYFAILED a DOWNLOADFAILED), úplnou cestu a důvod selhání. Protokoly úlohy a plán soubory jsou umístěny ve složce % USERPROFILE\\.azcopy složky na Windows nebo $HOME\\.azcopy složky na Mac a Linux.

> [!IMPORTANT]
> Při odesílání požadavku na Microsoft Support (nebo řešení potíží týkajících se třetí stranu), sdílet zrevidovaně verzi příkazu, kterou chcete spustit. Tím se zajistí, že SAS není omylem sdílet s kýmkoli. Můžete najít zrevidovaně verzi na začátku souboru protokolu.

### <a name="view-and-resume-jobs"></a>Zobrazit a obnovit úlohy

Každé operace přenosu vytvoří úlohu AzCopy. Chcete-li zobrazit historii úloh použijte následující příkaz:

```azcopy
.\azcopy jobs list
```

Chcete-li zobrazit statistiky úlohy, použijte následující příkaz:

```azcopy
.\azcopy jobs show <job-id>
```

Chcete-li filtrovat přenosy podle stavu, použijte následující příkaz:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Použijte následující příkaz k obnovení úlohy se nezdařilo nebo bylo zrušeno. Tento příkaz používá jeho identifikátor spolu s tokenem SAS. Není trvalé z bezpečnostních důvodů:

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Další postup

Pokud máte dotazy, problémy nebo obecnou zpětnou, odešlete je [na Githubu](https://github.com/Azure/azure-storage-azcopy).


