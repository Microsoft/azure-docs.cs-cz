---
title: 'Exportovat data: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul exportovat data ve službě Azure Machine Learning k uložení výsledků, mezilehlých dat a pracovních dat z vašich kanálů do cílů cloudového úložiště mimo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b7b4b9de1e91279243e35f1b71f1ef6d2244e9e0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693717"
---
# <a name="export-data-module"></a>Exportovat datový modul

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete ukládat výsledky, mezilehlá data a pracovní data z vašich kanálů do cloudového úložiště do míst mimo Azure Machine Learning.

Tento modul podporuje export nebo ukládání dat do následujících cloudových datových služeb:


- **Exportovat do azure BLOB Storage**: ukládá data do BLOB Service v Azure. Data v Blob service lze sdílet veřejně nebo uložit v zabezpečených úložištích dat aplikací.

  
## <a name="how-to-configure-export-data"></a>Jak nakonfigurovat exportovaná data

1. Přidejte modul **Export dat** do kanálu v rozhraní. Tento modul můžete najít v kategorii **vstup a výstup** .

2. Připojte **Export dat** do modulu obsahujícího data, která chcete exportovat.

3. Dvojím kliknutím na **exportovat data** otevřete podokno **vlastnosti** .

4. V části **cíl dat**vyberte typ cloudového úložiště, kam budete ukládat data. Pokud v této možnosti provedete nějaké změny, resetují se všechny ostatní vlastnosti. Proto si nezapomeňte tuto možnost vybrat jako první!

5. Zadejte název účtu a metodu ověřování vyžadované pro přístup k zadanému účtu úložiště.

    Možnost **exportovat do Azure Blob Storage** je jedinou možností v části Private Preview. Níže ukazuje, jak nastavit modul.
    1. Služba objektů BLOB v Azure slouží k ukládání velkých objemů dat, včetně binárních dat. Existují dva typy úložiště objektů BLOB: veřejné objekty BLOB a objekty blob, které vyžadují přihlašovací údaje.

    2. Pokud víte, že úložiště podporuje přístup přes adresu URL SAS, vyberte v poli **typ ověřování**možnost **veřejné (SAS)** .

          Adresa URL SAS je speciální typ adresy URL, který se dá vygenerovat pomocí nástroje Azure Storage a který je dostupný jenom pro omezený čas.  Obsahuje všechny informace, které jsou potřeba pro ověřování a stažení.

        Do pole **identifikátor URI**zadejte nebo vložte úplný identifikátor URI, který definuje účet a veřejný objekt BLOB.

        Pro formát souboru se podporují CSV a TSV.

    3. U privátních účtů vyberte **účet**a zadejte název účtu a klíč účtu, aby kanál mohl zapisovat do účtu úložiště.

         - **Název účtu**: zadejte nebo vložte název účtu, na který chcete data uložit. Pokud je například úplná adresa URL účtu úložiště `http://myshared.blob.core.windows.net`, zadáte `myshared`.

        - **Klíč účtu**: vložte přístupový klíč k úložišti, který je přidružený k účtu.

        -  **Cesta k kontejneru, adresáři nebo objektu BLOB**: zadejte název objektu blob, do kterého se budou ukládat exportovaná data. Pokud chcete například uložit výsledky vašeho kanálu do nového objektu BLOB s názvem **results01. csv** v kontejneru **předpovědi** v účtu s názvem **mymldata**, bude celá adresa URL objektu BLOB `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Proto v **cestě pole k kontejneru, adresáři nebo objektu BLOB**určíte kontejner a název objektu BLOB následujícím způsobem: `predictions/results01.csv`

        - Pokud zadáte název objektu blob, který ještě neexistuje, vytvoří Azure objekt blob za vás.

       -  Když zapisujete do existujícího objektu blob, můžete určit, že aktuální obsah objektu BLOB bude přepsán nastavením vlastnosti, **režimu zápisu Azure Blob Storage**. Ve výchozím nastavení je tato vlastnost nastavena na hodnotu **Chyba**, což znamená, že při každém nalezení existujícího souboru BLOB se stejným názvem dojde k chybě.


    4. Jako **Formát souboru objektu BLOB**vyberte formát, ve kterém se mají ukládat data.

        - **CSV**: hodnoty oddělené čárkami (CSV) jsou výchozím formátem úložiště. Pokud chcete exportovat záhlaví sloupců spolu s daty, vyberte možnost **Zapsat řádek záhlaví objektu BLOB**.  Další informace o formátu odděleném čárkami, který se používá v Azure Machine Learning, najdete v tématu [Převod do sdíleného svazku clusteru](./convert-to-csv.md).

        - **TSV**: formát hodnot oddělených tabulátory (TSV) je kompatibilní s mnoha nástroji pro strojové učení. Pokud chcete exportovat záhlaví sloupců spolu s daty, vyberte možnost **Zapsat řádek záhlaví objektu BLOB**.  

 
    5. **Použít výsledky uložené v mezipaměti**: tuto možnost vyberte, pokud chcete zabránit přepsání výsledků do souboru objektů BLOB při každém spuštění kanálu. Pokud v parametrech modulu nejsou žádné další změny, kanál zapíše výsledky pouze při prvním spuštění modulu nebo v případě, že dojde ke změně dat.

    6. Spuštění kanálu

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 