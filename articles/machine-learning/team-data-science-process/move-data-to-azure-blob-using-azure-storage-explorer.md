---
title: Přesunutí dat služby Blob Storage pomocí procesu Průzkumník služby Azure Storage – tým pro datové vědy
description: Naučte se, jak pomocí Průzkumník služby Azure Storage nahrávat a stahovat data z Azure Blob Storage.
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
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788905"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Přesun dat do a z Azure Blob Storage pomocí Průzkumník služby Azure Storage
Průzkumník služby Azure Storage je bezplatný nástroj od Microsoftu, který umožňuje pracovat s Azure Storagemi daty v systémech Windows, macOS a Linux. Toto téma popisuje, jak ho použít k nahrání a stažení dat z Azure Blob Storage. Nástroj lze stáhnout z [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který jste nastavili pomocí skriptů poskytovaných [virtuálními počítači pro datové vědy v Azure](../data-science-virtual-machine/overview.md), Průzkumník služby Azure Storage je už na virtuálním počítači nainstalovaný.
> 
> [!NOTE]
> Úplný Úvod do Azure Blob Storage najdete v tématu [základy Azure Blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md) a [Služba Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Předpoklady
V tomto dokumentu se předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním/stažením dat musíte znát název svého účtu Azure Storage a klíč účtu. 

* Pokud chcete nastavit předplatné Azure, přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a získání informací o účtu a klíči najdete v tématu [informace o Azure Storagech účtech](../../storage/common/storage-account-create.md). Poznamenejte si přístupový klíč účtu úložiště, protože tento klíč potřebujete k připojení k účtu pomocí nástroje pro Průzkumník služby Azure Storage.
* Nástroj Průzkumník služby Azure Storage lze stáhnout z [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Přijměte výchozí hodnoty během instalace.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Použití Průzkumníka služby Azure Storage
Následující postup popisuje, jak nahrávat a stahovat data pomocí Průzkumník služby Azure Storage. 

1. Spusťte Průzkumník služby Microsoft Azure Storage.
2. Přihlaste se **k vašemu účtu...** průvodce, vyberte ikonu **Nastavení účtu Azure** a pak **přidejte účet** a zadejte přihlašovací údaje. 
![Přidat účet Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Chcete-li vyvolat průvodce **připojením k Azure Storage** , vyberte ikonu **připojit k Azure Storage** . ![Klikněte na připojit k Azure Storage.](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. V průvodci **připojit k Azure Storage** zadejte přístupový klíč z účtu Azure Storage a potom klikněte na **Další**. ![Zadat přístupový klíč z Azure Storage účtu](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Do pole **název účtu** zadejte název účtu úložiště a pak vyberte **Další**. ![Připojit externí úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Měl by se teď zobrazit přidaný účet úložiště. Pokud chcete vytvořit kontejner objektů BLOB v účtu úložiště, klikněte pravým tlačítkem na uzel **kontejnery objektů BLOB** v tomto účtu, vyberte **vytvořit kontejner objektů BLOB** a zadejte název.
7. Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a klikněte na tlačítko **nahrát** .
![Účty úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klikněte na tlačítko **...** napravo od pole **soubory** , vyberte jeden nebo více souborů, které chcete odeslat ze systému souborů, a kliknutím na tlačítko **Odeslat** zahajte nahrávání souborů. ![ Nahrání souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Chcete-li stáhnout data, vyberte objekt BLOB v odpovídajícím kontejneru ke stažení a klikněte na tlačítko **Stáhnout**. ![Stažení souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
