---
title: 'Import ze služby Azure Blob Storage: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Přečtěte si že toto téma popisuje, jak pomocí importu z úložiště objektů Blob v Azure modulu ve službě Azure Machine Learning číst data z úložiště objektů blob v Azure, aby data můžete použít v experimentu služby machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029682"
---
# <a name="import-from-azure-blob-storage-module"></a>Importovat z modulu Azure Blob Storage

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k čtení dat z úložiště objektů blob v Azure, takže data můžete použít v experimentu služby machine learning.  

Služba objektů Blob v Azure je pro ukládání velkých objemů dat, včetně binární data. Objekty BLOB Azure můžete přistupovat odkudkoli a pomocí protokolu HTTP nebo HTTPS. Ověřování může být potřeba v závislosti na typu úložiště objektů blob. 

- Veřejné objekty BLOB je přístupný, kdokoli, nebo uživatelé, kteří mají adresa URL SAS.
- Soukromé objekty BLOB vyžadují přihlašovací údaje a přihlašovací údaje.

Import z úložiště objektů blob vyžaduje, aby se data uložená v objektech BLOB, které používají **objektů blob bloku** formátu. Soubory uložené v objektu blob musí používat oddělený čárkami (CSV) nebo formáty oddělené tabulátorem (TSV). Při čtení souboru záznamy a všechny příslušné atribut záhlaví jsou načtena jako řádky do paměti jako datovou sadu.


Důrazně doporučujeme Profilovat vaše data před importem, abyste měli jistotu, že schéma je podle očekávání. Proces importu prohledá některé počet řádků head k určení schématu, ale novější řádků může obsahovat další sloupce nebo data, která způsobí chyby.



## <a name="manually-set-properties-in-the-import-data-module"></a>Ručně nastavit vlastnosti v modulu Import dat

Následující kroky popisují, jak ručně nakonfigurovat zdroj pro import.

1. Přidat **Import dat** modulu do experimentu. Tento modul můžete najít v rozhraní, v **datový vstup a výstup**

2. Pro **zdroj dat**vyberte **Azure Blob Storage**.

3. Pro **typ ověřování**, zvolte **veřejné (adresa URL SAS)** Pokud víte, že jako veřejný zdroj dat byl poskytnut informace. SAS URL je adresa URL časově omezenou pro veřejný přístup, který můžete vygenerovat pomocí nástroje pro Azure storage.

    V opačném případě zvolte **účet**.

4. Pokud jsou vaše data v **veřejné** objekt blob, který je přístupný pomocí adresy URL SAS, nepotřebujete další přihlašovací údaje, protože řetězce adresy URL obsahuje všechny informace potřebné pro stahování a ověřování.

    V **URI** pole zadejte nebo vložte úplný identifikátor URI, který definuje účet a veřejných objektů blob.



5. Pokud jsou vaše data v **privátní** účtu, je nutné zadat přihlašovací údaje, včetně názvu účtu a klíč.

    - Pro **název účtu**, zadejte nebo vložte název účtu, který obsahuje objekt blob, které chcete získat přístup.

        Pokud je úplná adresa URL účtu úložiště, například `http://myshared.blob.core.windows.net`, zadali byste `myshared`.

    - Pro **klíč účtu**, vložte přístupový klíč úložiště, která je přidružená k účtu.

        Pokud si nejste jisti přístupový klíč, najdete v části, "Manage účtům Azure storage" v tomto článku: [Informace o účtech Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Pro **cesta ke kontejneru, adresáře nebo objekt blob**, zadejte název konkrétní objekt blob, který chcete načíst.

    Například, pokud jste nahráli soubor s názvem **data01.csv** do kontejneru **trainingdata** v rámci účtu s názvem **mymldata**, by být úplná adresa URL k souboru: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Proto se v poli **cesta ke kontejneru, adresáře nebo objekt blob**, zadali byste: `trainingdata/data01.csv`

    Pokud chcete importovat více souborů, můžete použít zástupné znaky `*` (hvězdička) nebo `?` (otazník).

    Například za předpokladu, že kontejner `trainingdata` obsahuje více souborů z kompatibilním formátu, můžete použít následující specifikace ke čtení všech souborů, začínající souborem `data`a zřetězení do jedné datové sady:

    `trainingdata/data*.csv`

    Zástupné znaky nelze použít v názvu kontejneru. Pokud je potřeba importovat soubory z několika kontejnerů, použijte samostatnou instanci **importovat Data** modul pro každý kontejner a pak sloučení datových sad pomocí [přidat řádky](./add-rows.md) modulu.

    > [!NOTE]
    > Pokud jste vybrali možnost **použití mezipaměti výsledky**, všechny změny provedené do souborů v kontejneru nespouštějí aktualizaci dat v testu.

7. Pro **formát souboru objektu Blob**, vyberte možnost, která udává formát dat, která je uložena v objektu blob, tak, aby Azure Machine Learning můžete zpracovávat data odpovídajícím způsobem. Podporují se následující formáty:

    - **CSV**: Hodnot oddělených čárkami (CSV) jsou výchozí formát úložiště pro export a import souborů ve službě Azure Machine Learning. Je-li data již obsahuje řádek záhlaví, nezapomeňte vybrat možnost, **soubor má řádek záhlaví**, nebo hlavičku, bude zacházeno jako data v řádku.

       

    - **TSV**: Hodnoty oddělené tabulátorem (TSV) jsou formátu, který používá mnoho nástrojů strojového učení. Je-li data již obsahuje řádek záhlaví, nezapomeňte vybrat možnost, **soubor má řádek záhlaví**, nebo hlavičku, bude zacházeno jako data v řádku.

       

    - **ARFF**: Tento formát podporuje import souborů v formát používaný čtečkou Weka sady nástrojů. 

   

8. Spusťte experiment.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 