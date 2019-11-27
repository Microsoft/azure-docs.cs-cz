---
title: Použití Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2
description: Naučte se, jak pomocí Průzkumník služby Azure Storage vytvořit systém souborů v účtu Azure Data Lake Storage Gen2 a také v adresáři a v souboru. V dalším kroku se dozvíte, jak stáhnout soubor do místního počítače a jak zobrazit celý soubor v adresáři.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b300d96408bed621a0687c04a9c94021af009f95
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484474"
---
# <a name="use-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Použití Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2

V tomto článku se naučíte, jak pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) vytvořit adresář a objekt BLOB. V dalším kroku se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit všechny objekty BLOB v adresáři. Naučíte se také, jak vytvořit snímek objektu blob, jak spravovat zásady přístupu k adresářům a vytvořit sdílený přístupový podpis.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Tento rychlý start vyžaduje instalaci Průzkumníka služby Azure Storage. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Přihlášení k Průzkumník služby Storage

Při prvním spuštění se objeví okno **Průzkumník služby Microsoft Azure Storage – Připojení**. I když Průzkumník služby Storage poskytuje několik způsobů, jak se připojit k účtům úložiště, v současné době se podporuje jenom jeden způsob správy seznamů ACL.

|Úkol|Účel|
|---|---|
|Přidání účtu Azure | Přesměruje vás na přihlašovací stránku vaší organizace, kde budete moci ověřit svůj přístup do Azure. V současné době se jedná o jedinou podporovanou metodu ověřování, pokud chcete spravovat a nastavovat seznamy ACL. |

Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se.** .. Podle pokynů na obrazovce se přihlaste k účtu Azure.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. V tomto zobrazení uvidíte místní úložiště i přehled všech svých účtů úložiště Azure nakonfigurovaných pomocí účtů [emulátoru úložiště Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo prostředí [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob jsou vždy nahrány do adresáře. Díky tomu můžete organizovat skupiny objektů blob podobně, jako organizujete soubory do složek na svém počítači.

Chcete-li vytvořit adresář, rozbalte účet úložiště, který jste vytvořili v kroku pokračování. Vyberte **kontejner objektů BLOB**, klikněte pravým tlačítkem a vyberte **vytvořit kontejner objektů BLOB**. Zadejte název kontejneru. Po dokončení stiskněte klávesu **ENTER** a kontejner se vytvoří. Jakmile se adresář objektů BLOB úspěšně vytvoří, zobrazí se ve složce **kontejneru objektů BLOB** pro vybraný účet úložiště.

![Průzkumník služby Microsoft Azure Storage – vytváření kontejneru](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Nahrajte objekty blob do adresáře.

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Soubory VHD, které se používají pro virtuální počítače IaaS, jsou objekty blob stránky. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina souborů uložených v úložišti objektů blob je objekty blob bloku.

Na pásu karet adresář vyberte **Odeslat**. Tato operace umožňuje nahrát složku nebo soubor.

Vyberte soubory nebo složku k nahrání. Vyberte **typ blobu**. K dispozici jsou možnosti **Připojit**, **Stránka** nebo **Blok**.

Pokud chcete nahrát soubor .vhd nebo .vhdx, zvolte **Nahrát soubory .vhd/.vhdx jako objekty blob stránky (doporučeno)** .

Do pole **Odeslat do složky (volitelné)** buď uložte název složky, ve které se budou soubory nebo složky ukládat do složky v adresáři. Pokud není zvolena žádná složka, soubory se nahrají přímo v adresáři.

![Průzkumník služby Microsoft Azure Storage – nahrání objektu blob](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Když vyberete **OK**, vybrané soubory se zařadí do fronty a postupně se nahrají. Po dokončení nahrávání se výsledky zobrazí v okně **Aktivity**.

## <a name="view-blobs-in-a-directory"></a>Zobrazení objektů BLOB v adresáři

V aplikaci **Průzkumník služby Azure Storage** vyberte adresář pod účtem úložiště. Hlavní podokno zobrazuje seznam objektů BLOB ve vybraném adresáři.

![Průzkumník služby Microsoft Azure Storage – vypíše objekty BLOB v adresáři.](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Když chcete stáhnout objekty blob pomocí **Průzkumníka služby Azure Storage**, vyberte objekt blob a pak na pásu karet **Stáhnout**. Otevře se dialogové okno pro výběr souboru, kde můžete zadat umístění a název staženého souboru. Výběrem **Uložit** zahájíte stahování objektu blob do místní složky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí **Průzkumníka služby Azure Storage**. Další informace o nastavení seznamů ACL pro vaše soubory a adresáře najdete v našem postupu na tomto předmětu.

> [!div class="nextstepaction"]
> [Postup nastavení seznamů ACL pro soubory a adresáře](data-lake-storage-how-to-set-permissions-storage-explorer.md)
