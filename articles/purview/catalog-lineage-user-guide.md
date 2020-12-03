---
title: Uživatelská příručka pro Data Catalog řádek (Preview)
description: Tento článek poskytuje přehled funkce katalogu katalogu pro Azure dosah.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: a319dbce2502f35272cf9b70da2022f581d64275
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553090"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Uživatelská příručka ke službě Azure dosah Data Catalog line

Tento článek obsahuje přehled funkcí datových linií ve službě Azure dosah Data Catalog.

## <a name="background"></a>Pozadí

Jednou z funkcí platformy Azure dosah je schopnost zobrazit mezi datovými sadami vytvořenými datovými procesy řadu. Systémy, jako je Data Factory, sdílení dat a Power BI zachytí při přesunu data. Vlastní vytváření sestav na základě čárového přenosu je podporované taky pomocí háčků a REST API.

## <a name="lineage-collection"></a>Kolekce řádků 
 Metadata shromážděná ve službě Azure dosah ze systémů podnikových dat se procházejí napříč, aby se zobrazila koncová koncová osa dat. Datové systémy, které shromažďují označení do dosah, jsou široce rozdělené do následujících tří typů.

### <a name="data-processing-system"></a>Systém zpracování dat
Nástroje pro integraci dat a ETL můžou do služby Azure dosah v době spuštění nabízet. Nástroje, jako Data Factory, sdílení dat, synapse, Azure Databricks atd., patří do této kategorie systémů dat. Systémy zpracování dat odkazují na datové sady jako na zdroj z různých databází a řešení úložiště, aby se vytvořily cílové datové sady. Seznam systémů zpracování dat, které jsou aktuálně integrovány s dosah, jsou uvedeny v následující tabulce.


| Systém zpracování dat | Podporovaný obor |
| ---------------------- | ------------|
| Azure Data Factory | [Aktivita kopírování](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [Aktivita toku dat](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Aktivita provádění balíčku SSIS](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Sdílet snímek](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Systémy úložiště dat
Databáze & řešení úložiště, jako jsou SQL Server, Teradata a SAP, obsahují dotazovací moduly pro transformaci dat pomocí skriptovacího jazyka. Datové řádky z uložených procedur se shromažďují v dosah a společně s nimi od jiných systémů.

| Systém úložiště dat | Podporovaný obor |
| ---------------------- | ------------|
| Teradata | Uložené procedury

### <a name="data-analytics--reporting-systems"></a>Data Analytics & systémy vytváření sestav
Datové systémy, jako je Azure ML a Power BI ve službě Azure dosah. Tyto systémy budou používat datové sady ze systémů úložišť a pomocí jejich meta modelu vytvořit řídicí panel BI, experimenty ML a tak dále.

| Systém generování sestav analýzy dat & | Podporovaný obor |
| ---------------------- | ------------|
| Power BI | [Datové sady, datové toky, sestavy & řídicí panely](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Začínáme s vydanými řádky

V dosah se zahrnují datové sady a procesy. Datové sady jsou také označovány jako uzly, zatímco procesy mohou být označovány také jako okraje:

* **Datová sada (uzel)**: datová sada (strukturovaná nebo nestrukturovaná) poskytnutá jako vstup pro proces. Například tabulka SQL, Azure Blob a soubory (například. csv a. XML) jsou všechny považované za datové sady. V části dosahy se v datových sadách zobrazují obdélníkové rámečky.

* **Process (Edge)**: aktivita nebo transformace prováděná v datové sadě se nazývá proces. Například aktivita kopírování ADF, snímek sdílených dat a tak dále. V části dosahy se procesy reprezentují pole s kulatými úsečkami.

Pokud chcete získat přístup k informacím o prostředcích v dosah, postupujte takto:

1. V Azure Portal přejdete na [stránku účty Azure dosah](https://aka.ms/purviewportal).

1. V seznamu vyberte svůj účet Azure dosah a pak na stránce **Přehled** vyberte **spustit účet dosah** .

1. Na **domovské** stránce Azure dosah vyhledejte název datové sady nebo název procesu, jako je například kopírování ADF nebo aktivita toku dat. A potom stiskněte klávesu ENTER.

1. Ve výsledcích hledání vyberte Asset a **vyberte jeho kartu** .

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Snímek obrazovky znázorňující, jakým způsobem se má vybrat karta pro čáru" border="true":::

## <a name="asset-level-lineage"></a>Asset – na úrovni majetku

Azure dosah podporuje pro datové sady a procesy řádkovou vyrovnávání úrovně prostředků. Pokud chcete vidět, že se má vyřadit z úrovně assetu **, přejděte na kartu pro** aktuální Asset v katalogu. Vyberte aktuální uzel Asset DataSet. Ve výchozím nastavení se v levém podokně zobrazí seznam sloupců, které patří k datům.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Snímek obrazovky ukazující, jak vybrat sloupce zobrazení na stránce s řádky" border="true":::

## <a name="column-level-lineage"></a>Čára na úrovni sloupce

Azure dosah pro datové sady podporuje čáry na úrovni sloupců. Chcete-li zobrazit čáru na úrovni sloupce, přejděte na **kartu pro** aktuální Asset v katalogu a postupujte podle následujících kroků:

1. Až budete na kartě zarážka, zaškrtněte v levém podokně políčko vedle každého sloupce, který chcete zobrazit v datovém řádku.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Snímek obrazovky ukazující, jak vybrat sloupce, které se mají zobrazit na stránce pro vybírání řádků" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

1. Najeďte myší na vybraný sloupec v levém podokně nebo v datové sadě plátna čáry, abyste viděli mapování sloupce. Všechny instance sloupců jsou zvýrazněné.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Snímek obrazovky ukazující, jak umístit ukazatel myši na název sloupce a zvýraznit tok sloupce v cestě k datové čáře" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

1. Pokud je počet sloupců větší, než se dá zobrazit v levém podokně, použijte možnost Filtr a vyberte konkrétní sloupec podle názvu. Alternativně můžete pomocí myši procházet seznam.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Snímek obrazovky znázorňující, jak filtrovat sloupce podle názvu sloupce na stránce s řádky" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

1. Pokud plátno na čáře obsahuje více uzlů a hran, použijte filtr k výběru datové assety nebo procesu procesů podle názvu. Alternativně můžete použít myš k posouvání kolem okna se zářádkem.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Snímek obrazovky znázorňující uzly datových assetů podle názvu na stránce s řádky" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

1. Pomocí přepínače v levém podokně zvýrazněte seznam datových sad na plátně pro čárové účely. Pokud přepínač vypnete, zobrazí se všechny materiály, které obsahují alespoň jeden z vybraných sloupců. Pokud přepínač zapnete, zobrazí se pouze datové sady, které obsahují všechny sloupce.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Snímek obrazovky, který ukazuje, jak pomocí přepínače filtrovat seznam uzlů na stránce o počtu řádků." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

1. Vyberte možnost **Přepnout na prostředek** u libovolného assetu a zobrazte odpovídající metadata ze zobrazení řádků. V tomto případě je to účinný způsob, jak procházet jiný Asset v katalogu ze zobrazení na základě řádků.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Snímek obrazovky s postupem, jak vybrat možnost přepnout na prostředek v datovém assetu na řádku" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

1. Plátno pro řadu by mohlo být složité pro oblíbené datové sady. Aby nedocházelo k zbytečnému využívání, výchozí zobrazení zobrazí jenom pět úrovní počtu řádků pro daný prostředek. Zbytek vydaných řádků lze rozšířit kliknutím na bubliny na plátně čáry. Příjemci dat také mohou skrýt prostředky na plátně, které neodpovídají žádnému zájmu. Chcete-li dál snížit přehlednost, vypněte na horním okraji plátna **více řádků** . Tato akce skryje všechny bubliny na plátně pro čárové aplikace.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Snímek obrazovky ukazující, jak přepnout více řádků" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

1. Pomocí inteligentních tlačítek na plátně čáry získáte optimální pohled na daný řádek. Automatické rozložení, přiblížení, přiblížení a přiblížení, celá obrazovka a navigační mapa jsou k dispozici pro moderní prostředí v katalogu.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Snímek obrazovky znázorňující, jak vybrat inteligentní tlačítka pro čáru" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Další kroky

* [Odkaz na Azure Data Factory pro řádek](how-to-link-azure-data-factory.md)
* [Odkaz na službu Azure Data Share za účelem vyvedení](how-to-link-azure-data-share.md)