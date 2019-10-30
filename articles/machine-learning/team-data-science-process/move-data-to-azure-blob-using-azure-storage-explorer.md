---
title: Přesunutí dat služby Blob Storage pomocí procesu Průzkumník služby Azure Storage – tým pro datové vědy
description: Naučte se, jak pomocí Průzkumník služby Azure Storage nahrávat a stahovat data z úložiště objektů BLOB v Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053959"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Přesun dat do a z Azure Blob Storage pomocí Průzkumník služby Azure Storage
Průzkumník služby Azure Storage je bezplatný nástroj od Microsoftu, který umožňuje pracovat s Azure Storagemi daty v systémech Windows, macOS a Linux. Toto téma popisuje, jak ho použít k nahrání a stažení dat z úložiště objektů BLOB v Azure. Nástroj lze stáhnout z [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který jste nastavili pomocí skriptů poskytovaných [virtuálními počítači pro datové vědy v Azure](virtual-machines.md), Průzkumník služby Azure Storage je už na virtuálním počítači nainstalovaný.
> 
> [!NOTE]
> Úplný Úvod do úložiště objektů BLOB v Azure najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Služba Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Předpoklady
V tomto dokumentu se předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním/stažením dat musíte znát název účtu úložiště Azure a klíč účtu. 

* Pokud chcete nastavit předplatné Azure, přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a získání informací o účtu a klíčích najdete v tématu [informace o účtech Azure Storage](../../storage/common/storage-create-storage-account.md). Poznamenejte si přístupový klíč účtu úložiště, protože tento klíč potřebujete k připojení k účtu pomocí nástroje pro Průzkumník služby Azure Storage.
* Nástroj Průzkumník služby Azure Storage lze stáhnout z [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Přijměte výchozí hodnoty během instalace.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Použít Průzkumník služby Azure Storage
Následující postup popisuje, jak nahrávat a stahovat data pomocí Průzkumník služby Azure Storage. 

1. Spusťte Průzkumník služby Microsoft Azure Storage.
2. Přihlaste se **k vašemu účtu...** průvodce, vyberte ikonu **Nastavení účtu Azure** a pak **přidejte účet** a zadejte přihlašovací údaje. ![přidat účet úložiště Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Pokud chcete vyvolat průvodce **připojením k Azure Storage** , vyberte ikonu **připojit k Azure Storage** . ![klikněte na připojit k úložišti Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. V průvodci **připojit k Azure Storage** zadejte přístupový klíč z účtu služby Azure Storage a potom klikněte na **Další**. ![zadat přístupový klíč z účtu Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Do pole **název účtu** zadejte název účtu úložiště a pak vyberte **Další**. ![připojit externí úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Přidaný účet úložiště by se teď měl zobrazit v seznamu. Pokud chcete vytvořit kontejner objektů BLOB v účtu úložiště, klikněte pravým tlačítkem na uzel **kontejnery objektů BLOB** v tomto účtu, vyberte **vytvořit kontejner objektů BLOB**a zadejte název.
7. Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a klikněte na tlačítko **nahrát** . účty úložiště![](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klikněte na tlačítko **...** napravo od pole **soubory** , vyberte jeden nebo více souborů, které chcete odeslat ze systému souborů, a kliknutím na tlačítko **Odeslat** zahajte nahrávání souborů.![nahrávat soubory](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Chcete-li stáhnout data, vyberte objekt BLOB v odpovídajícím kontejneru ke stažení a klikněte na tlačítko **Stáhnout**. ![stahovat soubory](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

