---
title: Toky dat tahání v Azure Data Factory
description: Přehled toků dat tahání v Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5d0393ab5f2582efa2a4b727543bc799cdb1143e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082847"
---
# <a name="what-are-wrangling-data-flows"></a>Co jsou toky dat tahání?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Organizace musí provést přípravu a tahání dat, aby bylo možné přesně analyzovat složitá data, která se budou dál rozšiřovat každý den. Příprava dat je povinná, aby organizace mohli používat data v různých obchodních procesech a zkrátit čas na hodnotu.

Tahání datové toky v Azure Data Factory umožňují iterativní přípravu dat bez kódu na úrovni cloudu. Tahání datové toky se integrují s [Power Query online](https://docs.microsoft.com/power-query/) a zpřístupňují Power Query M funkcí pro uživatele Data Factory.

Tok dat tahání transformuje M vygenerované online editorem hybridních webových Power Query aplikací do kódu Spark pro provádění škálování cloudu.

Toky dat tahání jsou zvláště užitečné pro inženýry dat nebo "občany pro datové integrátory".

> [!NOTE]
> Tok dat tahání je aktuálně k dispozici ve verzi Public Preview.

## <a name="use-cases"></a>Případy použití

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rychlé interaktivní zkoumání a Příprava dat

Více datových techniků a integrátorů dat občana může interaktivně prozkoumat a připravit datové sady v cloudovém měřítku. Díky nárůstu objemu, rozmanitosti a rychlosti dat v datových lakůch potřebují uživatelé účinný způsob, jak prozkoumat a připravit datové sady. Například může být nutné vytvořit datovou sadu, která obsahuje demografické údaje o zákaznících pro nové zákazníky od 2017. Nejste mapováni na známý cíl. Zkoumáte, tahání a připravujete datové sady, abyste splnili požadavky před jejich publikováním v Lake. Toky dat tahání se často používají pro méně formální analytické scénáře. Datové sady připravenou se dají použít k provádění transformací a operací strojového učení.

### <a name="code-free-agile-data-preparation"></a>Agilní Příprava dat bez kódu

Integrátory dat občana stráví více než 60% času hledáním a přípravou dat. Hledají si ho jako bezplatný kód pro zlepšení provozní produktivity. Umožnění rozšíření, tvaru a publikování dat občanům, kteří mají k disPower Query v rámci škálovatelné nástroje, jako je online, výrazně zlepšuje produktivitu. Tahání data flow v Azure Data Factory umožňuje známé Power Query online editoru hybridních webových aplikací, které umožňují integrátorům dat občanů rychle opravovat chyby, standardizovat data a vytvářet vysoce kvalitní data pro podporu obchodních rozhodnutí.

### <a name="data-validation"></a>Ověřování dat

Vizuálně naskenujte vaše data způsobem bez kódu, abyste mohli odebrat jakékoli odlehlé hodnoty, anomálie a odpovídat na obrazec pro rychlou analýzu.

## <a name="supported-sources"></a>Podporované zdroje

| Konektor | Formát dat | Typ ověřování |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Klíč účtu |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Instanční objekt |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Klíč účtu, instanční objekt |
| [Azure SQL Database](connector-azure-sql-database.md) | - | Ověřování SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Ověřování SQL |

## <a name="the-mashup-editor"></a>Editor hybridní webové aplikace

Při vytváření toku dat tahání se všechny zdrojové datové sady stanou dotazy datových sad a jsou umístěné ve složce **ADFResource** . Ve výchozím nastavení bude UserQuery ukazovat na první dotaz DataSet. Všechny transformace by se měly provádět na UserQuery, protože změny dotazů na datovou sadu se nepodporují, ani nebudou trvalé. Přejmenování, přidání a odstranění dotazů není aktuálně podporováno.

![Tahání](media/wrangling-data-flow/editor.png)

V současné době nejsou všechny funkce Power Query M podporovány pro tahání dat, přestože jsou k dispozici při vytváření obsahu. Při sestavování datových toků tahání se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Další informace o podporovaných transformacích najdete v tématu [tahání data Flow Functions](wrangling-data-flow-functions.md).

Datový tok aktuálně tahání podporuje zápis pouze do jedné jímky.

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet tahání tok dat](wrangling-data-flow-tutorial.md).
