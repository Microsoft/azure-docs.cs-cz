---
title: 'Rychlý Start: hromadné načtení dat pomocí vyhrazeného fondu SQL'
description: Pomocí synapse studia můžete hromadně načítat data do vyhrazeného fondu SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: be15a37a9a2965da36f7e8f884a0a3112106b9ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586722"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Rychlý Start: hromadné načítání pomocí synapse studia

Načítání dat je snadné pomocí Průvodce hromadným zatížením v synapse studiu. Synapse Studio je funkce služby Azure synapse Analytics. Průvodce hromadným zatížením vás provede vytvořením skriptu T-SQL s [příkazem Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) pro hromadné načtení dat do vyhrazeného fondu SQL. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Vstupní body Průvodce hromadnou zátěží

Data můžete hromadně načíst kliknutím pravým tlačítkem myši na následující oblast v synapse studiu: soubor nebo složka z účtu služby Azure Storage, který je připojený k vašemu pracovnímu prostoru.

![Snímek obrazovky, který ukazuje kliknutí pravým tlačítkem myši na soubor nebo složku z účtu úložiště.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Předpoklady

- Průvodce vygeneruje příkaz COPY, který používá předávací předávání Azure Active Directory (Azure AD) pro ověřování. Váš [uživatel Azure AD musí mít přístup](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) k pracovnímu prostoru, který má aspoň roli Azure data BLOB úložiště pro účet Azure Data Lake Storage Gen2. 

- Pokud vytváříte novou tabulku, do které se načítají, musíte mít požadovaná [oprávnění pro použití příkazu Kopírovat](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) a oprávnění k vytváření tabulek.

- Propojená služba přidružená k účtu Data Lake Storage Gen2 *musí mít přístup k souboru nebo složce* , které se mají načíst. Pokud je například mechanismus ověřování pro propojenou službu spravovaná identita, musí mít spravovaná identita v pracovním prostoru aspoň oprávnění ke čtečce dat objektů BLOB úložiště v účtu úložiště.

- Pokud je v pracovním prostoru povolená virtuální síť, ujistěte se, že integrovaný modul runtime přidružený k propojeným službám účtu Data Lake Storage Gen2 pro zdrojová data a umístění souboru chyb má povolený interaktivní vytváření. Interaktivní vytváření se vyžaduje pro detekci v rámci schématu, zobrazení náhledu obsahu zdrojového souboru a procházení Data Lake Storage Gen2 účtů úložiště v průvodci.

## <a name="steps"></a>Postup

1. Na panelu **umístění zdrojového úložiště** vyberte účet úložiště a soubor nebo složku, ze které nahráváte. Průvodce se automaticky pokusí rozpoznat soubory Parquet a soubory s oddělovači (CSV), včetně mapování zdrojových polí ze souboru na příslušné cílové datové typy SQL. 

   ![Snímek obrazovky, který zobrazuje výběr zdrojového umístění.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Vyberte nastavení formátu souboru, včetně nastavení chyby pro to, kdy během procesu hromadného načtení existují odmítnuté řádky. Můžete také vybrat možnost **Zobrazit data** a zjistit, jak bude příkaz Kopírovat analyzovat soubor, který vám pomůže nakonfigurovat nastavení formátu souboru. Při každé změně nastavení formátu souboru vyberte **Náhled dat** , abyste viděli, jak příkaz Copy bude analyzovat soubor s aktualizovaným nastavením.

   ![Snímek obrazovky, který zobrazuje náhled dat.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - Průvodce hromadným zatížením nepodporuje náhled dat pomocí ukončovacích polí s více znaky. Když zadáte ukončovací znak pole s více znaky, Průvodce zobrazí náhled dat v jednom sloupci. 
   > - Když vyberete možnost **odvodit názvy sloupců**, Průvodce hromadným zatížením analyzuje názvy sloupců z prvního řádku zadaného polem **první řádek** . Průvodce hromadnou zátěží automaticky zvýší `FIRSTROW` hodnotu v příkazu copy hodnotou 1 pro ignorování tohoto řádku záhlaví. 
   > - V příkazu COPY se podporuje zadání zakončení řádků s více znaky. Průvodce hromadným zatížením ho ale nepodporuje a vyvolá chybu.

3. Vyberte vyhrazený fond SQL, který používáte k načtení, včetně toho, zda bude zatížení pro existující tabulku nebo novou tabulku.
   ![Snímek obrazovky, který zobrazuje výběr cílového umístění.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Vyberte **Konfigurovat mapování sloupce** a ujistěte se, že máte vhodné mapování sloupce. Názvy sloupců poznámek se budou automaticky detekovat, pokud jste povolili možnost **odvodit názvy sloupců**. Pro nové tabulky je konfigurace mapování sloupce důležitá pro aktualizaci datových typů cílového sloupce.

   ![Snímek obrazovky zobrazující konfiguraci mapování sloupce](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Vyberte **otevřít skript**. Skript T-SQL se vygeneruje příkazem COPY, který se má načíst ze služby Data Lake.
   ![Snímek obrazovky, který ukazuje otevření skriptu SQL.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Další kroky

- Další informace o možnostech kopírování najdete v článku o [příkazu copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) .
- Informace o použití procesu extrakce, transformace a načítání (ETL) najdete v článku [Přehled načítání dat](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) .
