---
title: Kopírování nebo přesun dat do služby Azure Storage pomocí AzCopy v10 (Preview) | Dokumentace Microsoftu
description: Použití AzCopy v10 nástroj (Preview) se přesunout nebo kopírovat data do nebo z objektu blob, data lake a obsah souboru. Kopírování dat do služby Azure Storage z místních souborů nebo kopírování dat v rámci nebo mezi účty úložiště. Snadno migrate data do služby Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: a4e115194d7e903edae4b4713c4f65eef9895cbf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467114"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Přenos dat pomocí AzCopy v10 (Preview)

AzCopy v10 je nástroj příkazového řádku příští generace pro kopírování dat do a z úložiště objektů Blob v Microsoft Azure a soubor, který nabízí přepracovaný rozhraní příkazového řádku a nové architektury pro vysoce výkonné, spolehlivé datové přenosy (Preview). Pomocí AzCopy můžete kopírovat data mezi systémem souborů a účtem úložiště nebo mezi účty úložiště.

## <a name="whats-new-in-azcopy-v10"></a>Co je nového v AzCopy v10

- Synchronizujte systém souborů Azure Blob nebo naopak. Použití `azcopy sync <source> <destination>`. Ideální pro scénáře přírůstkového kopírování.
- Podporuje rozhraní API pro Azure Data Lake Storage Gen2. Použití `myaccount.dfs.core.windows.net` jako identifikátor URI pro volání rozhraní API ADLS Gen2.
- Podporuje kopírování celý účet (pouze služby Blob service) na jiný účet.
- Účet pro účet kopie teď používá nový [umístit z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API. Žádný přenos dat do klienta je potřeba rychleji díky přenosu!
- Seznam nebo odebrat soubory a objekty BLOB v zadané cestě.
- Stejně jako – zahrnout a--vyloučit příznaky podporuje jeden vzor zástupných znaků v cestě.
- Odolnost: Každá instance nástroje AzCopy se vytvoří pořadí úloh a související protokol. Můžete zobrazit a restartujte předchozí úlohy a pokračování úlohy. AzCopy taky automaticky zopakuje přenos po selhání.
- Vylepšení obecné informace o výkonu.

## <a name="download-and-install-azcopy"></a>Stáhněte a nainstalujte nástroje AzCopy

### <a name="latest-preview-version-v10"></a>Nejnovější verze preview (v10)

Stáhněte si nejnovější verzi AzCopy ve verzi preview:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Nejnovější produkční verzi (v8.1)

Stáhněte si [nejnovější produkční verzi AzCopy pro Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy podporuje služba Table storage (v7.3)

Stáhněte si [AzCopy v7.3 podporuje kopírování dat do a ze služby Microsoft Azure Table storage](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Kroky po instalaci

AzCopy v10 nevyžaduje instalaci. Otevřete upřednostňované aplikace příkazového řádku a přejděte do složky, kde `azcopy.exe` spustitelný soubor se nachází. V případě potřeby můžete přidat umístění složky AzCopy do systémové cesty.

## <a name="authentication-options"></a>Možnosti ověřování

AzCopy v10 umožňuje při ověřování pomocí služby Azure Storage, použijte následující možnosti:
- **Azure Active Directory [podporováno na objektech Blob a ADLS Gen2]**. Použití ```.\azcopy login``` k přihlášení pomocí Azure Active Directory.  Uživatel by měl mít [přiřazenou roli "Přispěvatel dat objektu Blob úložiště"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) k zápisu do úložiště objektů Blob pomocí ověřování Azure Active Directory.
- **Tokeny SAS [podporované ve službě Blob a souboru]**. Do cesty objektu blob na příkazovém řádku ji používat, připojte SAS token. Můžete vygenerovat token SAS pomocí webu Azure Portal [Průzkumníka služby Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [Powershellu](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), nebo jiné nástroje podle vašeho výběru. Další informace najdete v tématu [příklady](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Začínáme

AzCopy v10 má jednoduché svým zdokumentovaných syntaxi. Obecná syntaxe vypadá takto, po přihlášení do služby Azure Active Directory:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Zde je, jak získat seznam dostupných příkazů:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Pokud chcete zobrazit stránku nápovědy a příklady pro konkrétní příkaz spusťte následující příkaz:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Vytvoření kontejneru objektů Blob nebo sdílené složky 

**Vytvořte kontejner objektů Blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Vytvoření sdílené složky**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Vytvořte kontejner objektů Blob pomocí ADLS Gen2**

Pokud jste povolili hierarchické obory názvů na vašem účtu úložiště objektů blob, můžete vytvořit nový systém souborů (kontejner objektů Blob), aby soubory můžete nahrát do něj následující příkaz.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Kopírování dat do služby Azure Storage

Pomocí příkazu kopírování přenos dat ze zdroje do cíle. Zdroj a cíl mohou být a:
- Místní systém souborů
- Azure Blob nebo virtuální adresář nebo kontejner identifikátoru URI
- Soubor nebo adresář/sdílená složka Azure identifikátoru URI
- Identifikátor URI systému souborů a adresářů a souborů Azure Data Lake Storage Gen2

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Následující příkaz nahraje všechny soubory ve složce `C:\local\path` rekurzivně do kontejneru `mycontainer1` vytváření `path` adresáře v kontejneru:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Následující příkaz nahraje všechny soubory ve složce `C:\local\path` (bez recursing do podadresáře) do kontejneru `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Pokud chcete získat další příklady, použijte následující příkaz:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Kopírovat data mezi dva účty úložiště

Kopírování dat mezi dva účty úložiště používá [Vložit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API a nevyužívá šířku pásma sítě klientského počítače. Data se zkopíruje mezi dvěma servery služby Azure Storage přímo při AzCopy jednoduše orchestruje operace kopírování. Tato možnost aktuálně dostupná jenom pro úložiště objektů Blob.

Chcete-li kopírovat data mezi dva účty úložiště, použijte následující příkaz:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Příkaz se vytvořit výčet všech kontejnerů objektů blob a zkopírujte je do cílového účtu. V současné době podporuje AzCopy v10 kopírování jenom objekty BLOB bloku mezi dva účty. Všechny ostatní objekty účtu úložiště (doplňovací objekty BLOB, objekty BLOB stránky, soubory, tabulky a fronty), se přeskočí.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopírovat obraz virtuálního pevného disku do účtu úložiště

AzCopy v10 ve výchozím nastavení odesílá data do objektů BLOB bloku. Ale pokud zdrojového souboru má příponu virtuálního pevného disku, AzCopy v10 bude ve výchozím nastavení nahrajte ho do objektů blob stránky. Toto chování není aktuálně konfigurovatelné.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Synchronizace: přírůstkové kopírování a delete (pouze úložiště objektů Blob)

> [!NOTE]
> Příkaz Synchronizovat Synchronizuje obsah ze zdroje do cíle a to zahrnuje odstranění cílové soubory, pokud jsou ve zdroji neexistuje. Ujistěte se, že používáte cíl, kterou chcete synchronizovat.

K synchronizaci vašeho místního systému souborů do účtu úložiště, použijte následující příkaz:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Stejným způsobem můžete synchronizovat kontejner objektů Blob až místního systému souborů:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Tento příkaz umožňuje přírůstkové synchronizace zdroje do cíle, na základě časových razítek poslední změny. Pokud přidáte nebo odstraníte soubor ve zdroji, AzCopy v10 bude totéž v cílovém umístění. Před odstraněním AzCopy zobrazí výzvu k potvrzení odstranění souborů.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Konfigurace nastavení proxy serveru pro AzCopy v10, nastavte proměnnou https_proxy prostředí pomocí následujícího příkazu:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimalizace propustnosti

Nastavte proměnnou prostředí AZCOPY_CONCURRENCY_VALUE konfigurovat počet souběžných požadavků a řídit propustnost výkonu a využití prostředků. Ve výchozím nastavení je hodnota nastavena na 300. Snížení hodnoty bude omezovat šířku pásma a využití procesoru podle AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>Řešení potíží

AzCopy v10 vytvoří plán soubory pro všechny úlohy a soubory protokolu. Protokoly můžete prozkoumat a vyřešit potenciální problémy. Tyto protokoly budou obsahovat stav selhání (UPLOADFAILED COPYFAILED a DOWNLOADFAILED), úplnou cestu a důvod selhání. Protokoly úlohy a plán soubory jsou umístěny ve složce % USERPROFILE\\.azcopy složky.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte protokoly chyb

Následující příkaz získá všechny chyby se stavem UPLOADFAILED z 04dc9ca9-158f-7945-5933-564021086c79 protokolu:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Zobrazit a obnovit úlohy

Každé operace přenosu vytvoří úlohu AzCopy. Můžete zobrazit historii úloh pomocí následujícího příkazu:

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

Můžete obnovit úlohu se nepovedlo zrušit pomocí jeho identifikátoru spolu s tokenem SAS (není trvalé z bezpečnostních důvodů):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>Změnit výchozí úroveň protokolování

Ve výchozím nastavení je nástroj AzCopy úroveň protokolu nastavena na informace. Pokud chcete snížit úroveň podrobností protokolu k ušetřit místo na disku, přepíše nastavení pomocí ``--log-level`` možnost. Dostupné úrovně jsou: LADĚNÍ, informace, upozornění, chyby, PANIKOU a závažná chyba

## <a name="next-steps"></a>Další postup

Vaše zpětná vazba je vždycky vítány. Pokud máte nějaké dotazy, problémy nebo obecnou zpětnou odešlete je na https://github.com/Azure/azure-storage-azcopy. Děkujeme!
