---
title: Umožňuje importovat data z různých zdrojů dat
titleSuffix: Azure Machine Learning Studio
description: Jak importovat data do Azure Machine Learning Studio z různých zdrojů dat. Zjistěte, jaké datové typy a formáty dat jsou podporovány.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 2f401290a4a9150d27685c06c2d4cd9dc2f06f0d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730284"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Import cvičných dat do nástroje Azure Machine Learning Studio z různých zdrojů dat

Chcete-li použít vlastní data ve službě Machine Learning Studio k vývoji a trénování řešení prediktivní analýzy, můžete použít data z: 

* **Místní soubor** – místní data předem načíst z vašeho pevného disku, jak vytvořit modul datovou sadu v pracovním prostoru
* **Online zdroje dat** – použití [Import dat] [ import-data] modulu pro přístup k datům z jednoho z několika zdrojů online při spuštění experimentu
* **Machine Learning Studio experiment** – používají data, která byla uložena jako datové sady v nástroji Machine Learning Studio
* [**Databáze systému SQL Server v místním** ](use-data-from-an-on-premises-sql-server.md) – aniž byste museli ručně zkopírujte data použít data z místní databáze systému SQL Server

> [!NOTE]
> Existuje několik ukázkových datových sad v nástroji Machine Learning Studio, můžete použít pro trénovací data k dispozici. Informace naleznete v tématu [použití ukázkových datových sad v nástroji Azure Machine Learning Studio](use-sample-datasets.md).

## <a name="prepare-data"></a>Příprava dat

Machine Learning Studio je navržena pro práci s obdélníkové nebo tabulkových dat, jako jsou textová data, která má s oddělovači nebo strukturovaná data z databáze, i když se v některých případech může použít neobdélníkových data.

Je vhodné, pokud vaše data jsou relativně vyčištění před importem do sady Studio. Například budete chtít postará o problémy, jako jsou třeba řetězce bez uvozovek.

Však nejsou k dispozici v sadě Studio a po import dat povolit některé manipulaci s daty v rámci experimentu moduly. V závislosti na algoritmech strojového učení, které budete používat, musíte se rozhodnout, jak budete zpracovávat data strukturální problémy, jako je například chybějící hodnoty a řídkých datových a moduly, které vám ho mohl pomoct, které jsou. Podívejte se **transformace dat** části palety modulů pro moduly, které provádějí tyto funkce.

Kdykoli v experimentu můžete zobrazit nebo stáhnout data, který je vytvořen po kliknutí na výstupní port modulu. V závislosti na modulu můžou existovat jiné stahování možnosti k dispozici nebo je možné vizualizovat data ve vašem prohlížeči ve studiu.

## <a name="supported-data-formats-and-data-types"></a>Datové typy a formáty dat podporovaných

Počet typů dat můžete importovat do experimentu, v závislosti na tom, jaký mechanismus použijete k importu dat a odkud pocházejí od:

* Prostý text (TXT)
* Hodnot oddělených čárkami (CSV) s hlavičkou (CSV) nebo bez něj (. nh.csv)
* Hodnoty oddělené tabulátorem (TSV) s hlavičkou (TSV) nebo bez něj (. nh.tsv)
* Excelový soubor
* Tabulka Azure
* Tabulka Hive
* Tabulka databází SQL
* Hodnoty OData
* SVMLight data (.svmlight) (viz [SVMLight definice](http://svmlight.joachims.org/) pro informace o formátu)
* Atribut formátu arff (Relation File Format) data (.arff) (viz [ARFF definice](http://weka.wikispaces.com/ARFF) pro informace o formátu)
* Soubor ZIP (.zip)
* Soubor objektu nebo pracovního prostoru R (. RData)

Pokud importujete data ve formátu například ARFF, který zahrnuje jejich metadata, používá Studio tato metadata k definování záhlaví a datový typ jednotlivých sloupců.

Pokud importujete data, jako je formát TSV nebo sdíleného svazku clusteru, který nezahrnuje tato metadata, Studio odvodí typ dat pro každý sloupec vzorkováním data. Pokud data také nemá záhlaví sloupců, Studio poskytuje výchozí názvy.

Můžete explicitně zadat nebo změnit hlavičky a datové typy sloupců pomocí [upravit Metadata] [ edit-metadata] modulu.

Následující datové typy jsou rozpoznávány Studio:

* String
* Integer
* Double
* Logická hodnota
* DateTime
* TimeSpan

Studio používá interní data typ, který volá ***tabulka dat*** k předávání dat mezi moduly. Data můžete explicitně převést do formátu tabulky data s využitím [převod na datové sadě] [ convert-to-dataset] modulu.

Libovolného modulu, který přijímají se formáty než tabulka dat bez upozornění převede dat do tabulky před předáním k dalšímu modulu.

V případě potřeby můžete převést formát tabulky dat zpět do CSV, TSV, ARFF nebo SVMLight formátu a používat ostatní moduly převodu.
Podívejte se **převody formátů dat** části palety modulů pro moduly, které provádějí tyto funkce.

## <a name="import-from-a-local-file"></a>Import z místního souboru

Můžete nahrát soubor dat z vašeho pevného disku, který se použije jako trénovací data v sadě Studio. Pokud importujete soubor sady dat, vytvoříte datovou sadu modul připravené k použití v experimentech ve vašem pracovním prostoru.

Import dat z místního pevného disku, postupujte takto:

1. Klikněte na tlačítko **+ nová** v dolní části okna nástroje Studio.
2. Vyberte **datovou sadu** a **z místního souboru**.
3. V **nahrát novou datovou sadu** dialogové okno, přejděte na soubor, který chcete nahrát.
4. Zadejte název, identifikace typu dat a volitelně zadejte popis. Popis se doporučuje – umožňuje zaznamenat všechny charakteristiky data, která chcete mějte na paměti při používání dat v budoucnu.
5. Zaškrtávací políčko **jde o novou verzi existující datovou sadu** umožňuje aktualizovat existující datovou sadu s novými daty. Uděláte to tak, klikněte na toto zaškrtávací políčko a potom zadejte název existující datovou sadu.

![Nahrát novou datovou sadu](./media/import-data/upload-dataset-from-local-file.png)

Nahrát doba závisí na velikosti dat a rychlosti vašeho připojení ke službě. Pokud víte, že soubor bude trvat dlouhou dobu, vám pomůžou jiné práce se sadou Studio, zatímco čekáte. Ale zavření prohlížeče před dokončením odesílání dat způsobí, že se nezdaří.

Po nahrání dat je uložený v modulu datová sada a je k dispozici žádné experimentu do pracovního prostoru.

Při úpravách experimentu si můžete datové sady, když jste odeslali v **Moje datové sady** seznamu v části **uložení datové sady** seznamu paletě modulů. Můžete přetáhnout a vyřadit datovou sadu na plátno experimentu. Pokud chcete používat datovou sadu pro další analýzy s využitím a strojové učení.

## <a name="import-from-online-data-sources"></a>Importovat z online zdrojů dat

Použití [importovat Data] [ import-data] modulu, experimentu můžete importovat data z různých online zdrojů dat při spuštění testu.

> [!NOTE]
> Tento článek obsahuje obecné informace o [Import dat] [ import-data] modulu. Podrobné informace o typech dat můžete přistupovat, formátů, parametry a odpovědi na běžné dotazy, naleznete v tématu referenční modulu pro [Import dat] [ import-data] modulu.

S použitím [Import dat] [ import-data] modul, můžete přístup k datům z jednoho z několika zdrojů dat online při spuštění experimentu:

* Adresu URL webu pomocí protokolu HTTP
* Hadoop pomocí HiveQL
* Úložiště blobů v Azure
* Tabulka Azure
* Azure SQL database nebo SQL Server na virtuálním počítači Azure
* Místní databáze SQL serveru
* Datový kanál zprostředkovatele, aktuálně OData
* Azure Cosmos DB

Protože tato data školení je přístup při spuštění experimentu, je dostupná jenom v testu. Naproti tomu data, která byla uložena v datové sadě modulu je k dispozici žádné experimentu do pracovního prostoru.

Chcete-li získat přístup k online zdroje dat v experimentu Studio, přidejte [Import dat] [ import-data] modulu do experimentu. Potom vyberte **spusťte Průvodce importem dat** pod **vlastnosti** podrobné pokyny s průvodcem konfigurace zdroje dat a vyberte. Alternativně můžete ručně vybrat **zdroj dat** pod **vlastnosti** a zadejte parametry potřebné pro přístup k datům.

V následující tabulce je uvedeno online zdroje dat, které jsou podporovány. Tato tabulka shrnuje také formátů souborů, které jsou podporovány a parametry, které se používají pro přístup k datům.

> [!IMPORTANT]
> V současné době [Import dat] [ import-data] a [exportovat Data] [ export-data] může číst a zapisovat data pouze z úložiště Azure, které jsou vytvořeny pomocí klasického moduly model nasazení. Jinými slovy nový typ účtu úložiště objektů Blob v Azure, která nabízí úroveň přístupu horkého úložiště nebo studené úrovni přístupu úložiště zatím nepodporuje.
>
> Obecně platí, žádné účty úložiště Azure může mít vytvořené předtím, než tuto možnost služby jsou dostupné by neměly být ovlivněny.
> Pokud je potřeba vytvořit nový účet, vyberte **Classic** pro nasazení modelu, nebo použijte Resource manager a vyberte **Obecné** spíše než **úložiště objektů Blob** pro  **Druh účtu**.
>
> Další informace najdete v tématu [Azure Blob Storage: Horká a studená úroveň úložiště](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Podporované zdroje dat online
Azure Machine Learning **Import dat** modul podporuje následující zdroje dat:

| Zdroj dat | Popis | Parametry |
| --- | --- | --- |
| Adresa URL webu přes protokol HTTP |Čte data v hodnot oddělených čárkami (CSV), hodnoty oddělené tabulátorem (TSV), atribut vztah formátu arff (Format) a formáty Support Vector počítače (SVM light) z jakékoli webové adresy URL, která používá protokol HTTP |<b>ADRESA URL</b>: Určuje úplný název souboru, včetně adresy URL webu a název souboru s jakoukoli příponou. <br/><br/><b>Formát dat</b>: Určuje jeden z podporovaných datových formátů: CSV, TSV, ARFF nebo SVM-light. Pokud data obsahují řádek záhlaví, používá se k přiřazení názvy sloupců. |
| Hadoop/HDFS |Čte data z distribuovaného úložiště v systému Hadoop. Určíte data, která chcete, aby pomocí HiveQL SQL jako dotazovací jazyk. HiveQL slouží také k agregovat data a provádět filtrování předtím, než přidáte data k sadě Studio dat. |<b>Databázový dotaz Hive</b>: Určuje dotaz Hive sloužící ke generování data.<br/><br/><b>Identifikátor URI serveru HCatalog </b> : Zadaný název vašeho clusteru ve formátu  *&lt;název clusteru&gt;. azurehdinsight.net.*<br/><br/><b>Název uživatelského účtu systému Hadoop</b>: Určuje název uživatelského účtu systému Hadoop, slouží ke zřizování clusteru.<br/><br/><b>Heslo uživatelského účtu systému Hadoop</b> : Určuje přihlašovací údaje použité při zřizování clusteru. Další informace najdete v tématu [vytváření clusterů Hadoop v HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Umístění výstupních dat</b>: Určuje, zda jsou data uložená v Hadoop distributed file system (HDFS) nebo v Azure. <br/><ul>Pokud uchováváte výstupní data v HDFS, zadejte identifikátor URI serveru HDFS. (Nezapomeňte použít název clusteru HDInsight bez předpony HTTPS://). <br/><br/>Pokud ukládáte výstupní data v Azure, musíte zadat název účtu služby Azure storage, úložiště přístupový klíč a název kontejneru úložiště.</ul> |
| Databáze SQL |Přečte data, která je uložená ve službě Azure SQL database nebo databáze systému SQL Server běžící na virtuálním počítači Azure. |<b>Název databázového serveru</b>: Určuje název serveru, na kterém běží databáze.<br/><ul>V případě Azure SQL Database zadejte název serveru, který je generován. Obvykle má formát  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>V případě SQL serveru hostované na Azure Virtual machine zadejte *tcp:&lt;název DNS virtuálního počítače&gt;, 1433*</ul><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má oprávnění k přístupu k databázi. <br/><br/><b>Heslo uživatelského účtu serveru</b>: Určuje heslo pro uživatelský účet.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data chcete číst. |
| Místní databáze SQL |Přečte data, která je uložená do místní databáze SQL. |<b>Brána data gateway</b>: Určuje název brána správy dat nainstalovaná na počítači, kde má přístup k vaší databázi SQL serveru. Informace o nastavení brány najdete v tématu [provádět pokročilé analýzy pomocí služby Azure Machine Learning pomocí dat z SQL serveru v místním](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Název databázového serveru</b>: Určuje název serveru, na kterém běží databáze.<br/><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má oprávnění k přístupu k databázi. <br/><br/><b>Uživatelské jméno a heslo</b>: Klikněte na tlačítko <b>zadejte hodnoty</b> zadávat přihlašovací údaje databáze. Můžete použít integrované ověřování Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data chcete číst. |
| Tabulka Azure |Přečte data ze služby Table service ve službě Azure Storage.<br/><br/>Pokud načtete velké množství dat jen zřídka, použijte službu Azure Table Service. Nabízí flexibilní, nerelačních (NoSQL), řešení masivně škálovatelné, cenově dostupné a vysoce dostupné úložiště. |Možnosti **Import dat** měnit v závislosti na tom, zda přistupujete informací veřejného nebo privátního úložiště účtu, který vyžaduje přihlašovací údaje. Toto je určeno <b>typ ověřování</b> která může mít hodnotu "PublicOrSAS" nebo "Account", z nichž každá má svou vlastní sadu parametrů. <br/><br/><b>Veřejný nebo sdílený přístupový podpis (SAS) URI</b>: Parametry jsou:<br/><br/><ul><b>Tabulka URI</b>: Určuje Public nebo adresa URL SAS pro tabulku.<br/><br/><b>Určuje řádků, které chcete vyhledat názvy vlastností</b>: Hodnoty jsou <i>prvních n hodnot</i> kontrolovat zadaný počet řádků, nebo <i>ScanAll</i> zobrazíte všechny řádky v tabulce. <br/><br/>Pokud jsou data homogenní a předvídatelné, doporučuje se, že vyberete *prvních n hodnot* a zadejte číslo pro N. Pro velké tabulky to může způsobit rychlejší časy čtení.<br/><br/>Pokud se nastaví vlastnosti, které se liší v závislosti na hloubka a pozice tabulce strukturovaná data, zvolte *ScanAll* možnosti prohledávání všech řádků. Tím zajistíte integritu výsledný vlastností a metadat převodu.<br/><br/></ul><b>Účet úložiště privátního</b>: Parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje tabulku ke čtení.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidruženého k účtu.<br/><br/><b>Název tabulky</b> : Určuje název tabulky, která obsahuje data ke čtení.<br/><br/><b>Řádky, které chcete vyhledat názvy vlastností</b>: Hodnoty jsou <i>prvních n hodnot</i> kontrolovat zadaný počet řádků, nebo <i>ScanAll</i> zobrazíte všechny řádky v tabulce.<br/><br/>Pokud jsou data homogenní a předvídatelné, doporučujeme vám, že vyberete *prvních n hodnot* a zadejte číslo pro N. Pro velké tabulky to může způsobit rychlejší časy čtení.<br/><br/>Pokud se nastaví vlastnosti, které se liší v závislosti na hloubka a pozice tabulce strukturovaná data, zvolte *ScanAll* možnosti prohledávání všech řádků. Tím zajistíte integritu výsledný vlastností a metadat převodu.<br/><br/> |
| Azure Blob Storage |Přečte data uložená ve službě Blob service ve službě Azure Storage, včetně obrázků, ukládání nestrukturovaných textových nebo binárních dat.<br/><br/>Služba objektů Blob můžete použít veřejně zpřístupnit data, nebo k soukromému ukládání dat aplikací. Můžete přistupovat k datům z libovolného místa s využitím připojení protokolu HTTP nebo HTTPS. |Možnosti **Import dat** modulu změnit v závislosti na tom, zda přistupujete informací veřejného nebo privátního úložiště účtu, který vyžaduje přihlašovací údaje. Toto je určeno <b>typ ověřování</b> která může mít hodnotu "PublicOrSAS" nebo "Účet".<br/><br/><b>Veřejný nebo sdílený přístupový podpis (SAS) URI</b>: Parametry jsou:<br/><br/><ul><b>IDENTIFIKÁTOR URI</b>: Určuje Public nebo adresa URL SAS pro objekt blob úložiště.<br/><br/><b>Formát souborů</b>: Určuje formát dat ve službě Blob service. Podporované formáty jsou CSV, TSV a ARFF.<br/><br/></ul><b>Účet úložiště privátního</b>: Parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje objekt blob, který chcete číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidruženého k účtu.<br/><br/><b>Cesta ke kontejneru, adresáře nebo objekt blob </b> : Určuje název objektu blob, který obsahuje data ke čtení.<br/><br/><b>Formát souboru objektu BLOB</b>: Určuje formát dat ve službě blob service. Formáty dat podporovaných jsou uvedené kódování a Excel CSV, TSV, ARFF, sdíleného svazku clusteru. <br/><br/><ul>Pokud je ve formátu CSV nebo TSV, ujistěte se, k označení, zda soubor obsahuje řádek záhlaví.<br/><br/>Možnost aplikace Excel můžete použít ke čtení dat z Excelových sešitů. V <i>formát dat aplikace Excel</i> možnost určete, zda jsou data uložena v oblasti listu aplikace Excel nebo v Excelové tabulce. V <i>listu aplikace Excel nebo vložené tabulky </i>určete název listu nebo tabulky, který chcete číst z.</ul><br/> |
| Zprostředkovatel datových kanálů |Čte data z podporovaného poskytovatele informačního kanálu. Aktuálně se podporuje jenom formátu Open Data Protocol (OData). |<b>Datový typ obsahu, který</b>: Určuje formát protokolu OData.<br/><br/><b>Adresa URL zdroje</b>: Určuje úplnou adresu URL datového kanálu. <br/>Například na následující adrese URL čte z ukázkové databáze Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Import z jiného experimentu

Bude nastat situace, kdy budete chtít využít přechodný výsledek z jednoho experimentu a používat jako součást jiného experimentu. K tomuto účelu uložte modul jako datovou sadu:

1. Kliknutím na výstup modulu, který chcete uložit jako datovou sadu.
2. Klikněte na tlačítko **uložit jako datovou sadu**.
3. Po zobrazení výzvy zadejte název a popis, který by bylo možné snadno identifikovat datové sady.
4. Klikněte na tlačítko **OK** značky zaškrtnutí.

Po dokončení uložení datové sady bude k dispozici pro použití v rámci jakékoli experimentu do pracovního prostoru. Najdete ho v **uložení datové sady** seznamu paletě modulů.

## <a name="next-steps"></a>Další postup

[Nasazení webové služby Azure Machine Learning studio, používající importu a exportu dat modulů](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
