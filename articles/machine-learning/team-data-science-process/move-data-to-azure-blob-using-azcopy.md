---
title: Přesun dat do a z Azure Blob Storage pomocí AzCopy | Microsoft Docs
description: Přesun dat z a do služby Azure Blob Storage pomocí AzCopy
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: a25a35bc05781c6a52e21d697233ba1187ebeccf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838411"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Přesun dat do a z Azure Blob Storage pomocí AzCopy
AzCopy je nástroj příkazového řádku pro odesílání, stahování a kopírování dat do a z objektu blob, soubory a úložiště tabulek Microsoft Azure.

Pokyny k instalaci na platformu Azure pomocí nástroje AzCopy a další informace najdete v tématu [Začínáme pomocí příkazového řádku Azcopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který byl nastaven pomocí skriptů poskytovaných [datové vědy virtuálních počítačů v Azure](virtual-machines.md), pak AzCopy je již nainstalován ve virtuálním počítači.
> 
> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v části [základy objektu Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby objektů Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahrávání nebo stahování dat, musíte znát klíč účet a název účtu úložiště Azure.

* Předplatné Azure, naleznete v tématu [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny pro vytvoření účtu úložiště a získávání účet a klíčové informace naleznete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Příkazy spouštějte AzCopy
Ke spuštění příkazů AzCopy, otevřete okno příkazového řádku a přejděte do instalačního adresáře nástroje AzCopy ve vašem počítači, kde se nachází AzCopy.exe spustitelný soubor. 

Základní syntaxe pro příkazy AzCopy je:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Můžete přidat umístění instalace AzCopy cestu v systému a pak spusťte příkazy z libovolného adresáře. Ve výchozím nastavení, je nainstalován nástroj AzCopy k *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* nebo *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Nahrání souborů do objektu blob Azure
Když Pokud chcete nahrát soubor, použijte následující příkaz:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Stáhnout soubory z objektu blob Azure
Ke stažení souboru z objektu blob Azure, použijte následující příkaz:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Přenos mezi Azure kontejnery objektů BLOB
Objekty BLOB přenášet mezi Azure kontejnery, použijte následující příkaz:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tipy pro použití nástroje AzCopy
> [!TIP]
> 1. Když **odesílání** soubory, */S* nahrávání souborů rekurzivně. Tento parametr nejsou uloženy soubory nacházejí v podadresářích adresáře.  
> 2. Když **stahování** souboru */S* Vyhledá rekurzivně kontejneru dokud všechny soubory v určeném adresáři a jejích podadresářů nebo všechny soubory, které odpovídají zadanému vzoru v daném adresáři a jejích podadresářů, se stáhnou.  
> 3. Nelze zadat **konkrétní objekt blob souboru** ke stažení pomocí */Source* parametr. Pokud chcete stáhnout konkrétní soubor, zadejte název objektu blob souboru ke stažení pomocí */vzor* parametr. **/S** parametr můžete použít tak, aby měl AzCopy hledat rekurzivně vzor názvu souboru. Bez parametru vzor AzCopy stáhne všechny soubory v tomto adresáři.
> 
> 

