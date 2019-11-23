---
title: 'Tutorial: Migrate on-premises data to Azure Storage with AzCopy| Microsoft Docs'
description: V tomto kurzu použijete AzCopy k migraci dat nebo kopírování dat do nebo z objektu blob, tabulky a obsahu souboru. Jednoduše migrujte data z místního úložiště do služby Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327523"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrate on-premises data to cloud storage with AzCopy

AzCopy je nástroj příkazového řádku určený ke kopírování dat do nebo z úložiště objektů blob v Azure, služby Soubory Azure a úložiště tabulek v Azure pomocí jednoduchých příkazů. Příkazy jsou navržené pro zajištění optimálního výkonu. Pomocí AzCopy můžete kopírovat data mezi systémem souborů a účtem úložiště nebo mezi účty úložiště. AzCopy je možné použít ke kopírování místních dat (v místním prostředí) do účtu úložiště.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště 
> * Použití AzCopy k nahrání veškerých dat
> * Úprava dat pro účely testování
> * Vytvoření plánované úlohy nebo úlohy Cron pro identifikaci nových souborů k nahrání

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

To complete this tutorial, download the latest version of AzCopy. See [Get started with AzCopy](storage-use-azcopy-v10.md).

Pokud používáte Windows, budete potřebovat nástroj [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), který se v tomto kurzu používá k naplánování úlohy. Uživatelé Linuxu místo toho použijí příkaz crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Prvním krokem je vytvoření kontejneru, protože objekty blob se musí vždy nahrávat do kontejneru. Kontejnery slouží jako metoda uspořádání skupin objektů blob podobně jako složky slouží k uspořádání souborů na počítači.

Podle těchto pokynů vytvořte kontejner:

1. Na hlavní stránce vyberte tlačítko **Účty úložiště** a pak vyberte účet úložiště, který jste vytvořili.
2. V části **Služby** vyberte **Objekty blob** a pak vyberte **Kontejner**.

   ![Vytvoření kontejneru](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Názvy kontejnerů musí začínat písmenem nebo číslicí. Můžou obsahovat pouze písmena, číslice a znak spojovníku (-). Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Download AzCopy

Download the AzCopy V10 executable file.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Place the AzCopy file anywhere on your computer. Add the location of the file to your system path variable so that you can refer to this executable file from any folder on your computer.

## <a name="authenticate-with-azure-ad"></a>Ověřování pomocí Azure AD

First, assign the [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) role to your identity. See [Grant access to Azure blob and queue data with RBAC in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Then, open a command prompt, type the following command, and press the ENTER key.

```azcopy
azcopy login
```

This command returns an authentication code and the URL of a website. Open the website, provide the code, and then choose the **Next** button.

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

A sign-in window will appear. In that window, sign into your Azure account by using your Azure account credentials. After you've successfully signed in, you can close the browser window and begin using AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Nahrání obsahu složky do úložiště objektů blob

Pomocí AzCopy můžete nahrát všechny soubory ve složce do úložiště objektů blob ve [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) nebo v [Linuxu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Pokud chcete nahrát všechny objekty blob ve složce, zadejte následující příkaz AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`).

* Replace the `<storage-account-name>` placeholder with the name of your storage account.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To upload the contents of the specified directory to Blob storage recursively, specify the `--recursive` option. When you run AzCopy with this option, all subfolders and their files are uploaded as well.

## <a name="upload-modified-files-to-blob-storage"></a>Nahrání upravených souborů do úložiště objektů blob

You can use AzCopy to upload files based on their last-modified time. 

Pokud to chcete vyzkoušet, upravte nebo vytvořte nové soubory ve zdrojovém adresáři pro účely testování. Then, use the AzCopy `sync` command.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`.

* Replace the `<storage-account-name>` placeholder with the name of your storage account.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To learn more about the `sync` command, see [Synchronize files](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Vytvoření plánované úlohy

Můžete vytvořit plánovanou úlohu nebo úlohu Cron, která spouští skript příkazů AzCopy. Skript v zadaném časovém intervalu identifikuje nová místní data a nahrává je do cloudového úložiště.

Zkopírujte následující příkaz AzCopy do textového editoru. Aktualizujte hodnoty parametrů příkazu AzCopy na odpovídající hodnoty. Uložte soubor jako `script.sh` (Linux) nebo `script.bat` (Windows) pro AzCopy. 

These examples assume that your folder is named `myFolder`, your storage account name is `mystorageaccount` and your container name is `mycontainer`.

> [!NOTE]
> The Linux example appends a SAS token. You'll need to provide one in your command. The current version of AzCopy V10 doesn't support Azure AD authorization in cron jobs.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

V tomto kurzu se k vytvoření plánované úlohy ve Windows používá [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx). Příkaz [Crontab](http://crontab.org/) slouží k vytvoření úlohy Cron v Linuxu.

 **Schtasks** umožňuje správci vytvářet, odstraňovat, dotazovat, měnit, spouštět a ukončovat plánované úlohy na místním nebo vzdáleném počítači. **Cron** umožňuje uživatelům Linuxu a Unixu spouštět příkazy nebo skripty v zadaném datu a čase pomocí [výrazů Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Pokud chcete vytvořit úlohu Cron v Linuxu, zadejte v terminálu následující příkaz:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Zadání výrazu Cron `*/5 * * * *` v příkazu značí, že se má skript prostředí `script.sh` spouštět každých pět minut. Spouštění skriptu můžete naplánovat na určitý čas každý den, měsíc nebo rok. Další informace o nastavení data a času provádění úlohy najdete v článku o [výrazech Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Pokud chcete vytvořit plánovanou úlohu ve Windows, zadejte na příkazovém řádku nebo v PowerShellu následující příkaz:

This example assumes that your script is located in the root drive of your computer, but your script can be anywhere that you want.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Příkaz používá:
- Parametr `/SC` k určení minutového plánu.
- Parametr `/MO` k určení pětiminutového intervalu.
- Parametr `/TN` k určení názvu úlohy.
- Parametr `/TR` k určení cesty k souboru `script.bat`.

Další informace o vytváření plánovaných úloh ve Windows najdete v tématu [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Pokud chcete ověřit správné spouštění plánované úlohy nebo úlohy Cron, vytvořte v adresáři `myFolder` nové soubory. Počkejte pět minut a ověřte, že se nové soubory nahrály do účtu úložiště. Přejděte do svého adresáře protokolů a zobrazte protokoly výstupu plánované úlohy nebo úlohy Cron.

## <a name="next-steps"></a>Další kroky

Další informace o způsobech přesunu místních dat do služby Azure Storage a naopak najdete na následujícím odkazu:

* [Přesun dat do a ze služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)  

For more information about AzCopy, see any of these articles:

* [Get started with AzCopy](storage-use-azcopy-v10.md)

* [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md)

* [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)

* [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)
 
* [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)
