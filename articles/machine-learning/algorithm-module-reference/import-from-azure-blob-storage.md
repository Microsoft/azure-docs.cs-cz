---
title: 'Import z Azure Blob Storage: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Přečtěte si toto téma popisuje, jak pomocí modulu import z Azure Blob Storage v rámci služby Azure Machine Learning číst data z úložiště objektů BLOB v Azure, abyste mohli používat data v kanálu strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f8d23bfbee6d3665d770d8cbbcb9440827a88e8e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693743"
---
# <a name="import-from-azure-blob-storage-module"></a>Import z modulu Azure Blob Storage

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Tento modul slouží ke čtení dat z úložiště objektů BLOB v Azure, abyste mohli používat data v kanálu Machine Learning.  

Služba objektů BLOB v Azure slouží k ukládání velkých objemů dat, včetně binárních dat. K objektům blob Azure se dá dostat odkudkoli a použít buď HTTP, nebo HTTPS. V závislosti na typu úložiště objektů BLOB může být vyžadováno ověřování. 

- K veřejným objektům blob může přistupovat kdokoli nebo uživatelé, kteří mají adresu URL SAS.
- Privátní objekty blob vyžadují přihlašovací údaje a přihlašovací údaje.

Import z úložiště objektů BLOB vyžaduje, aby byla data uložená v objektech blob, které používají formát **objektů blob bloku** . Soubory uložené v objektu BLOB musí používat formáty TSV (CSV) nebo oddělený tabulátorem (TSV). Při čtení souboru jsou záznamy a příslušné záhlaví atributů načteny jako řádky do paměti jako datová sada.


Důrazně doporučujeme profilovat data před importem, abyste se ujistili, že je schéma očekávaným způsobem. Proces importu kontroluje určitý počet řádků hlavní řádky a určí schéma, ale pozdější řádky mohou obsahovat nadbytečné sloupce nebo data, která způsobují chyby.



## <a name="manually-set-properties-in-the-import-data-module"></a>Ručně nastavit vlastnosti v modulu import dat

Následující postup popisuje, jak ručně nakonfigurovat zdroj importu.

1. Přidejte do svého kanálu modul **Import dat** . Tento modul můžete najít v rozhraní, **vstup a výstup dat**

2. V případě **zdroje dat**vyberte **Azure Blob Storage**.

3. Pokud víte, že byly informace k dispozici jako veřejný zdroj dat, vyberte jako **typ ověřování**možnost **veřejné (SAS adresa URL)** . Adresa URL SAS je časově vázaná adresa URL pro veřejný přístup, kterou můžete vygenerovat pomocí nástroje Azure Storage.

    V opačném případě vyberte možnost **účet**.

4. Pokud jsou vaše data ve **veřejném** objektu blob, ke kterému se dá získat přístup pomocí adresy URL SAS, nepotřebujete další přihlašovací údaje, protože řetězec adresy URL obsahuje všechny informace, které jsou potřeba ke stažení a ověření.

    Do pole **identifikátor URI** zadejte nebo vložte úplný identifikátor URI, který definuje účet a veřejný objekt BLOB.



5. Pokud jsou vaše data v **privátním** účtu, musíte zadat přihlašovací údaje včetně názvu účtu a klíče.

    - Do **pole název účtu**zadejte nebo vložte název účtu, který obsahuje objekt blob, ke kterému chcete získat přístup.

        Pokud je například úplná adresa URL účtu úložiště `http://myshared.blob.core.windows.net`, zadáte `myshared`.

    - Do pole **klíč účtu**vložte přístupový klíč k úložišti, který je přidružený k účtu.

        Pokud přístupový klíč neznáte, přečtěte si část Správa účtů služby Azure Storage v tomto článku: [informace o Azure Storagech účtech](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. **Do pole cesta k kontejneru, adresáři nebo objektu BLOB**zadejte název konkrétního objektu blob, který chcete načíst.

    Pokud jste například nahráli soubor s názvem **data01. csv** do kontejneru **trainingdata** v účtu s názvem **mymldata**, bude úplná adresa URL pro tento soubor: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    Proto zadejte v cestě pole **k kontejneru, adresáři nebo objektu BLOB**: `trainingdata/data01.csv`

    Chcete-li importovat více souborů, můžete použít zástupné znaky `*` (hvězdička) nebo `?` (otazník).

    Například za předpokladu, že `trainingdata` kontejnerů obsahuje více souborů kompatibilního formátu, můžete použít následující specifikaci ke čtení všech souborů počínaje `data` a jejich zřetězení do jedné datové sady:

    `trainingdata/data*.csv`

    V názvech kontejnerů nemůžete použít zástupné znaky. Pokud potřebujete importovat soubory z více kontejnerů, použijte samostatnou instanci modulu **Import dat** pro každý kontejner a potom slučte datové sady pomocí modulu [Přidat řádky](./add-rows.md) .

    > [!NOTE]
    > Pokud jste vybrali možnost, **použijte výsledky uložené v mezipaměti**, všechny změny provedené v souborech v kontejneru neaktivují aktualizaci dat v kanálu.

7. V případě **formátu souboru BLOB**vyberte možnost, která určuje formát dat uložených v objektu blob, aby Azure Machine Learning mohl data zpracovat odpovídajícím způsobem. Podporovány jsou následující formáty:

    - **CSV**: hodnoty oddělené čárkami (CSV) jsou výchozím formátem úložiště pro export a import souborů v Azure Machine Learning. Pokud již data obsahují řádek záhlaví, nezapomeňte vybrat možnost, **soubor má řádek záhlaví**, jinak bude záhlaví považováno za řádek dat.

       

    - **TSV**: hodnoty oddělené tabulátory (TSV) jsou formáty používané mnoha nástroji pro strojové učení. Pokud již data obsahují řádek záhlaví, nezapomeňte vybrat možnost, **soubor má řádek záhlaví**, jinak bude záhlaví považováno za řádek dat.

       

    - **Arff**: Tento formát podporuje import souborů ve formátu používaném sadou nástrojů weka. 

   

8. Spuštění kanálu


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 