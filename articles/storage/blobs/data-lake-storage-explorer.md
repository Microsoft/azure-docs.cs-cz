---
title: Použití Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2
description: Pomocí Průzkumník služby Azure Storage můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652271"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Správa adresářů a souborů v Azure Data Lake Storage Gen2 pomocí Průzkumník služby Azure Storage

V tomto článku se dozvíte, jak pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) vytvářet a spravovat adresáře a soubory v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](../common/storage-account-create.md) pokynů.

- Průzkumník služby Azure Storage nainstalované na místním počítači. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Průzkumník služby Storage využívá při práci s Azure Data Lake Storage Gen2 i [koncové body](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) objektů BLOB (blob) & Data Lake Storage Gen2 (DFS). Pokud je přístup k Azure Data Lake Storage Gen2 nakonfigurovaný pomocí privátních koncových bodů, zajistěte, aby se pro účet úložiště vytvořily dva privátní koncové body: jeden s cílovým dílčím prostředkem `blob` a druhý s cílovým dílčím prostředkem `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Přihlášení k Průzkumník služby Storage

Když poprvé spustíte Průzkumníka služby Storage, objeví se okno **Průzkumník služby Microsoft Azure Storage – Připojení**. I když Průzkumník služby Storage poskytuje několik způsobů, jak se připojit k účtům úložiště, v současné době se podporuje jenom jeden způsob správy seznamů ACL.

|Úkol|Účel|
|---|---|
|Přidání účtu Azure | Vás přesměruje na přihlašovací stránku vaší organizace a provede ověření v Azure. V současné době se jedná o jedinou podporovanou metodu ověřování, pokud chcete spravovat a nastavovat seznamy ACL.|
|Použití připojovacího řetězce nebo sdíleného přístupového podpisu URI | Ty můžete použít k přímému přístupu ke kontejneru nebo účtu úložiště pomocí tokenu SAS nebo sdíleného připojovacího řetězce. |
|Použití názvu a klíče účtu úložiště| Pro připojení k úložišti Azure můžete použít název a klíč.|

Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se.**.. Podle pokynů na obrazovce se přihlaste k účtu Azure.

![Snímek obrazovky zobrazující Průzkumník služby Microsoft Azure Storage a zvýrazní možnost Přidat účet Azure a tlačítko přihlásit.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. Toto zobrazení vám umožní získat přehled o všech účtech úložiště Azure i o místním úložišti nakonfigurovaném pomocí [emulátoru úložiště Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), účtů [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) prostředí.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner obsahuje adresáře a soubory. Pokud ho chcete vytvořit, rozbalte účet úložiště, který jste vytvořili v kroku pokračování. Vyberte **kontejnery objektů BLOB**, klikněte pravým tlačítkem myši a vyberte **vytvořit kontejner objektů BLOB**. Zadejte název kontejneru. Seznam pravidel a omezení pro pojmenování kontejnerů najdete v části [vytvoření kontejneru](storage-quickstart-blobs-dotnet.md#create-a-container) . Po dokončení stiskněte klávesu **ENTER** a kontejner se vytvoří. Jakmile se kontejner úspěšně vytvoří, zobrazí se ve složce **kontejnery objektů BLOB** pro vybraný účet úložiště.

![Průzkumník služby Microsoft Azure Storage – vytváření kontejneru](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Vytvoření adresáře

Chcete-li vytvořit adresář, vyberte kontejner, který jste vytvořili v kroku pokračování. Na pásu karet kontejner klikněte na tlačítko **Nová složka** . Zadejte název adresáře. Po dokončení stiskněte klávesu **ENTER** a vytvořte adresář. Po úspěšném vytvoření adresáře se zobrazí v okně editoru.

![Průzkumník služby Microsoft Azure Storage – vytváření adresáře](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Nahrajte objekty blob do adresáře.

Na pásu karet Adresář klikněte na tlačítko **nahrát** . Tato operace umožňuje nahrát složku nebo soubor.

Vyberte soubory nebo složku k nahrání.

![Průzkumník služby Microsoft Azure Storage – nahrání objektu blob](media/data-lake-storage-explorer/upload-file.png)

Když vyberete **OK**, vybrané soubory se zařadí do fronty a postupně se nahrají. Po dokončení nahrávání se výsledky zobrazí v okně **Aktivity**.

## <a name="view-blobs-in-a-directory"></a>Zobrazení objektů BLOB v adresáři

V aplikaci **Průzkumník služby Azure Storage** vyberte adresář pod účtem úložiště. Hlavní podokno zobrazuje seznam objektů BLOB ve vybraném adresáři.

![Průzkumník služby Microsoft Azure Storage – vypíše objekty BLOB v adresáři.](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Pokud chcete soubory stáhnout pomocí **Průzkumník služby Azure Storage** s vybraným souborem, na pásu karet vyberte **Stáhnout** . Otevře se dialogové okno pro výběr souboru, kde můžete zadat umístění a název staženého souboru. Výběrem **Uložit** zahájíte stahování souboru do místního umístění.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak spravovat oprávnění k souborům a adresářům nastavením seznamů řízení přístupu (ACL).

> [!div class="nextstepaction"]
> [Správa seznamů ACL v Azure Data Lake Storage Gen2 pomocí Průzkumník služby Azure Storage](./data-lake-storage-explorer-acl.md)
