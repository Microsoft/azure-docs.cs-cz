---
title: 'Rychlý Start: hromadné načtení dat pomocí Synapseu Čt'
description: Hromadné načítání dat v synapse SQL pomocí synapse studia
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 4dd0455c73395b3f3b883032d25160f0ea299cea
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090620"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Rychlý Start: hromadné načítání pomocí synapse SQL

Načítání dat nebylo nikdy snazší při použití Průvodce hromadným zatížením v nástroji synapse Studio. Tento průvodce vás provede vytvořením skriptu T-SQL s [příkazem Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) pro hromadné načtení dat. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Vstupní body Průvodce hromadnou zátěží

Teď můžete snadno hromadně načíst data pomocí fondů SQL, a to jednoduše tak, že kliknete pravým tlačítkem na následující oblasti v rámci synapse studia:

- Soubor nebo složka z účtu služby Azure Storage připojeného k vašemu pracovnímu prostoru ![ kliknutím pravým tlačítkem myši na soubor nebo složku z účtu úložiště](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Předpoklady

- Tento průvodce vygeneruje příkaz COPY, který používá předávací službu Azure AD pro ověřování. Váš [uživatel Azure AD musí mít přístup](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples#d-azure-active-directory-authentication-aad) k pracovnímu prostoru, který má aspoň roli Azure data BLOB úložiště pro účet adls Gen2.

- Pokud vytváříte novou tabulku, do které se načítají, musíte mít požadovaná [oprávnění pro použití příkazu Kopírovat](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#permissions) a oprávnění k vytváření tabulek.

- Propojená služba přidružená k účtu adls Gen2 **musí mít přístup ke** / **složce** souborů, která se má načíst. Pokud má například mechanismus ověřování propojených služeb spravovanou identitu, musí mít spravovaná identita v pracovním prostoru aspoň oprávnění čtenář objektů BLOB úložiště v účtu úložiště.

- Pokud je ve vašem pracovním prostoru povolená virtuální síť, ujistěte se, že integrovaný modul runtime přidružený k účtu ADLS Gen2 propojené služby pro zdrojová data a umístění souboru chyb má povolený interaktivní vytváření. Interaktivní vytváření se vyžaduje pro detekci v rámci schématu, zobrazení náhledu obsahu zdrojového souboru a procházení ADLS Gen2 účtů úložiště v průvodci.

### <a name="steps"></a>Postup

1. Z panelu umístění zdrojového úložiště vyberte účet úložiště a soubor nebo složku, které nahráváte: ![ Výběr zdrojového umístění.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Vyberte nastavení formátu souboru včetně účtu úložiště, do kterého chcete zapsat odmítnuté řádky (soubor s chybou). V současné době jsou podporovány pouze soubory CSV a Parquet.

    ![Výběr nastavení formátu souboru](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Můžete vybrat možnost Ukázková data, abyste viděli, jak příkaz COPY analyzuje soubor, abyste mohli nakonfigurovat nastavení formátu souboru. Vyberte možnost Ukázková data pokaždé, když změníte nastavení formátu souboru, abyste viděli, jak příkaz COPY bude analyzovat soubor s aktualizovaným nastavením: ![ Náhled dat](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Vyberte fond SQL, který používáte k načtení, včetně toho, jestli bude zatížení pro existující tabulku nebo novou tabulku: ![ Výběr cílového umístění.](./sql/media/bulk-load/bulk-load-target-location.png)

5. Vyberte konfigurovat mapování sloupce a ujistěte se, že máte vhodné mapování sloupce. Pro nové tabulky je konfigurace mapování sloupce kritická pro aktualizaci datových typů cílového sloupce: ![ Konfigurace mapování sloupce](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Vyberte otevřít skript a skript T-SQL se vygeneruje příkazem COPY, který se má načíst ze služby Data Lake: ![ otevření skriptu SQL.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Další kroky

- Další informace o možnostech kopírování najdete v článku o [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) .
- Podívejte se na článek [Přehled načítání dat](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) .
