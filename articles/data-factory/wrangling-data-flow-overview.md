---
title: Hašteření datových toků v Azure Data Factory
description: Přehled hašteření datových toků v Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408985"
---
# <a name="what-are-wrangling-data-flows"></a>Co jsou toky dat?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Organizace musí provádět přípravu dat a hašteření pro přesnou analýzu komplexních dat, která každým dnem rostou. Příprava dat je vyžadována, aby organizace mohly data používat v různých obchodních procesech a zkrátit dobu potřebnou k dosažení hodnoty.

Hašteření datových toků v Azure Data Factory vám umožní provést přípravu dat bez kódu v cloudovém měřítku iterativně. Hašteření datových toků se integruje s [Power Query Online](https://docs.microsoft.com/power-query/) a zpřístupňuje funkce Power Query M uživatelům datové továrny.

Hádání tok dat převádí M generované Power Query Online Mashup Editor do jiskry kód pro cloud škálování spuštění.

Hašteření datových toků je užitečné zejména pro datové inženýry nebo "integrátory údajů občanů".

## <a name="use-cases"></a>Případy použití

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rychlý interaktivní průzkum a příprava dat

Více datových inženýrů a integrátorů dat občanů může interaktivně zkoumat a připravovat datové sady v cloudovém měřítku. S nárůstem objemu, rozmanitosti a rychlosti dat v datových jezerech potřebují uživatelé efektivní způsob, jak prozkoumat a připravit datové sady. Například možná budete muset vytvořit datovou sadu, která "má všechny demografické informace o zákaznících pro nové zákazníky od roku 2017". Nemapujete na známý cíl. Zkoumáte, hádáte a připravujete datové sady, abyste splnili požadavek před publikováním v jezeře. Hašteření datových toků se často používá pro scénáře méně formální analýzy. Přípravné datové sady lze použít pro provedení transformace a operace strojového učení po proudu.

### <a name="code-free-agile-data-preparation"></a>Agilní příprava dat bez kódu

Integrátoři údajů o občanech tráví více než 60 % svého času hledáním a přípravou dat. Chtějí to udělat bez kódu, aby zlepšili provozní produktivitu. Povolení integrátorům dat občanů obohacovat, tvarovat a publikovat data pomocí známých nástrojů, jako je Power Query Online, škálovatelným způsobem, výrazně zvyšuje jejich produktivitu. Tok dat v Azure Data Factory umožňuje známému editoru mashup Power Query Online, který umožňuje integrátorům dat občanů rychle opravovat chyby, standardizovat data a vytvářet vysoce kvalitní data pro podporu obchodních rozhodnutí.

### <a name="data-validation"></a>Ověřování dat

Vizuálně naskenujte data způsobem bez kódu, abyste odstranili všechny odlehlé hodnoty, anomálie a přizpůsobili je obrazci pro rychlou analýzu.

## <a name="supported-sources"></a>Podporované zdroje

| Konektor | Formát dat | Typ ověřování |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parkety | Klíč účtu |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Instanční objekt |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parkety | Klíč účtu, instanční objekt |
| [Azure SQL Database](connector-azure-sql-database.md) | - | Ověřování pomocí SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Ověřování pomocí SQL |

## <a name="the-mashup-editor"></a>Editor mashup

Když vytvoříte tok dat hádání, všechny zdrojové datové sady se stanou dotazy datové sady a jsou umístěny ve složce **ADFResource.** Ve výchozím nastavení bude UserQuery ukazovat na první dotaz na datovou sadu. Všechny transformace by měly být provedeny na UserQuery jako změny dotazů datové sady nejsou podporovány ani budou trvalé. Přejmenování, přidání a odstranění dotazů není aktuálně podporováno.

![Hádky](media/wrangling-data-flow/editor.png)

V současné době nejsou podporovány všechny funkce Power Query M pro hádání dat, přestože jsou k dispozici během vytváření. Při vytváření toků dat hádání se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Další informace o podporovaných transformacích naleznete [v tématu hádání funkce toku dat](wrangling-data-flow-functions.md).

V současné době hádání tok dat podporuje pouze zápis do jednoho jímky.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit tok dat v hašteření](wrangling-data-flow-tutorial.md).