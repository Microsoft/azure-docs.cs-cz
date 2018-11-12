---
title: 'Kurz: Migrace místních dat do služby Azure Storage pomocí AzCopy | Microsoft Docs'
description: V tomto kurzu použijete AzCopy k migraci dat nebo kopírování dat do nebo z objektu blob, tabulky a obsahu souboru. Jednoduše migrujte data z místního úložiště do služby Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 347092fd7d5865379911265b19477ac16e3bcd69
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261379"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy

AzCopy je nástroj příkazového řádku určený ke kopírování dat do nebo z úložiště objektů blob v Azure, služby Soubory Azure a úložiště tabulek v Azure pomocí jednoduchých příkazů. Příkazy jsou navržené pro zajištění optimálního výkonu. Pomocí AzCopy můžete kopírovat data mezi systémem souborů a účtem úložiště nebo mezi účty úložiště. AzCopy je možné použít ke kopírování místních dat (v místním prostředí) do účtu úložiště.

Stáhněte si verzi AzCopy určenou pro váš operační systém:

* [AzCopy v Linuxu](storage-use-azcopy-linux.md) využívá rozhraní .NET Core Framework. Nabízí možnosti příkazového řádku ve stylu POSIX a cílí tak na platformy Linuxu. 
* [AzCopy ve Windows](storage-use-azcopy.md) využívá rozhraní .NET Framework. Nabízí možnosti příkazového řádku ve stylu Windows. 
 
V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště 
> * Použití AzCopy k nahrání veškerých dat
> * Úprava dat pro účely testování
> * Vytvoření plánované úlohy nebo úlohy Cron pro identifikaci nových souborů k nahrání

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, stáhněte si nejnovější verzi AzCopy v [Linuxu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) nebo ve [Windows](https://aka.ms/downloadazcopy).

Pokud používáte Windows, budete potřebovat nástroj [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), který se v tomto kurzu používá k naplánování úlohy. Uživatelé Linuxu místo toho použijí příkaz crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Prvním krokem je vytvoření kontejneru, protože objekty blob se musí vždy nahrávat do kontejneru. Kontejnery slouží jako metoda uspořádání skupin objektů blob podobně jako složky slouží k uspořádání souborů na počítači. 

Podle těchto pokynů vytvořte kontejner:

1. Na hlavní stránce vyberte tlačítko **Účty úložiště** a pak vyberte účet úložiště, který jste vytvořili.
2. V části **Služby** vyberte **Objekty blob** a pak vyberte **Kontejner**. 

   ![Vytvoření kontejneru](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Názvy kontejnerů musí začínat písmenem nebo číslicí. Můžou obsahovat pouze písmena, číslice a znak spojovníku (-). Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Nahrání obsahu složky do úložiště objektů blob

Pomocí AzCopy můžete nahrát všechny soubory ve složce do úložiště objektů blob ve [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) nebo v [Linuxu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Pokud chcete nahrát všechny objekty blob ve složce, zadejte následující příkaz AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Nahraďte `<key>` a `key` klíčem svého účtu. Na webu Azure Portal můžete klíč svého účtu získat tak, že v části **Nastavení** ve svém účtu úložiště vyberete **Přístupové klíče**. Vyberte klíč a vložte ho do příkazu AzCopy. Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje. Aktualizuje zdrojovou cestu ke svému adresáři dat a nahraďte **myaccount** v cílové adrese URL názvem svého účtu úložiště.

Pokud chcete obsah zadaného adresáře nahrát do úložiště objektů blob rekurzivně, zadejte možnost `--recursive` (Linux) nebo `/S` (Windows). Když spustíte AzCopy s některou z těchto možností, nahrají se také všechny podsložky a soubory, které obsahují.

## <a name="upload-modified-files-to-blob-storage"></a>Nahrání upravených souborů do úložiště objektů blob

Pomocí AzCopy můžete [nahrávat soubory](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) na základě času jejich poslední úpravy. Pokud to chcete vyzkoušet, upravte nebo vytvořte nové soubory ve zdrojovém adresáři pro účely testování. Pokud chcete nahrát pouze aktualizované nebo nové soubory, přidejte do příkazu AzCopy parametr `--exclude-older` (Linux) nebo `/XO` (Windows).

Pokud chcete zkopírovat pouze zdrojové prostředky, které neexistují v cíli, zadejte v příkazu AzCopy parametry `--exclude-older` i `--exclude-newer` (Linux) nebo `/XO` i `/XN` (Windows). AzCopy nahraje pouze aktualizovaná data na základě jejich časového razítka.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task"></a>Vytvoření plánované úlohy

Můžete vytvořit plánovanou úlohu nebo úlohu Cron, která spouští skript příkazů AzCopy. Skript v zadaném časovém intervalu identifikuje nová místní data a nahrává je do cloudového úložiště.

Zkopírujte následující příkaz AzCopy do textového editoru. Aktualizujte hodnoty parametrů příkazu AzCopy na odpovídající hodnoty. Uložte soubor jako `script.sh` (Linux) nebo `script.bat` (Windows) pro AzCopy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy se spustí s možností podrobného výstupu `--verbose` (Linux) nebo `/V` (Windows). Výstup se přesměruje do souboru protokolu.

V tomto kurzu se k vytvoření plánované úlohy ve Windows používá [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx). Příkaz [Crontab](http://crontab.org/) slouží k vytvoření úlohy Cron v Linuxu.
 **Schtasks** umožňuje správci vytvářet, odstraňovat, dotazovat, měnit, spouštět a ukončovat plánované úlohy na místním nebo vzdáleném počítači. **Cron** umožňuje uživatelům Linuxu a Unixu spouštět příkazy nebo skripty v zadaném datu a čase pomocí [výrazů Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Pokud chcete vytvořit úlohu Cron v Linuxu, zadejte v terminálu následující příkaz:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Zadání výrazu Cron `*/5 * * * * ` v příkazu značí, že se má skript prostředí `script.sh` spouštět každých pět minut. Spouštění skriptu můžete naplánovat na určitý čas každý den, měsíc nebo rok. Další informace o nastavení data a času provádění úlohy najdete v článku o [výrazech Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Pokud chcete vytvořit plánovanou úlohu ve Windows, zadejte na příkazovém řádku nebo v PowerShellu následující příkaz:

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Příkaz používá:
- Parametr `/SC` k určení minutového plánu.
- Parametr `/MO` k určení pětiminutového intervalu.
- Parametr `/TN` k určení názvu úlohy.
- Parametr `/TR` k určení cesty k souboru `script.bat`.

Další informace o vytváření plánovaných úloh ve Windows najdete v tématu [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Pokud chcete ověřit správné spouštění plánované úlohy nebo úlohy Cron, vytvořte v adresáři `myfolder` nové soubory. Počkejte pět minut a ověřte, že se nové soubory nahrály do účtu úložiště. Přejděte do svého adresáře protokolů a zobrazte protokoly výstupu plánované úlohy nebo úlohy Cron.

## <a name="next-steps"></a>Další kroky

Další informace o způsobech přesunu místních dat do služby Azure Storage a naopak najdete na následujícím odkazu:

> [!div class="nextstepaction"]
> [Přesun dat do a ze služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)  

Další informace konkrétně o AzCopy najdete v odpovídajícím článku pro váš operační systém:

> [!div class="nextstepaction"]
> [Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md)
> [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md)