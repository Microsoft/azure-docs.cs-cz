---
title: Přesunutí dat úložiště objektů blob pomocí Průzkumníka úložiště Azure – proces vědecké ho zpracování týmových dat
description: Zjistěte, jak pomocí Azure Storage Exploreru nahrávat a stahovat data z úložiště objektů blob Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720907"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Přesunutí dat do úložiště objektů blob Azure a z ní pomocí Azure Storage Exploreru
Azure Storage Explorer je bezplatný nástroj od Microsoftu, který vám umožní pracovat s daty Azure Storage ve Windows, macOS a Linuxu. Toto téma popisuje, jak ji použít k nahrávání a stahování dat z úložiště objektů blob Azure. Nástroj lze stáhnout z [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Pokud používáte virtuální počítač, který byl nastaven se skripty poskytovanými [virtuálními počítači pro datové vědy v Azure](virtual-machines.md), pak se na virtuálním počítači už nainstaluje Průzkumník a údržby Azure.
> 
> [!NOTE]
> Úplný úvod do úložiště objektů blob Azure najdete v části [Základy objektů blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním nebo stahováním dat musíte znát název účtu azure storage a klíč účtu. 

* Pokud chcete nastavit předplatné Azure, [přečtěte](https://azure.microsoft.com/pricing/free-trial/)si bezplatné měsíční zkušební verzi .
* Pokyny k vytvoření účtu úložiště a k získání informací o účtu a klíči najdete [v tématu O účtech Azure Storage](../../storage/common/storage-create-storage-account.md). Poznamenejte si přístupový klíč pro váš účet úložiště, protože tento klíč potřebujete pro připojení k účtu pomocí nástroje Azure Storage Explorer.
* Nástroj Průzkumník a ukládání Azure lze stáhnout z [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com/). Přijměte výchozí hodnoty během instalace.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Použití Průzkumníka úložiště Azure
Následující kroky dokumentují, jak nahrát nebo stáhnout data pomocí Průzkumníka úložiště Azure. 

1. Spusťte Průzkumníka úložiště Microsoft Azure.
2. Pokud chcete vyvolat průvodce **Přihlásit se ke svému účtu...** vyberte ikonu **Nastavení účtu Azure,** pak **přidejte účet** a zadejte přihlašovací údaje. 
![Přidání účtu Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Pokud chcete vyvolat Průvodce **připojením k úložišti Azure,** vyberte ikonu **Připojit k úložišti Azure.** ![Klikněte na "Připojit k úložišti Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Zadejte přístupový klíč ze svého účtu Azure Storage v průvodci **Připojit k úložišti Azure** a pak **Další**. ![Zadání přístupového klíče z účtu Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Do pole Název **účtu** zadejte název účtu úložiště a pak vyberte **Další**. ![Připojení externího úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Přidaný účet úložiště by měl být nyní zobrazen. Pokud chcete vytvořit kontejner objektů blob v účtu úložiště, klikněte pravým tlačítkem myši na uzel **Kontejnery objektů blob** v tomto účtu, vyberte **vytvořit kontejner objektů blob**a zadejte název.
7. Chcete-li nahrát data do kontejneru, vyberte cílový kontejner a klepněte na tlačítko **Odeslat.**
![Účty úložiště](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klikněte na **...** na pravé straně pole **Soubory,** vyberte jeden nebo více souborů nahrát ze systému souborů a klepněte na tlačítko **Nahrát** začít nahrávat soubory. ![Nahrávání souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Chcete-li stáhnout data, vyberte objekt blob v odpovídajícím kontejneru ke stažení a klepněte na tlačítko **Stáhnout**. ![Stažení souborů](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

