---
title: Přesun dat objektů Blob úložiště pomocí AzCopy - vědecké zpracování týmových dat
description: Přesun dat z a do služby Azure Blob Storage pomocí AzCopy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a204c68a05cec827fa8e432889cc60296132d4e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140562"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Přesun dat do a z Azure Blob Storage pomocí AzCopy
AzCopy je nástroj příkazového řádku určený pro nahrávání, stahování a kopírování dat do a z objektů blob, file a table storage Microsoft Azure.

Pokyny k instalaci nástroje AzCopy a další informace o používání s platformou Azure, najdete v části [Začínáme pomocí příkazového řádku Azcopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který byl nastaven pomocí skriptů poskytovaných [Data Science virtuální počítače v Azure](virtual-machines.md), pak nástroj AzCopy je už nainstalovaný na virtuálním počítači.
> 
> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahrávání a stahování dat, musíte znát vaše služby Azure storage účtu název a klíč účtu.

* Nastavení předplatného služby Azure, najdete v článku [zkušební verze na měsíc zdarma](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a účet a informace o klíči najdete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Spusťte příkazy AzCopy
Ke spuštění příkazů AzCopy, otevřete okno příkazového řádku a přejděte do instalačního adresáře nástroje AzCopy v počítači, kde se nachází AzCopy.exe spustitelný soubor. 

Základní syntaxe pro příkazy AzCopy je:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Můžete přidat umístění instalace nástroje AzCopy do systémové cesty a pak spusťte příkazy z libovolného adresáře. Ve výchozím nastavení je nástroj AzCopy nainstalovaný na *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* nebo *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Nahrání souborů do objektu blob Azure
Pokud chcete nahrát soubor, použijte následující příkaz:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Stahování souborů z objektu blob Azure
Ke stažení souboru z objektu blob Azure, použijte následující příkaz:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Přenos objektů BLOB mezi kontejnery služby Azure
Objekty BLOB přenášet mezi kontejnery služby Azure, použijte následující příkaz:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tipy pro používání nástroje AzCopy
> [!TIP]
> 1. Když **nahrávání** soubory, */S* nahraje soubory rekurzivně. Bez tohoto parametru se nahrají soubory nacházejí v podadresářích.  
> 2. Když **stahování** souboru */S* kontejneru rekurzivně vyhledává až všechny soubory v zadaném adresáři a jeho podadresářích, nebo všechny soubory, které odpovídají zadanému vzoru v daném adresáři a jeho podadresáře, se stáhnou.  
> 3. Nelze zadat **konkrétní objekt blob souboru** stáhnout pomocí */Source* parametru. Stažení konkrétního souboru, zadejte název souboru objektu blob ke stažení pomocí */vzorku* parametru. **/S** parametr je možné mít AzCopy rekurzivně vzor název soubor vyhledejte. Bez parametru vzor AzCopy stáhne všechny soubory v tomto adresáři.
> 
> 

