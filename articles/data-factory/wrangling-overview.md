---
title: Tahání dat v Azure Data Factory
description: Přehled tahání dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: aa829f8f3de145cf831719a39cfb66163d9cc4f9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633923"
---
# <a name="what-is-data-wrangling"></a>Co je data tahání?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Organizace potřebují možnost prozkoumat jejich důležitá obchodní data pro přípravu a tahání dat, aby poskytovala přesnou analýzu složitých dat, která se v každém dni stále rozrůstá. Příprava dat je povinná, aby organizace mohli používat data v různých obchodních procesech a zkrátit čas na hodnotu.

Data Factory vám umožní dodávat data bez kódu na úrovni cloudu iterativním využitím Power Query. Data Factory se integruje s [Power Query online](/power-query/) a zpřístupňuje Power Query M funkce jako aktivitu kanálu.

Data Factory překládá M vygenerovaných Power Query online editoru hybridních webových aplikací do kódu Spark pro provádění škálování v cloudu díky překladu M na Azure Data Factory toků dat. Tahání data pomocí Power Query a toků dat jsou obzvláště užitečná pro inženýry dat nebo "občanská data".

> [!NOTE]
> Aktivita Power Query v Azure Data Factory je aktuálně dostupná ve verzi Public Preview.

## <a name="use-cases"></a>Případy použití

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rychlé interaktivní zkoumání a Příprava dat

Více datových techniků a integrátorů dat občana může interaktivně prozkoumat a připravit datové sady v cloudovém měřítku. Díky nárůstu objemu, rozmanitosti a rychlosti dat v datových lakůch potřebují uživatelé účinný způsob, jak prozkoumat a připravit datové sady. Například může být nutné vytvořit datovou sadu, která obsahuje demografické údaje o zákaznících pro nové zákazníky od 2017. Nejste mapováni na známý cíl. Zkoumáte, tahání a připravujete datové sady, abyste splnili požadavky před jejich publikováním v Lake. Tahání se často používá pro méně formální analytické scénáře. Datové sady připravenou se dají použít k provádění transformací a operací strojového učení.

### <a name="code-free-agile-data-preparation"></a>Agilní Příprava dat bez kódu

Integrátory dat občana stráví více než 60% času hledáním a přípravou dat. Hledají si ho jako bezplatný kód pro zlepšení provozní produktivity. Umožnění rozšíření, tvaru a publikování dat občanům, kteří mají k disPower Query v rámci škálovatelné nástroje, jako je online, výrazně zlepšuje produktivitu. Tahání v Azure Data Factory umožňuje známé Power Query online editoru hybridních webových aplikací, které umožní integrátorům dat občanů rychle opravovat chyby, standardizovat data a vytvářet vysoce kvalitní data pro podporu obchodních rozhodnutí.

### <a name="data-validation-and-exploration"></a>Ověřování a průzkum dat

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

Při vytváření aktivity Power Query se všechny zdrojové datové sady stanou dotazy datových sad a ukládají se do složky **ADFResource** . Ve výchozím nastavení bude UserQuery ukazovat na první dotaz DataSet. Všechny transformace by se měly provádět na UserQuery, protože změny dotazů na datovou sadu se nepodporují, ani nebudou trvalé. Přejmenování, přidání a odstranění dotazů není aktuálně podporováno.

![Tahání](media/wrangling-data-flow/editor.png)

V současné době nejsou všechny funkce Power Query M podporovány pro tahání dat, přestože jsou k dispozici při vytváření obsahu. Při sestavování Power Querych aktivit se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Další informace o podporovaných transformacích naleznete v tématu [data tahání Functions](wrangling-functions.md).

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet data tahání Power Query zpracování](wrangling-tutorial.md).
