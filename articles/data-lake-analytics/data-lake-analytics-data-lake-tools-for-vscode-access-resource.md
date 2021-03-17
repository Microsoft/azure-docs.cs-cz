---
title: Přístup k prostředkům pomocí Data Lakech nástrojů
description: Naučte se používat Azure Data Lake nástroje pro přístup k prostředkům Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754660"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Přístup k prostředkům pomocí Azure Data Lakech nástrojů

Pomocí příkazů nebo akcí Azure Data Tools můžete v VS Code snadno přistupovat k prostředkům Azure Data Lake Analytics.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrace s Azure Data Lake Analytics prostřednictvím příkazu

K vypsání účtů, metadat přístupu a zobrazení analytických úloh můžete přistupovat k Azure Data Lake Analytics prostředkům.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Výpis účtů Azure Data Lake Analytics v rámci předplatného Azure

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: list Accounts**. Účty se zobrazí v podokně **výstup** .

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Přístup k metadatům Azure Data Lake Analytics

1. Vyberte CTRL + SHIFT + P a pak zadejte **ADL: list Tables**.
2. Vyberte jeden z účtů Data Lake Analytics.
3. Vyberte jednu z Data Lake Analytics databází.
4. Vyberte jedno ze schémat. Můžete zobrazit seznam tabulek.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Zobrazení úloh Azure Data Lake Analytics

1. Otevřete paletu příkazů (CTRL + SHIFT + P) a vyberte **ADL: Zobrazit úlohy**.
2. Vyberte Data Lake Analytics nebo místní účet.
3. Počkejte, než se seznam úloh zobrazí pro účet.
4. Vyberte úlohu ze seznamu úloh. Data Lake Tools otevře zobrazení úlohy v pravém podokně a zobrazí některé informace ve výstupu VS Code.

    ![Seznam úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrace s Azure Data Lake Store prostřednictvím příkazu

Příkazy související s Azure Data Lake Store můžete použít k těmto akcím:

- [Procházení prostředků Azure Data Lake Store](#list-the-storage-path)
- [Náhled souboru Azure Data Lake Store](#preview-the-storage-file)
- Nahrajte soubor přímo do Azure Data Lake Store v VS Code
- Stáhněte si soubor přímo z Azure Data Lake Store VS Code

### <a name="list-the-storage-path"></a>Vypíše cestu k úložišti.

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Výpis cesty úložiště pomocí palety příkazů

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: cesta k seznamu**.
2. Zvolte složku v seznamu nebo vyberte možnost **zadat cestu** nebo **Přejít z kořenové cesty**. (Jako příklad používáme **cestu ENTER** .)
3. Vyberte účet Data Lake Analytics.
4. Vyhledejte nebo zadejte cestu ke složce úložiště (například složku/Output/).  

Paleta příkazů obsahuje informace o cestě na základě vašich záznamů.

![Výsledky cesty úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Pohodlnější způsob, jak zobrazit relativní cestu, je prostřednictvím místní nabídky.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Výpis cesty úložiště v místní nabídce

Klikněte pravým tlačítkem na řetězec cesty a vyberte možnost **cesta k seznamu**.

!["Cesta k seznamu" v místní nabídce](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Náhled souboru úložiště

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: náhled souboru**.
2. Vyberte účet Data Lake Analytics.
3. Zadejte cestu k souboru Azure Storage (například složku/Output/SearchLog.txt).

Soubor se otevře v VS Code.

![Postup a výsledek pro náhled souboru úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Dalším způsobem, jak náhled souboru zobrazit, je prostřednictvím místní nabídky na celé cestě souboru nebo v relativní cestě k souboru v editoru skriptu.

### <a name="upload-a-file-or-folder"></a>Nahrání souboru nebo složky

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **nahrát soubor** nebo **nahrát složku**.
2. Vyberte jeden soubor nebo více souborů, pokud jste vybrali možnost **nahrát soubor**, nebo vyberte celou složku, pokud jste vybrali možnost **nahrát složku**. Potom vyberte **Nahrát**.
3. Zvolte složku úložiště v seznamu nebo vyberte možnost **zadat cestu** nebo **Přejít z kořenové cesty**. (Jako příklad používáme **cestu ENTER** .)
4. Vyberte účet Data Lake Analytics.
5. Vyhledejte nebo zadejte cestu ke složce úložiště (například složku/Output/).
6. Vyberte **možnost zvolit aktuální složku** a určete tak cíl nahrávání.

![Postup a výsledek pro nahrání souboru nebo složky](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Další způsob, jak nahrávat soubory do úložiště, je prostřednictvím místní nabídky na celé cestě souboru nebo v relativní cestě k souboru v editoru skriptu.

[Stav nahrávání můžete monitorovat](#check-storage-tasks-status).

### <a name="download-a-file"></a>Stažení souboru

Soubor můžete stáhnout pomocí příkazu **ADL: download file** nebo **ADL: Download File (rozšířené)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Stažení souboru pomocí příkazu ADL: Download File (rozšířené)

1. Klikněte pravým tlačítkem na editor skriptů a pak vyberte **Stáhnout soubor (rozšířené)**.
2. VS Code zobrazí soubor JSON. Můžete zadat cesty k souborům a stáhnout více souborů současně. Pokyny se zobrazí v okně **výstup** . Chcete-li pokračovat ve stahování souboru nebo souborů, uložte (CTRL + S) soubor JSON.

    ![Soubor JSON s cestami pro stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

V okně **výstup** se zobrazí stav stahování souboru.

![Okno výstup se stavem stahování](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

[Stav stahování můžete monitorovat](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Stažení souboru pomocí příkazu ADL: Download File

1. Klikněte pravým tlačítkem na editor skriptů, vyberte **Stáhnout soubor** a pak v dialogovém okně **Vybrat složku** vyberte cílovou složku.

1. Zvolte složku v seznamu nebo vyberte možnost **zadat cestu** nebo **Přejít z kořenové cesty**. (Jako příklad používáme **cestu ENTER** .)

1. Vyberte účet Data Lake Analytics.

1. Vyhledejte nebo zadejte cestu ke složce úložiště (například složku/Output/) a potom vyberte soubor, který chcete stáhnout.

![Postup a výsledek pro stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Další způsob, jak stahovat soubory úložiště, je prostřednictvím místní nabídky na celé cestě souboru nebo v relativní cestě k souboru v editoru skriptu.

[Stav stahování můžete monitorovat](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrolovat stav úloh úložiště

Stav nahrávání a stahování se zobrazí na stavovém řádku. Vyberte stavový řádek a na kartě **výstup** se zobrazí stav.

![Stavový řádek a podrobnosti výstupu](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrace s Azure Data Lake Analyticsem z Průzkumníka

Po přihlášení budou všechna předplatná pro váš účet Azure uvedená v levém podokně v části **Azure datalake**.

![Průzkumník Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics navigace metadat

Rozbalte své předplatné Azure. V uzlu **databáze u-SQL** můžete procházet databázi u-SQL a zobrazovat složky jako **schémata**, **přihlašovací údaje**, **sestavení**, **tabulky** a **index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics Správa entit metadat

Rozbalte **databáze U-SQL**. Můžete vytvořit databázi, schéma, tabulku, typ tabulky, index nebo statistiku kliknutím pravým tlačítkem myši na odpovídající uzel a následným výběrem **skriptu, který chcete vytvořit** v místní nabídce. Na stránce otevřený skript upravte skript podle vašich potřeb. Pak úlohu odešlete tak, že na ni kliknete pravým tlačítkem myši a vyberete **ADL: odeslat úlohu**.

Po dokončení vytváření položky klikněte pravým tlačítkem na uzel a pak vyberte **aktualizovat** , aby se položka zobrazila. Položku můžete také odstranit kliknutím na ni pravým tlačítkem myši a následným výběrem možnosti **Odstranit**.

![Příkaz "skript k vytvoření" v místní nabídce v Průzkumníkovi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Stránka skriptu pro novou položku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrace sestavení Data Lake Analytics

Sestavení můžete zaregistrovat v odpovídající databázi tak, že kliknete pravým tlačítkem myši na uzel **sestavení** a pak vyberete **registrovat sestavení**.

![Příkaz registrovat sestavení v místní nabídce uzlu sestavení](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrace s Azure Data Lake Storeem z Průzkumníka

Přejděte na **Data Lake Store**:

- Můžete kliknout pravým tlačítkem myši na uzel složka a potom v místní nabídce použít příkazy **aktualizovat**, **Odstranit**, **nahrát**, **Odeslat složku**, **Kopírovat relativní cestu** a **Kopírovat úplné cesty** .

   ![Příkazy místní nabídky pro uzel složky v Průzkumníkovi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Můžete kliknout pravým tlačítkem myši na uzel souboru a potom použít **Náhled**, **Stáhnout**, **Odstranit**, **vytvořit extrakci skriptu** (k dispozici pouze pro soubory CSV, TSV a txt), **zkopírovat relativní cestu** a **Kopírovat příkazy úplné cesty** v místní nabídce.

   ![Příkazy místní nabídky pro uzel souboru v Průzkumníkovi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrace s Azure Blob Storage z Průzkumníka

Přejděte do úložiště objektů BLOB:

- Můžete kliknout pravým tlačítkem na uzel kontejneru objektů BLOB a potom použít příkazy **aktualizovat**, **Odstranit kontejner objektů BLOB** a **nahrát objekty blob** v místní nabídce.

   ![Příkazy místní nabídky pro uzel kontejneru objektů BLOB v úložišti objektů BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Můžete kliknout pravým tlačítkem myši na uzel složka a potom použít příkazy **aktualizovat** a **nahrát objekt BLOB** v místní nabídce.

   ![Příkazy místní nabídky pro uzel složky v úložišti objektů BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Můžete kliknout pravým tlačítkem myši na uzel souboru a potom použít příkaz **Zobrazit nebo upravit**, **Stáhnout**, **Odstranit**, **vytvořit extrahování skriptu** (k dispozici pouze pro soubory CSV, TSV a txt), **zkopírovat relativní cestu** a **Kopírovat příkazy úplné cesty** v místní nabídce.

    ![Příkazy místní nabídky pro uzel souboru pod úložištěm objektů BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otevřete Data Lake Exploreru na portálu.

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **Open web Průzkumník služby Azure Storage** nebo klikněte pravým tlačítkem na relativní cestu nebo na úplnou cestu v editoru skriptů a pak vyberte **otevřít webovou Průzkumník služby Azure Storage**.
3. Vyberte účet Data Lake Analytics.

Data Lake nástroje otevře Azure Storage cestu v Azure Portal. Můžete najít cestu a zobrazit náhled souboru z webu.

## <a name="additional-features"></a>Další funkce

Data Lake nástroje pro VS Code podporují následující funkce:

- **Automatické dokončování IntelliSense**: návrhy se zobrazí v automaticky otevíraných oknech kolem položek, jako jsou klíčová slova, metody a proměnné. Různé ikony reprezentují různé typy objektů:

  - Datový typ Scala
  - Komplexní datový typ
  - Integrovaná UDT
  - Kolekce a třídy .NET
  - Výrazy jazyka C#
  - Předdefinované C# UDF, Udo a UDAAGs
  - Funkce U-SQL
  - Funkce oken U-SQL

    ![Typy objektů IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Automatické dokončování v IntelliSense pro data Lake Analytics metadata**: Data Lake nástroje stahují informace o data Lake Analytics metadat místně. Funkce technologie IntelliSense automaticky vyplní objekty z metadat Data Lake Analytics. Mezi tyto objekty patří databáze, schéma, tabulka, zobrazení, funkce vracející tabulku, procedury a sestavení jazyka C#.

  ![Metadata technologie IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Značka chyby technologie IntelliSense**: u-SQL a C# jsou podtržené chyby v úpravách data Lakech nástrojů.
- **Syntaxe – zvýraznění**: Data Lake nástroje používá barvy k odlišení položek, jako jsou proměnné, klíčová slova, datové typy a funkce.

    ![Syntaxe s různými barvami](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Doporučujeme, abyste upgradovali na verzi Nástroje Azure Data Lake pro Visual Studio 2.3.3000.4 nebo novější. Předchozí verze jsou zastaralé a nejsou už k dispozici ke stažení.  

## <a name="next-steps"></a>Další kroky

- [Vývoj U-SQL pomocí Pythonu, R a C ostrého pro Azure Data Lake Analytics v VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Místní spuštění U-SQL a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Kurz: Začínáme s Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Kurz: vývoj skriptů U-SQL pomocí Data Lakech nástrojů pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)