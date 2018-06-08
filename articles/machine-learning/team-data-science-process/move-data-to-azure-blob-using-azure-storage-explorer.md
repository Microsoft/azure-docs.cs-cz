---
title: Přesun dat do a z úložiště objektů Blob s Azure Storage Explorer | Microsoft Docs
description: Přesun dat z a do služby Azure Blob Storage pomocí nástroje Azure Storage Explorer
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 464290901959ee052059b092b737e369928bd19b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837785"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Přesun dat do a z Azure Blob Storage pomocí Průzkumníka úložiště Azure
Azure Storage Explorer je bezplatný nástroj od společnosti Microsoft, který umožňuje pracovat s daty Azure Storage ve Windows, systému macOS a Linux. Toto téma popisuje, jak použít jej k nahrávání a stahování dat z Azure blob storage. Nástroj si můžete stáhnout z [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který byl nastaven pomocí skriptů poskytovaných [datové vědy virtuálních počítačů v Azure](virtual-machines.md), pak Azure Storage Explorer je již nainstalován ve virtuálním počítači.
> 
> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v části [základy objektu Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby objektů Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahrávání nebo stahování dat, musíte znát klíč účet a název účtu úložiště Azure. 

* Předplatné Azure, naleznete v tématu [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny pro vytvoření účtu úložiště a získávání účet a klíčové informace naleznete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md). Poznamenejte si přístupový klíč účtu úložiště potřebujete tento klíč pro připojení k účtu pomocí nástroje Azure Storage Explorer.
* Nástroj Azure Storage Explorer si můžete stáhnout z [Microsoft Azure Storage Explorer](http://storageexplorer.com/). Přijměte výchozí hodnoty během instalace.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Pomocí Průzkumníka úložiště Azure
Následující kroky dokumentů postup nahrávání nebo stahování dat pomocí Průzkumníka úložiště Azure. 

1. Spusťte Průzkumníka úložiště Microsoft Azure.
2. Chcete-li zprovoznit **přihlásit ke svému účtu...**  průvodci vyberte **nastavení účtu Azure** ikonu, pak **přidat účet** a zadat přihlašovací údaje. ![Přidat účet úložiště Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Se zprovoznit **připojit k Azure Storage** průvodce, vyberte **připojit k úložišti Azure** ikonu. ![Připojit k úložišti Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Na zadejte přístupový klíč účtu úložiště Azure **připojit k Azure Storage** průvodce a potom **Další**. ![Připojit k úložišti Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Zadejte název účtu úložiště v **název účtu** pole a pak vyberte **Další**. ![Připojit externí úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Přidat účet úložiště by měl být nyní uvedený. Pokud chcete vytvořit kontejner objektů blob v účtu úložiště, klikněte pravým tlačítkem myši **kontejnery objektů Blob** uzlu v daném účtu, vyberte **vytvořit kontejner objektů Blob**a zadejte název.
7. Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a klikněte na **nahrát** tlačítko.![ Účty úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klikněte na **...**  napravo **soubory** , vyberte jeden nebo více souborů ze systému souborů a klikněte na **nahrát** zahájíte nahrávání souborů.![ Nahrání souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Ke stahování dat, výběr objektu blob v kontejneru odpovídající ke stažení a klikněte na tlačítko **Stáhnout**. ![Stažení souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

