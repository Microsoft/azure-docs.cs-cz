---
title: Přesun dat úložiště objektů Blob pomocí Průzkumníka služby Azure Storage – vědecké zpracování týmových dat
description: Naučte se, jak pomocí Průzkumník služby Azure Storage nahrávat a stahovat data z úložiště objektů BLOB v Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720907"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Přesun dat do a z Azure Blob Storage pomocí Průzkumníka služby Azure Storage
Průzkumník služby Azure Storage je bezplatný nástroj od Microsoftu, která umožňuje pracovat s daty Azure Storage ve Windows, macOS a Linuxu. Toto téma popisuje, jak ji používat k nahrávání a stahování dat z Azure blob storage. Nástroj lze stáhnout z [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který jste nastavili pomocí skriptů poskytovaných [virtuálními počítači pro datové vědy v Azure](virtual-machines.md), Průzkumník služby Azure Storage je už na virtuálním počítači nainstalovaný.
> 
> [!NOTE]
> Úplný Úvod do úložiště objektů BLOB v Azure najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Služba Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Předpoklady
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním/stažením dat musíte znát název svého účtu Azure Storage a klíč účtu. 

* Pokud chcete nastavit předplatné Azure, přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a získání informací o účtu a klíči najdete v tématu [informace o Azure Storagech účtech](../../storage/common/storage-create-storage-account.md). Poznamenejte si přístupový klíč účtu úložiště potřebujete tento klíč k připojení k účtu pomocí nástroje Průzkumník služby Azure Storage.
* Nástroj Průzkumník služby Azure Storage lze stáhnout z [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Přijměte výchozí hodnoty během instalace.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Pomocí Průzkumníka služby Azure Storage
Následující kroky dokumentu jak nahrávání a stahování dat pomocí Průzkumníka služby Azure Storage. 

1. Spusťte Průzkumníka služby Microsoft Azure Storage.
2. Přihlaste se **k vašemu účtu...** průvodce, vyberte ikonu **Nastavení účtu Azure** a pak **přidejte účet** a zadejte přihlašovací údaje. 
![přidat účet Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Chcete-li vyvolat průvodce **připojením k Azure Storage** , vyberte ikonu **připojit k Azure Storage** . ![klikněte na připojit k Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. V průvodci **připojit k Azure Storage** zadejte přístupový klíč z účtu Azure Storage a potom klikněte na **Další**. ![zadat přístupový klíč z účtu Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Do pole **název účtu** zadejte název účtu úložiště a pak vyberte **Další**. ![připojit externí úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Měl by se teď zobrazit přidaný účet úložiště. Pokud chcete vytvořit kontejner objektů BLOB v účtu úložiště, klikněte pravým tlačítkem na uzel **kontejnery objektů BLOB** v tomto účtu, vyberte **vytvořit kontejner objektů BLOB**a zadejte název.
7. Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a klikněte na tlačítko **nahrát** .
![Účty úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klikněte na tlačítko **...** napravo od pole **soubory** , vyberte jeden nebo více souborů, které chcete odeslat ze systému souborů, a kliknutím na tlačítko **Odeslat** zahajte nahrávání souborů.![nahrávat soubory](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Chcete-li stáhnout data, vyberte objekt BLOB v odpovídajícím kontejneru ke stažení a klikněte na tlačítko **Stáhnout**. ![stahovat soubory](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

