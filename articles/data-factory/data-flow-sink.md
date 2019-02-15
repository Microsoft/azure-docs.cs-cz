---
title: Azure Data Factory mapování transformace jímky toku dat
description: Azure Data Factory mapování transformace jímky toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 795b8072bbd9b248f982d061d699f490b1b63b17
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271747"
---
# <a name="azure-data-factory-mapping-data-flow-sink-transformation"></a>Azure Data Factory mapování transformace jímky toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Jímka možnosti](media/data-flow/windows1.png "jímky 1")

Po dokončení vaší transformace toku dat můžete jímky Transformovaná data do cílové datové sadě. V transformaci jímky můžete definici datové sady, kterou chcete použít pro určení výstupní data.

Běžnou praxí pro změnu příchozích dat a aby se zohlednily odchylek schématu je do složky bez definované schéma v výstupní datovou sadu jímky výstupní data. Můžete také započítat i všechny změny ve sloupcích ve zdrojích tak, že vyberete "Povolit schématu odchylek" ve zdroji i automaticky mapovat všechna pole v jímce.

Můžete přepsat, připojte nebo selhání toku dat při vnořování do datové sady.

Můžete také "automaticky mapovat" do jímky všechna příchozí pole. Pokud chcete zvolit pole, které chcete do jímky do cíle, nebo pokud chcete změnit názvy polí v cílovém umístění, zvolte "Off" pro "automaticky mapovat" a potom klikněte na kartu mapování a mapování polí výstup:

![Jímka možnosti](media/data-flow/sink2.png "jímky 2")

## <a name="output-to-one-file"></a>Výstup do jednoho souboru
Pro Data Lake nebo Azure Storage Blob jímky typy bude výstup Transformovaná data do složky. Spark se vygenerují dělené výstupní soubory data podle schématu dělení se používají v transformace jímky. Kliknutím na kartu "Optimalizace" můžete nastavit schéma rozdělení oddílů. Pokud byste o ni ADF sloučit výstup do jednoho souboru, klikněte na přepínací tlačítko "Jeden oddíl".

![Jímka možnosti](media/data-flow/opt001.png "jímky možnosti")

## <a name="blob-storage-folder"></a>Složka úložiště objektů BLOB
Při zpracování transformaci dat do objektů Blob Store, vyberte objekt blob *složky* jako vaše cesta k cílové složce, nejedná se o soubor. Tok dat ADF vygenerovat výstupní soubory v této složce.

![Cesta ke složce](media/data-flow/folderpath.png "cesta ke složce")

## <a name="optional-azure-sql-data-warehouse-sink"></a>Volitelné Azure SQL Data Warehouse jímky

Vydáváme předčasné beta datové sady jímky rozpoznáno ADW pro datový tok. To vám umožní dostat Transformovaná data přímo do Azure SQL data Warehouse v rámci toku dat bez nutnosti přidávání aktivitu kopírování v kanálu.

Začněte vytvořením datové sadě služby rozpoznáno ADW, stejně jako u jakékoli jiné ADF kanálu s propojenou službu, která obsahuje vaše přihlašovací údaje rozpoznáno ADW a vyberte, kterou chcete připojit k databázi. V názvu tabulky vyberte existující tabulky nebo zadejte název tabulky, který má tok dat automaticky vytvořit za vás z příchozí polí.

![Jímka možnosti](media/data-flow/adw3.png "jímky 3")

Zpět na tranformation jímky (rozpoznáno ADW je momentálně podporovaný jenom jako jímka) budete vybírat datovou sadu rozpoznáno ADW, který jste vytvořili, tak i účet úložiště, které chcete použít pro přípravu dat pro Polybase načíst do rozpoznáno ADW. Pole cesty je ve formátu: "containername/foldername".

![Jímka možnosti](media/data-flow/adw1.png "jímky 4")

### <a name="save-policy"></a>Uložit zásadu

Přepsání vymazat data z tabulky, pokud existuje, znovu ji vytvořte a nahrajte data. Připojte se nové řádky vložit. Pokud tabulky z tabulky název datové sady v cíli rozpoznáno ADW neexistuje vůbec, toku dat zobrazí vytvořit tabulku a načíst data.

Pokud zrušíte zaškrtnutí políčka "Automatické mapy", můžete ho namapovat pole cílové tabulky ručně.

![Jímka rozpoznáno ADW možnosti](media/data-flow/adw2.png "rozpoznáno adw jímky")

### <a name="max-concurrent-connections"></a>Maximální počet souběžných připojení

Při zápisu dat připojení k Azure database, můžete nastavit maximální počet souběžných připojení v transformaci jímky.

![Možnosti připojení](media/data-flow/maxcon.png "připojení")
