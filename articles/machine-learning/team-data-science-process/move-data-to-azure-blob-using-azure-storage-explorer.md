---
title: Přesun dat do a z úložiště objektů Blob pomocí Průzkumníka služby Azure Storage | Dokumentace Microsoftu
description: Přesun dat z a do služby Azure Blob Storage pomocí nástroje Azure Storage Explorer
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
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
ms.openlocfilehash: 295fadd69a86769f74ec45ed3e77e8a5b807e14f
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392518"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Přesun dat do a z Azure Blob Storage pomocí Průzkumníka služby Azure Storage
Průzkumník služby Azure Storage je bezplatný nástroj od Microsoftu, která umožňuje pracovat s daty Azure Storage ve Windows, macOS a Linuxu. Toto téma popisuje, jak ji používat k nahrávání a stahování dat z Azure blob storage. Nástroj si můžete stáhnout z [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který byl nastaven pomocí skriptů poskytovaných [Data Science virtuální počítače v Azure](virtual-machines.md), pak na virtuálním počítači je již nainstalována Průzkumníka služby Azure Storage.
> 
> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahrávání a stahování dat, musíte znát vaše služby Azure storage účtu název a klíč účtu. 

* Nastavení předplatného služby Azure, najdete v článku [zkušební verze na měsíc zdarma](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a účet a informace o klíči najdete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md). Poznamenejte si přístupový klíč účtu úložiště potřebujete tento klíč k připojení k účtu pomocí nástroje Průzkumník služby Azure Storage.
* Nástroj Průzkumník služby Azure Storage je možné stáhnout z [Microsoft Azure Storage Explorer](http://storageexplorer.com/). Přijměte výchozí hodnoty během instalace.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Pomocí Průzkumníka služby Azure Storage
Následující kroky dokumentu jak nahrávání a stahování dat pomocí Průzkumníka služby Azure Storage. 

1. Spusťte Průzkumníka služby Microsoft Azure Storage.
2. Zobrazí se **přihlásit ke svému účtu...**  průvodce, vyberte **nastavení účtu Azure** ikonu, pak **přidat účet** a zadejte přihlašovací údaje. ![Přidání účtu služby Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Zobrazí se **připojit ke službě Azure Storage** průvodce, vyberte **připojit ke službě Azure storage** ikonu. ![Připojení k Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Zadejte přístupový klíč z vašeho účtu úložiště Azure **připojit ke službě Azure Storage** průvodce a pak **Další**. ![Připojení k Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Zadejte název účtu úložiště **název účtu** a potom vyberte **Další**. ![Připojit externí úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Přidání účtu úložiště by měl nyní uveden. Vytvořte kontejner objektů blob v účtu úložiště, klikněte pravým tlačítkem myši **kontejnery objektů Blob** uzlu v příslušném účtu, vyberte **vytvořit kontejner objektů Blob**a zadejte název.
7. Jak nahrát data do kontejneru, vyberte cílový kontejner a klikněte na tlačítko **nahrát** tlačítko.![ Účty úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klikněte na **...**  napravo od **soubory** , vyberte jeden nebo více souborů ze systému souborů a klikněte na **nahrát** zahajte nahrávání souborů.![ Nahrání souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Chcete-li stáhnout data výběru objektu blob v kontejneru odpovídající ke stažení a klikněte na tlačítko **Stáhnout**. ![Stažení souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

