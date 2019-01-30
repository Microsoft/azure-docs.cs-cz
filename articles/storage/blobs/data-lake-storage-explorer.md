---
title: 'Rychlý start: Pomocí Průzkumníka služby Azure Storage ke správě dat v Azure Data Lake Storage Gen2 '
description: V tomto rychlém startu se dozvíte, jak vytvořit a systém souborů v účtu Azure Data Lake Storage Gen2, stejně jako adresář a soubor pomocí Průzkumníka služby Azure Storage. V dalším kroku se dozvíte, jak stáhnout soubor do místního počítače a postup zobrazení celého souboru v adresáři.
services: storage
author: tamram
ms.subservice: data-lake-storage-gen2
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: tamram
ms.openlocfilehash: 6f3aa81f0fa2497db02d069e93e007b43c37beb9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250448"
---
# <a name="quickstart-use-azure-storage-explorer-to-manage-data-in-an-azure-data-lake-storage-gen2-account"></a>Rychlý start: Pomocí Průzkumníka služby Azure Storage ke správě dat v účtu služby Azure Data Lake Storage Gen2

V tomto rychlém startu se dozvíte, jak používat [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) k vytvoření adresáře a objektu blob. V dalším kroku se dozvíte, jak stáhnout objekt blob do místního počítače a postup zobrazení všech objektů BLOB v adresáři. Také se dozvíte, jak vytvořit snímek objektu blob, spravovat zásady přístupu k adresáři a vytvořit sdílený přístupový podpis.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Tento rychlý start vyžaduje instalaci Průzkumníka služby Azure Storage. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Přihlášení do Průzkumníka služby Storage

Při prvním spuštění se objeví okno **Průzkumník služby Microsoft Azure Storage – Připojení**. Zatímco Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště, pouze jedním ze způsobů aktuálně podporovány pro správu seznamů řízení přístupu.

|Úkol|Účel|
|---|---|
|Přidání účtu Azure | Přesměruje vás na přihlašovací stránku vaší organizace, kde budete moci ověřit svůj přístup do Azure. Aktuálně to je metoda pouze podporované metody ověřování, pokud chcete spravovat a nastavit seznamy ACL. |

Vyberte **Přidat účet Azure** a klikněte na tlačítko **Přihlásit...** Podle pokynů na obrazovce se přihlaste ke svému účtu Azure.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. V tomto zobrazení uvidíte místní úložiště i přehled všech svých účtů úložiště Azure nakonfigurovaných pomocí účtů [emulátoru úložiště Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo prostředí [Azure Stack](../../azure-stack/user/azure-stack-storage-connect-se.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-filesystem"></a>Vytvořit systém souborů

Objekty BLOB se vždy nahrávají do adresáře. Díky tomu můžete organizovat skupiny objektů blob podobně, jako organizujete soubory do složek na svém počítači.

K vytvoření adresáře, rozbalte účet úložiště, který jste vytvořili v předchozím kroku. Vyberte **kontejner objektů Blob**klikněte pravým tlačítkem a vyberte **kontejner objektů Blob vytvořit**. Zadejte název pro váš systém souborů. Jakmile budete hotovi, stisknutím klávesy **Enter** vytvořit systém souborů. Po úspěšném vytvoření adresáře objektů blob se zobrazí v části **kontejner objektů Blob** složku pro vybraný účet úložiště.

![Průzkumník služby Storage Microsoft Azure – vytvoření a systém souborů](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Nahrání objektů BLOB do adresáře

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Soubory VHD, které se používají pro virtuální počítače IaaS, jsou objekty blob stránky. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina souborů uložených v úložišti objektů blob je objekty blob bloku.

Na pásu karet adresáře vyberte **nahrát**. Tato operace umožňuje nahrát složku nebo soubor.

Vyberte soubory nebo složku k nahrání. Vyberte **typ blobu**. K dispozici jsou možnosti **Připojit**, **Stránka** nebo **Blok**.

Pokud chcete nahrát soubor .vhd nebo .vhdx, zvolte **Nahrát soubory .vhd/.vhdx jako objekty blob stránky (doporučeno)**.

V **nahrát do složky (volitelné)** pole buď název složky pro uložení souborů nebo složky v adresáři. Pokud nevyberete žádnou složku, soubory se nahrávají přímo v adresáři.

![Průzkumník služby Microsoft Azure Storage – nahrání objektu blob](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Když vyberete **OK**, vybrané soubory se zařadí do fronty a postupně se nahrají. Po dokončení nahrávání se výsledky zobrazí v okně **Aktivity**.

## <a name="view-blobs-in-a-directory"></a>Zobrazení objektů BLOB v adresáři

V **Průzkumníka služby Azure Storage** vyberte adresář v rámci účtu úložiště. V hlavním podokně se seznamem objektů BLOB ve vybraném adresáři.

![Průzkumník služby Microsoft Azure Storage – výpis objektů BLOB v adresáři](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Když chcete stáhnout objekty blob pomocí **Průzkumníka služby Azure Storage**, vyberte objekt blob a pak na pásu karet **Stáhnout**. Otevře se dialogové okno pro výběr souboru, kde můžete zadat umístění a název staženého souboru. Výběrem **Uložit** zahájíte stahování objektu blob do místní složky.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se naučili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí **Průzkumníka služby Azure Storage**. Další informace o tom, jak nastavit seznamy ACL pro soubory a adresáře, i nadále naše postupy k tomuto tématu.

> [!div class="nextstepaction"]
> [Jak nastavit seznamy ACL pro soubory a adresáře](data-lake-storage-how-to-set-permissions-storage-explorer.md)
