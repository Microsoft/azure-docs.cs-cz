---
title: 'Exportujte dat: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Export dat ve službě Azure Machine Learning k uložení výsledků, dočasných dat a pracovní data z experimentů do cíle cloudové úložiště mimo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028317"
---
# <a name="export-data-module"></a>Modul exportu dat

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k uložení výsledků, dočasných dat a pracovní data z experimentů do cíle cloudové úložiště mimo Azure Machine Learning.

Tento modul podporuje export nebo ukládání dat do následujících cloudových datových služeb:


- **Export do Azure Blob Storage**: Ukládá data do služby Blob service v Azure. Data ve službě Blob service můžete veřejně sdílené nebo uložen v úložišti dat zabezpečeným aplikace.

  
## <a name="how-to-configure-export-data"></a>Jak nakonfigurovat Export dat

1. Přidat **exportovat Data** modulu do experimentu v rozhraní. V tomto modulu můžete najít **vstupní a výstupní** kategorie.

2. Připojit **exportovat Data** modul, který obsahuje data, kterou chcete exportovat.

3. Dvakrát klikněte na panel **exportovat Data** otevřít **vlastnosti** podokně.

4. Pro **cíl dat**, vyberte typ cloudového úložiště, ve kterém budete zachraňte vaše data. Pokud provedete změny tuto možnost, všechny ostatní vlastnosti se obnoví. Nezapomeňte proto tuto možnost zvolte, nejprve!

5. Poskytují účet název a ověřování metodu vyžadovaných pro přístup k zadanému účtu úložiště.

    **Export do Azure Blob Storage** je jedinou možností ve verzi private preview. Níže ukazuje, jak nastavit modul.
    1. Služba objektů blob v Azure je pro ukládání velkých objemů dat, včetně binární data. Existují dva typy služby blob storage: veřejné objekty BLOB a objekty BLOB, které vyžadují přihlašovací údaje.

    2. Pro **typ ověřování**, zvolte **veřejné (SAS)** Pokud víte, že úložiště podporuje přístup prostřednictvím adresy URL SAS.

          Adresa URL SAS je speciální typ adresy URL, můžete vygenerovat pomocí nástroje pro Azure storage, který je k dispozici pouze po omezenou dobu.  Obsahuje všechny informace potřebné pro ověřování a ke stažení.

        Pro **URI**, zadejte nebo vložte úplný identifikátor URI, který definuje účet a veřejných objektů blob.

        Pro formát souborů CSV a TSV jsou podporovány.

    3. Soukromé účty, zvolte **účet**a zadejte název účtu a klíč účtu tak, aby se experiment můžete zapisovat do účtu úložiště.

         - **Název účtu**: Zadejte nebo vložte název účtu, ve které chcete uložit data. Pokud je úplná adresa URL účtu úložiště, například `http://myshared.blob.core.windows.net`, zadali byste `myshared`.

        - **Klíč účtu**: Vložte přístupový klíč úložiště, která je přidružená k účtu.

        -  **Cesta ke kontejneru, adresáře nebo objekt blob**: Zadejte název objektu blob, kde bude uložena exportovaná data. Například pro ukládání výsledků experimentů na nový objekt blob s názvem **results01.csv** v kontejneru **předpovědi** v rámci účtu s názvem **mymldata**, úplná adresa URL pro objekt BLOB by `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Proto se v poli **cesta ke kontejneru, adresáře nebo objekt blob**, zadali byste kontejner a následuje název objektu blob jako: `predictions/results01.csv`

        - Pokud zadáte název objektu blob, který ještě neexistuje, pro vás vytvoří objekt blob Azure.

       -  Při zápisu do existující objekt blob, můžete určit, že aktuální obsah objektu blob tak, že nastavíte vlastnost, přepsán **režimu zápisu do úložiště objektů blob v Azure**. Ve výchozím nastavení, tato vlastnost nastavena na **chyba**, což znamená, že je vyvolána chyba pokaždé, když je nalezena existující soubor objektu blob se stejným názvem.


    4. Pro **formát souboru pro soubor blob**, vyberte formát, ve kterém by měla být uložena data.

        - **CSV**: Hodnot oddělených čárkami (CSV) jsou výchozí formát úložiště. Pokud chcete exportovat záhlaví sloupců společně s daty, vyberte možnost, **řádek záhlaví zápisu objektů blob**.  Další informace o čárkou oddělený formát použitý ve službě Azure Machine Learning najdete v tématu [převést na sdílený svazek clusteru](./convert-to-csv.md).

        - **TSV**: Formát hodnoty oddělené tabulátorem (TSV) není kompatibilní s celou řadu nástrojů machine learning. Pokud chcete exportovat záhlaví sloupců společně s daty, vyberte možnost, **řádek záhlaví zápisu objektů blob**.  

 
    5. **Použít výsledky uložené v mezipaměti**: Tuto možnost vyberte, pokud chcete se vyhnout přepisování výsledky do souboru objektu blob při každém spuštění testu. Pokud nejsou žádné další změny parametry modulu, experiment zapisuje výsledky pouze při prvním spuštění modulu, nebo pokud existují změny v datech.

    6. Spusťte experiment.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 