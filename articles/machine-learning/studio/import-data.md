---
title: Import trénovacích dat
titleSuffix: ML Studio (classic) - Azure
description: Jak importovat data do Azure Machine Learning Studio (klasické) z různých zdrojů dat. Zjistěte, jaké datové typy a formáty dat jsou podporovány.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: cee49124a7547399889e425008a8580b9b25945a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217983"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Import trénovacích dat do Azure Machine Learning Studio (klasické) z různých zdrojů dat

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Chcete-li použít vlastní data v Machine Learning Studio (klasické) k vývoji a trénování prediktivní ho řešení analýzy, můžete použít data z: 

* **Místní soubor** – načtení místních dat z pevného disku předem a vytvoření modulu datové sady v pracovním prostoru
* **Online zdroje dat** – Pomocí modulu [Import dat][import-data] můžete získat přístup k datům z jednoho z několika online zdrojů, zatímco experiment běží.
* **Machine Learning Studio (klasický) experiment** – použití dat, která byla uložena jako datová sada v Machine Learning Studio (klasické)
* [**Místní databáze SERVERU SQL Server**](use-data-from-an-on-premises-sql-server.md) – použití dat z místní databáze serveru SQL Server bez nutnosti ruční kopírování dat

> [!NOTE]
> Existuje celá řada ukázkových datových sad, které jsou k dispozici v Machine Learning Studio (klasické), které můžete použít pro trénovací data. Informace o nich najdete [v tématu použití ukázkových datových sad v Azure Machine Learning Studio (klasické)](use-sample-datasets.md).

## <a name="prepare-data"></a>Příprava dat

Machine Learning Studio (klasické) je navržen pro práci s obdélníková nebo tabulková data, jako jsou textová data, která jsou oddělena nebo strukturovaná data z databáze, i když v některých případech mohou být použita neobdélníková data.

Je to nejlepší, pokud vaše data jsou relativně čisté před importem do studia (klasické). Například budete chtít postarat o problémy, jako jsou nekotované řetězce.

Existují však moduly, které jsou k dispozici v aplikaci Studio (klasické), které umožňují některé manipulace s daty v rámci experimentu po importu dat. V závislosti na algoritmech strojového učení, které budete používat, se možná budete muset rozhodnout, jak budete zpracovávat strukturální problémy dat, jako jsou chybějící hodnoty a řídká data, a existují moduly, které s tím mohou pomoci. V části **Transformace dat** v paletě modulů se podívejte do modulů, které tyto funkce provádějí.

Kdykoli v experimentu můžete zobrazit nebo stáhnout data vytvořená modulem kliknutím na výstupní port. V závislosti na modulu mohou být k dispozici různé možnosti stahování nebo můžete být schopni vizualizovat data ve webovém prohlížeči ve studiu (classic).

## <a name="supported-data-formats-and-data-types"></a>Podporované formáty dat a datové typy

Do experimentu můžete importovat několik datových typů v závislosti na tom, jaký mechanismus používáte k importu dat a odkud pocházejí:

* Prostý text (.txt)
* Hodnoty oddělené čárkami (CSV) s hlavičkou (.csv) nebo bez (.nh.csv)
* Hodnoty oddělené tabulátory (TSV) se záhlavím (.tsv) nebo bez (.nh.tsv)
* Excelový soubor
* Tabulka Azure
* Hive tabulka
* Tabulka databáze SQL
* Hodnoty OData
* SVMLight data (.svmlight) (viz [definice SVMLight](http://svmlight.joachims.org/) pro informace o formátu)
* Data formátu souboru (ARFF) (.arff) (informace o formátu naleznete v [definici ARFF)](https://weka.wikispaces.com/ARFF)
* Soubor ZIP (.zip)
* R objekt nebo soubor pracovního prostoru (. RData)

Pokud importujete data ve formátu, jako je ARFF, který obsahuje metadata, Studio (klasické) používá tato metadata k definování nadpisu a datového typu každého sloupce.

Pokud importujete data, jako je formát TSV nebo CSV, který neobsahuje tato metadata, Studio (klasické) odvodí datový typ pro každý sloupec vzorkováním dat. Pokud data také nemají záhlaví sloupců, Studio (klasické) poskytuje výchozí názvy.

Nadpisy a datové typy sloupců můžete explicitně určit nebo změnit pomocí modulu [Upravit metadata.][edit-metadata]

Následující datové typy jsou rozpoznány Studio (klasické):

* Řetězec
* Integer
* Double
* Logická hodnota
* DateTime
* TimeSpan

Studio používá interní datový typ nazývaný ***datová tabulka*** k předání dat mezi moduly. Data můžete explicitně převést do formátu tabulky dat pomocí modulu [Převést na datovou sadu.][convert-to-dataset]

Jakýkoli modul, který přijímá formáty jiné než tabulka dat převede data do tabulky dat tiše před předáním do dalšího modulu.

V případě potřeby můžete převést formát tabulky dat zpět do formátu CSV, TSV, ARFF nebo SVMLight pomocí jiných konverzních modulů.
V části **Převody datových formátů** v paletě modulů vyhledejte moduly, které tyto funkce provádějí.

## <a name="data-capacities"></a>Datové kapacity

Moduly v Machine Learning Studio (klasické) podporují datové sady až 10 GB hustých číselných dat pro běžné případy použití. Pokud modul přijímá víc než jeden vstup, celková velikost všech vstupních velikostí je 10 GB. Větší datové sady můžete vzorkovat pomocí dotazů z Hive nebo Azure SQL Database, nebo můžete použít učení podle počtu předběžného zpracování před importem dat.  

Během normalizace funkcí je možné následující typy dat rozšířit do větších datových sad. Tyto typy jsou omezené na méně než 10 GB:

* Řídké
* Kategorické
* Řetězce
* Binární data

Následující moduly jsou omezené na datové sady menší než 10 GB:

* Doporučené moduly
* Modul SMOTE (Synthetic Minority Oversampling Technique)
* Skriptovací moduly: R, Python, SQL
* Moduly, kde velikost výstupních dat může být větší než velikost vstupních dat, třeba Join nebo Feature Hashing
* Pro velmi velký počet iterací Cross-validation, Tune Model Hyperparameters, Ordinal Regression a One-vs-All Multiclass

Pro datové sady, které jsou větší než pár GBs, nahrajte data do Azure Storage nebo Azure SQL Database nebo použijte Azure HDInsight, místo toho, abyste je nahrávali přímo z místního souboru.

Informace o obrazových datech naleznete v odkazu na modul [Importovat obrázky.](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)

## <a name="import-from-a-local-file"></a>Import z místního souboru

Datový soubor můžete nahrát z pevného disku a použít jej jako trénovací data ve Studiu (klasickém). Při importu datového souboru vytvoříte modul datové sady připravený k použití v experimentech v pracovním prostoru.

Chcete-li importovat data z místního pevného disku, postupujte takto:

1. Klikněte na **+NOVÝ** v dolní části okna Studio (klasické).
2. Vyberte **dataset** a **z místního souboru**.
3. V **dialogovém okně Nahrát novou datovou sadu** přejděte k souboru, který chcete nahrát.
4. Zadejte název, identifikujte datový typ a případně zadejte popis. Doporučuje se popis - umožňuje zaznamenávat všechny charakteristiky dat, které si chcete zapamatovat při používání dat v budoucnu.
5. Zaškrtávací políčko **Toto je nová verze existující datové sady** umožňuje aktualizovat existující datovou sadu s novými daty. Chcete-li tak učinit, zaškrtněte toto políčko a zadejte název existující datové sady.

![Nahrání nové datové sady](./media/import-data/upload-dataset-from-local-file.png)

Doba nahrávání závisí na velikosti dat a rychlosti připojení ke službě. Pokud víte, že soubor bude trvat dlouho, můžete dělat jiné věci uvnitř Studio (klasické) během čekání. Zavření prohlížeče před dokončením nahrávání dat však způsobí selhání nahrávání.

Jakmile jsou data nahrána, jsou uložena v modulu datové sady a jsou dostupná pro jakýkoli experiment ve vašem pracovním prostoru.

Při úpravách experimentu najdete datové sady, které jste nahráli, v seznamu **Moje datové sady** v seznamu Uložené datové **sady** v paletě modulů. Datovou sadu můžete přetáhnout na plátno experimentu, když chcete datovou sadu použít pro další analýzy a strojové učení.

## <a name="import-from-online-data-sources"></a>Import z online zdrojů dat

Pomocí modulu [Importovat data][import-data] může experiment importovat data z různých online zdrojů dat během experimentu.

> [!NOTE]
> Tento článek obsahuje obecné informace o modulu [Import dat.][import-data] Podrobnější informace o typech dat, ke kterých máte přístup, formáty, parametry a odpovědi na běžné otázky naleznete v tématu referenčního modulu modulu [Import dat.][import-data]

Pomocí modulu [Importovat data][import-data] můžete získat přístup k datům z jednoho z několika online zdrojů dat, když experiment běží:

* Webová adresa URL pomocí protokolu HTTP
* Hadoop pomocí HiveQL
* Azure Blob Storage
* Tabulka Azure
* Databáze Azure SQL nebo SQL Server na virtuálním počítači Azure
* Místní databáze serveru SQL Server
* Zprostředkovatel datového kanálu, OData v současné době
* Azure Cosmos DB

Vzhledem k tomu, že tato trénovací data jsou přístupná v době, kdy je experiment spuštěn, je k dispozici pouze v tomto experimentu. Pro srovnání, data, která byla uložena v modulu datové sady je k dispozici pro všechny experimenty ve vašem pracovním prostoru.

Chcete-li získat přístup ke zdrojům online dat v experimentu studia (klasický), přidejte do experimentu modul [Importovat data.][import-data] Pak vyberte **Spustit Průvodce importem dat** v části **Vlastnosti** pro podrobné pokyny s průvodcem pro výběr a konfiguraci zdroje dat. Případně můžete ručně vybrat **zdroj dat v** části **Vlastnosti** a zadat parametry potřebné pro přístup k datům.

Podporované zdroje dat online jsou rozepsány v následující tabulce. Tato tabulka také shrnuje formáty souborů, které jsou podporovány a parametry, které se používají pro přístup k datům.

> [!IMPORTANT]
> V současné době [importdat][import-data] a [export dat][export-data] moduly můžete číst a zapisovat data pouze z úložiště Azure vytvořené pomocí modelu nasazení Classic. Jinými slovy, nový typ účtu Azure Blob Storage, který nabízí úroveň přístupu k horkému úložiště nebo úroveň přístupu k skvělému úložišti, ještě není podporován.
>
> Obecně platí, že žádné účty úložiště Azure, které jste vytvořili před tím, než tato možnost služby bude k dispozici by neměla být ovlivněna.
> Pokud potřebujete vytvořit nový účet, vyberte **classic** pro model nasazení nebo použijte Správce prostředků a vyberte **obecné účely** spíše než úložiště **objektů blob** pro **typ účtu**.
>
> Další informace najdete v [tématu Azure Blob Storage: Hot a Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Podporované online zdroje dat
Modul Import **dat** Azure Machine Learning Studio (klasický) podporuje následující zdroje dat:

| Zdroj dat | Popis | Parametry |
| --- | --- | --- |
| Webová adresa URL přes HTTP |Čte data ve formátech CSV oddělených čárkami, hodnot oddělených tabulátory (TSV), formátu souborů vztahů s atributem (ARFF) a podpůrných vektorových počítačích (SVM-light) z libovolné webové adresy URL, která používá protokol HTTP |<b>Adresa URL</b>: Určuje úplný název souboru, včetně adresy URL webu a názvu souboru, s libovolnou příponou. <br/><br/><b>Formát dat</b>: Určuje jeden z podporovaných datových formátů: CSV, TSV, ARFF nebo SVM-light. Pokud data mají řádek záhlaví, slouží k přiřazení názvů sloupců. |
| Hadoop/HDFS |Čte data z distribuovaného úložiště v Hadoopu. Data, která chcete zadat, zadáte pomocí HiveQL, dotazovacího jazyka podobného SQL. HiveQL lze také použít k agregaci dat a provádění filtrování dat před přidáním dat do aplikace Studio (klasické). |<b>Dotaz databáze Hive</b>: Určuje dotaz Hive použitý ke generování dat.<br/><br/><b>Identifikátor URI serveru HCatalog</b> : Zadal název clusteru ve formátu * &lt;,azurehdinsight.net.&gt;*<br/><br/><b>Název uživatelského účtu Hadoop</b>: Určuje název uživatelského účtu Hadoop použitý ke zřízení clusteru.<br/><br/><b>Heslo uživatelského účtu Hadoop</b> : Určuje pověření použitá při zřizování clusteru. Další informace naleznete v [tématu Vytvoření clusterů Hadoop v HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Umístění výstupních dat</b>: Určuje, jestli jsou data uložená v distribuovaném systému souborů Hadoop (HDFS) nebo v Azure. <br/><ul>Pokud ukládáte výstupní data do HDFS, zadejte identifikátor URI serveru HDFS. (Nezapomeňte použít název clusteru HDInsight bez HTTPS:// předpony). <br/><br/>Pokud ukládáte výstupní data v Azure, musíte zadat název účtu úložiště Azure, přístupový klíč úložiště a název kontejneru úložiště.</ul> |
| Databáze SQL |Čte data uložená v databázi Azure SQL nebo v databázi SQL Serveru spuštěné na virtuálním počítači Azure. |<b>Název databázového serveru</b>: Určuje název serveru, na kterém je databáze spuštěna.<br/><ul>V případě Azure SQL Database zadejte název serveru, který je generován. Obvykle má formulář * &lt;generated_identifier&gt;.database.windows.net.* <br/><br/>V případě serveru SQL hostovaného na virtuálním počítači Azure zadejte *tcp:&lt;Název&gt;DNS virtuálního počítače 1433*</ul><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má přístupová oprávnění pro databázi. <br/><br/><b>Heslo uživatelského účtu serveru</b>: Určuje heslo pro uživatelský účet.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data, která chcete číst. |
| Místní databáze SQL |Čte data uložená v místní databázi SQL. |<b>Brána dat</b>: Určuje název brány pro správu dat nainstalované v počítači, kde má přístup k databázi serveru SQL Server. Informace o nastavení brány najdete v [tématu Provádění pokročilých analýz pomocí Azure Machine Learning Studio (klasické) pomocí dat z místního serveru SQL](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Název databázového serveru</b>: Určuje název serveru, na kterém je databáze spuštěna.<br/><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má přístupová oprávnění pro databázi. <br/><br/><b>Uživatelské jméno a heslo</b>: Kliknutím na <b>Zadat hodnoty</b> zadejte pověření databáze. Integrované ověřování systému Windows nebo ověřování serveru SQL server můžete použít v závislosti na konfiguraci místního serveru SQL Server.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data, která chcete číst. |
| Tabulka Azure |Čte data ze služby Table ve službě Azure Storage.<br/><br/>Pokud čtete velké množství dat zřídka, použijte Azure Table Service. Poskytuje flexibilní, nerelační (NoSQL), masivně škálovatelné, levné a vysoce dostupné úložné řešení. |Možnosti v **importu dat** se mění v závislosti na tom, zda přistupujete k veřejným informacím nebo k účtu soukromého úložiště, který vyžaduje přihlašovací údaje. To je určeno <b>typ ověřování,</b> který může mít hodnotu "PublicOrSAS" nebo "Účet", z nichž každý má svou vlastní sadu parametrů. <br/><br/><b>Identifikátor URI veřejného nebo sdíleného přístupového podpisu (SAS):</b>Parametry jsou:<br/><br/><ul><b>Identifikátor URI tabulky</b>: Určuje adresu URL veřejné tabulky nebo adresy SAS pro tabulku.<br/><br/><b>Určuje řádky, které mají být prohledány názvy vlastností</b>: Hodnoty jsou <i>TopN</i> pro skenování zadaného počtu řádků nebo <i>ScanAll</i> získat všechny řádky v tabulce. <br/><br/>Pokud jsou data homogenní a předvídatelná, doporučujeme vybrat *TopN* a zadat číslo pro N. U velkých tabulek to může mít za následek rychlejší čtení.<br/><br/>Pokud jsou data strukturována s sadami vlastností, které se liší v závislosti na hloubce a umístění tabulky, zvolte možnost *ScanAll* a proskenujte všechny řádky. Tím je zajištěna integrita výsledné vlastnosti a převod metadat.<br/><br/></ul><b>Účet soukromého úložiště</b>: Parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje tabulku ke čtení.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidružený k účtu.<br/><br/><b>Název tabulky</b> : Určuje název tabulky, která obsahuje data ke čtení.<br/><br/><b>Řádky pro skenování názvů vlastností</b>: Hodnoty jsou <i>TopN</i> pro skenování zadaného počtu řádků nebo <i>ScanAll</i> získat všechny řádky v tabulce.<br/><br/>Pokud jsou data homogenní a předvídatelná, doporučujeme vybrat *TopN* a zadat číslo pro N. U velkých tabulek to může mít za následek rychlejší čtení.<br/><br/>Pokud jsou data strukturována s sadami vlastností, které se liší v závislosti na hloubce a umístění tabulky, zvolte možnost *ScanAll* a proskenujte všechny řádky. Tím je zajištěna integrita výsledné vlastnosti a převod metadat.<br/><br/> |
| Azure Blob Storage |Čte data uložená ve službě Blob ve službě Azure Storage, včetně ibi, nestrukturovaného textu nebo binárních dat.<br/><br/>Službu Blob můžete použít k veřejnému zpřístupnění dat nebo k soukromému ukládání dat aplikací. K datům můžete přistupovat odkudkoli pomocí připojení HTTP nebo HTTPS. |Možnosti v modulu **Import dat** se mění v závislosti na tom, zda přistupujete k veřejným informacím nebo k účtu soukromého úložiště, který vyžaduje přihlašovací údaje. To je určeno <b>typu ověřování,</b> který může mít hodnotu "PublicOrSAS" nebo "Účet".<br/><br/><b>Identifikátor URI veřejného nebo sdíleného přístupového podpisu (SAS):</b>Parametry jsou:<br/><br/><ul><b>Identifikátor URI</b>: Určuje adresu URL veřejné ho úložiště nebo adresu SAS pro objekt blob úložiště.<br/><br/><b>Formát souboru</b>: Určuje formát dat ve službě Blob. Podporované formáty jsou CSV, TSV a ARFF.<br/><br/></ul><b>Účet soukromého úložiště</b>: Parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje objekt blob, který chcete číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidružený k účtu.<br/><br/><b>Cesta ke kontejneru, adresáři nebo objektu blob</b> : Určuje název objektu blob, který obsahuje data ke čtení.<br/><br/><b>Formát souboru objektů blob</b>: Určuje formát dat ve službě objektů blob. Podporované formáty dat jsou CSV, TSV, ARFF, CSV se zadaným kódováním a Excel. <br/><br/><ul>Pokud je formát CSV nebo TSV, nezapomeňte označit, zda soubor obsahuje řádek záhlaví.<br/><br/>Možnost Excelu můžete použít ke čtení dat ze sešitů aplikace Excel. V možnosti <i>Formát dat excelu</i> určete, jestli jsou data v oblasti listu Excelu nebo v excelové tabulce. V možnosti <i>Excel list nebo vložená tabulka </i>zadejte název listu nebo tabulky, ze které chcete číst.</ul><br/> |
| Zprostředkovatel datového kanálu |Čte data od podporovaného poskytovatele informačního kanálu. V současné době je podporován pouze formát Open Data Protocol (OData). |<b>Typ datového obsahu</b>: Určuje formát OData.<br/><br/><b>Zdrojová adresa URL</b>: Určuje úplnou adresu URL datového kanálu. <br/>Například následující adresa URL čte z ukázkové databáze Northwind:https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Import z jiného experimentu

Tam budou časy, kdy budete chtít, aby se zprostředkující výsledek z jednoho experimentu a použít jej jako součást jiného experimentu. Chcete-li to provést, uložte modul jako datovou sadu:

1. Klikněte na výstup modulu, který chcete uložit jako datovou sadu.
2. Klepněte na **tlačítko Uložit jako datovou sadu**.
3. Po zobrazení výzvy zadejte název a popis, který vám umožní snadno identifikovat datovou sadu.
4. Klikněte na zaškrtnutí **ok.**

Po dokončení ukládání bude datová sada k dispozici pro použití v rámci libovolného experimentu v pracovním prostoru. Najdete ji v seznamu **Uložené datové sady** v paletě modulů.

## <a name="next-steps"></a>Další kroky

[Nasazení webových služeb Azure Machine Learning Studio, které používají moduly importu dat a exportu dat](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
