---
title: Import trénovacích dat
titleSuffix: ML Studio (classic) - Azure
description: Jak importovat data do Azure Machine Learning Studio (Classic) z různých zdrojů dat. Zjistěte, jaké datové typy a datové formáty jsou podporovány.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 322dac1e2a3181dba58b518b142ea0fff2ac5de6
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153005"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Import školicích dat do Azure Machine Learning Studio (Classic) z různých zdrojů dat

Pokud chcete používat vlastní data v Machine Learning Studio (Classic) k vývoji a výukovém řešení prediktivní analýzy, můžete použít data z: 

* **Místní** načtení místních dat souborů z pevného disku na pevný disk pro vytvoření modulu DataSet v pracovním prostoru
* **Online zdroje dat** – použijte modul [importu dat][import-data] pro přístup k datům z jednoho z několika online zdrojů, zatímco je váš experiment spuštěný.
* **Experiment Machine Learning Studio (klasický)** – použití dat uložených jako datová sada v Machine Learning Studio (Classic)
* [**Místní SQL Server databáze**](use-data-from-an-on-premises-sql-server.md) – použití dat z místní databáze SQL Server bez nutnosti ručního kopírování dat

> [!NOTE]
> V Machine Learning Studio (Classic) je k dispozici několik ukázkových datových sad, které můžete použít pro školení dat. Další informace najdete v tématu [použití ukázkových datových sad v Azure Machine Learning Studio (Classic)](use-sample-datasets.md).

## <a name="prepare-data"></a>Příprava dat

Machine Learning Studio (Classic) je navržená tak, aby fungovala s pravoúhlými nebo tabelárními daty, jako jsou textová data, která jsou oddělená nebo strukturovaná data z databáze, i když v některých případech mohou být použita neobdélníková data.

Je nejvhodnější, pokud jsou data relativně čistá, než je naimportujete do studia (Classic). Například budete chtít pořídit problémy, jako jsou například řetězce v uvozovkách.

V aplikaci Studio (Classic) jsou však k dispozici moduly, které umožňují určitou manipulaci s daty v rámci experimentu po importu dat. V závislosti na algoritmech strojového učení, které budete používat, se možná budete muset rozhodnout, jak budete zpracovávat problémy se strukturou dat, jako jsou chybějící hodnoty a zhuštěná data, a moduly, které s ní můžou pomoci. Prohlédněte si část **transformace dat** v paletě modulu pro moduly, které provádějí tyto funkce.

V jakémkoli okamžiku experimentu můžete zobrazit nebo stáhnout data vytvořená modulem kliknutím na výstupní port. V závislosti na modulu mohou být k dispozici různé možnosti stahování nebo můžete vizualizovat data ve webovém prohlížeči v aplikaci Studio (Classic).

## <a name="supported-data-formats-and-data-types"></a>Podporované formáty dat a datové typy

Do experimentu můžete importovat několik datových typů, v závislosti na tom, jaký mechanismus používáte k importu dat a odkud pochází:

* Prostý text (. txt)
* Hodnoty oddělené čárkami (CSV) s hlavičkou (. csv) nebo bez (. NH. csv)
* Hodnoty oddělené tabulátory (TSV) s hlavičkou (. TSV) nebo bez (. NH. TSV)
* Excelový soubor
* Tabulka Azure
* Tabulka podregistru
* Tabulka SQL Database
* Hodnoty OData
* Data SVMLight (. SVMLight) (viz [definice SVMLight](http://svmlight.joachims.org/) pro informace o formátu)
* Data formátu souboru relací atributů (ARFF) (. arff) (viz [definice arff](https://weka.wikispaces.com/ARFF) pro informace o formátu)
* Soubor zip (. zip)
* Objekt R nebo soubor pracovního prostoru (. RData

Pokud importujete data ve formátu, jako je například ARFF, který obsahuje metadata, Studio (Classic) Tato metadata používá k definování nadpisu a datového typu každého sloupce.

Pokud importujete data jako formát TSV nebo CSV, který tato metadata neobsahuje, aplikace Studio (Classic) odvodí datový typ pro každý sloupec vzorkováním dat. Pokud data také nemají záhlaví sloupců, Studio (Classic) poskytuje výchozí názvy.

Můžete explicitně zadat nebo změnit záhlaví a datové typy pro sloupce pomocí modulu [Upravit metadata][edit-metadata] .

Studio (Classic) rozpozná následující typy dat:

* Řetězec
* Integer
* Double
* Logická hodnota
* DateTime
* TimeSpan

Studio používá k předávání dat mezi moduly interní datový typ nazvaný ***Tabulka dat*** . Data můžete explicitně převést do formátu tabulky dat pomocí modulu [převést na datovou sadu][convert-to-dataset] .

Libovolný modul, který přijímá jiné formáty než tabulka dat, převede data do tabulky dat před jejich předáním dalšímu modulu.

V případě potřeby můžete formát tabulky dat převést zpátky do formátu CSV, TSV, ARFF nebo SVMLight pomocí jiných modulů převodu.
Podívejte se na část s **převody formátů dat** v paletě modulu pro moduly, které tyto funkce provádějí.

## <a name="data-capacities"></a>Datové kapacity

Moduly v Machine Learning Studio (Classic) podporují datové sady až 10 GB hustých číselných dat pro běžné případy použití. Pokud modul přijímá víc než jeden vstup, celková velikost všech vstupních velikostí je 10 GB. Můžete vzorkovat větší datové sady pomocí dotazů z podregistru nebo Azure SQL Database, nebo můžete použít učení podle počtu předzpracování před importem dat.  

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

U datových sad, které jsou větší než několik GB, nahrajte data Azure Storage nebo Azure SQL Database nebo použijte Azure HDInsight místo nahrávání přímo z místního souboru.

Informace o datech imagí najdete v referenčních informacích k modulu [Import imagí](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importovat z místního souboru

Můžete nahrát datový soubor z pevného disku, který se použije jako školicí data v studiu (Classic). Když importujete datový soubor, vytvoříte modul DataSet připravený pro použití ve experimentech ve vašem pracovním prostoru.

Import dat z místního pevného disku, postupujte takto:

1. Klikněte na **+ Nový** ve spodní části okna Studio (Classic).
2. Vyberte **datovou sadu** a **z místního souboru**.
3. V dialogovém okně **nahrát novou datovou sadu** vyhledejte soubor, který chcete nahrát.
4. Zadejte název, identifikace typu dat a volitelně zadejte popis. Popis se doporučuje – umožňuje zaznamenat všechny charakteristiky data, která chcete mějte na paměti při používání dat v budoucnu.
5. Zaškrtávací políčko **Toto je nová verze existující datové sady** , umožňuje aktualizovat existující datovou sadu s novými daty. Uděláte to tak, že kliknete na toto políčko a pak zadáte název existující datové sady.

![Nahrát novou datovou sadu](./media/import-data/upload-dataset-from-local-file.png)

Nahrát doba závisí na velikosti dat a rychlosti vašeho připojení ke službě. Pokud víte, že soubor bude trvat dlouhou dobu, můžete v nástroji Studio (Classic) v průběhu čekání provádět další akce. Zavřením prohlížeče před dokončením nahrávání dat ale dojde k selhání nahrávání.

Po nahrání dat je uložený v modulu datová sada a je k dispozici žádné experimentu do pracovního prostoru.

Když upravujete experiment, můžete najít datové sady, které jste nahráli v seznamu **Moje datové sady** v seznamu **uložené datové sady** v paletě modulu. Můžete přetáhnout a vyřadit datovou sadu na plátno experimentu. Pokud chcete používat datovou sadu pro další analýzy s využitím a strojové učení.

## <a name="import-from-online-data-sources"></a>Import z online zdrojů dat

Pomocí modulu [Import dat][import-data] může experiment importovat data z různých online zdrojů dat, zatímco experiment běží.

> [!NOTE]
> Tento článek poskytuje obecné informace o modulu [Import dat][import-data] . Podrobnější informace o typech dat, ke kterým můžete získat přístup, formáty, parametry a odpovědi na běžné otázky, najdete v tématu Referenční příručka k modulu [Import dat][import-data] .

Pomocí modulu [Import dat][import-data] můžete získat přístup k datům z jednoho z několika online zdrojů dat, když je spuštěný experiment:

* Adresu URL webu pomocí protokolu HTTP
* Hadoop pomocí HiveQL
* Azure Blob Storage
* Tabulka Azure
* Azure SQL database nebo SQL Server na virtuálním počítači Azure
* Místní databáze SQL serveru
* Datový kanál zprostředkovatele, aktuálně OData
* Databáze Azure Cosmos

Vzhledem k tomu, že tato školicí data jsou k dispozici v době, kdy experiment běží, je k dispozici pouze v tomto experimentu. Porovnáním jsou data uložená v modulu DataSet dostupná pro libovolný experiment v pracovním prostoru.

Pokud chcete získat přístup k online zdrojům dat v experimentu studia (Classic), přidejte do experimentu modul [Import dat][import-data] . Pak vyberte **Spustit Průvodce importem dat** v části **vlastnosti** pro podrobné pokyny pro výběr a konfiguraci zdroje dat. Alternativně můžete ručně vybrat **zdroj dat** v části **vlastnosti** a zadat parametry potřebné pro přístup k datům.

V následující tabulce je uvedeno online zdroje dat, které jsou podporovány. Tato tabulka shrnuje také formátů souborů, které jsou podporovány a parametry, které se používají pro přístup k datům.

> [!IMPORTANT]
> Datové moduly [importu dat][import-data] a [exportu][export-data] v současné době mohou číst a zapisovat data pouze z úložiště Azure vytvořeného pomocí modelu nasazení Classic. Jinými slovy nový typ účtu úložiště objektů Blob v Azure, která nabízí úroveň přístupu horkého úložiště nebo studené úrovni přístupu úložiště zatím nepodporuje.
>
> Obecně platí, žádné účty úložiště Azure může mít vytvořené předtím, než tuto možnost služby jsou dostupné by neměly být ovlivněny.
> Pokud potřebujete vytvořit nový účet, vyberte pro model nasazení možnost **klasický** , nebo použijte Správce prostředků a pro **druh účtu**vyberte **obecný účel** místo **BLOB Storage** .
>
> Další informace najdete v tématu [Azure Blob Storage: horká a studená vrstva úložiště](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Podporované zdroje dat online
Modul **Import dat** Azure Machine Learning Studio (Classic) podporuje následující zdroje dat:

| Zdroj dat | Popis | Parametry |
| --- | --- | --- |
| Adresa URL webu přes protokol HTTP |Čte data v hodnot oddělených čárkami (CSV), hodnoty oddělené tabulátorem (TSV), atribut vztah formátu arff (Format) a formáty Support Vector počítače (SVM light) z jakékoli webové adresy URL, která používá protokol HTTP |<b>Adresa URL</b>: Určuje úplný název souboru včetně adresy URL webu a názvu souboru s libovolným rozšířením. <br/><br/><b>Formát dat</b>: Určuje jeden z podporovaných formátů dat: CSV, TSV, arff nebo SVM-Light. Pokud data obsahují řádek záhlaví, používá se k přiřazení názvy sloupců. |
| Hadoop/HDFS |Čte data z distribuovaného úložiště v systému Hadoop. Určíte data, která chcete, aby pomocí HiveQL SQL jako dotazovací jazyk. HiveQL lze také použít k agregaci dat a filtrování dat před přidáním dat do studia (Classic). |<b>Dotaz na databázi podregistru</b>: určuje dotaz na podregistr použitý k vygenerování dat.<br/><br/><b>HCatalog serveru \ identifikátor URI</b> : zadaný název clusteru ve formátu *&lt;název clusteru&gt;. azurehdinsight.NET.*<br/><br/><b>Název uživatelského účtu Hadoop</b>: Určuje název uživatelského účtu Hadoop, který se používá k zřízení clusteru.<br/><br/><b>Heslo uživatelského účtu Hadoop</b> : Určuje přihlašovací údaje, které se použijí při zřizování clusteru. Další informace najdete v tématu [vytváření clusterů Hadoop ve službě HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Umístění výstupních dat</b>: Určuje, jestli jsou data uložená v systému Hadoop Distributed File System (HDFS) nebo v Azure. <br/><ul>Pokud uchováváte výstupní data v HDFS, zadejte identifikátor URI serveru HDFS. (Nezapomeňte použít název clusteru HDInsight bez předpony HTTPS://). <br/><br/>Pokud ukládáte výstupní data v Azure, musíte zadat název účtu služby Azure storage, úložiště přístupový klíč a název kontejneru úložiště.</ul> |
| Databáze SQL |Přečte data, která je uložená ve službě Azure SQL database nebo databáze systému SQL Server běžící na virtuálním počítači Azure. |<b>Název databázového serveru</b>: Určuje název serveru, na kterém je databáze spuštěná.<br/><ul>V případě Azure SQL Database zadejte název serveru, který je generován. Obvykle má formulář *&lt;generated_identifier&gt;. Database.Windows.NET.* <br/><br/>V případě SQL serveru hostovaného na virtuálním počítači Azure zadejte *TCP:&lt;název DNS virtuálního počítače&gt;, 1433*</ul><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má oprávnění pro přístup k databázi. <br/><br/><b>Heslo k uživatelskému účtu serveru</b>: Určuje heslo pro uživatelský účet.<br/><br/><b>Databázový dotaz</b>: zadejte příkaz SQL, který popisuje data, která chcete číst. |
| Místní databáze SQL |Přečte data, která je uložená do místní databáze SQL. |<b>Brána dat</b>: určuje název Správa dat brány nainstalované v počítači, kde má přístup k databázi SQL Server. Informace o nastavení brány najdete v tématu [provádění pokročilých analýz pomocí Azure Machine Learning Studio (Classic) pomocí dat z místního SQL serveru](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Název databázového serveru</b>: Určuje název serveru, na kterém je databáze spuštěná.<br/><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má oprávnění pro přístup k databázi. <br/><br/><b>Uživatelské jméno a heslo</b>: kliknutím na <b>zadat hodnoty</b> zadejte svoje přihlašovací údaje do databáze. Můžete použít integrované ověřování Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.<br/><br/><b>Databázový dotaz</b>: zadejte příkaz SQL, který popisuje data, která chcete číst. |
| Tabulka Azure |Přečte data ze služby Table service ve službě Azure Storage.<br/><br/>Pokud načtete velké množství dat jen zřídka, použijte službu Azure Table Service. Nabízí flexibilní, nerelačních (NoSQL), řešení masivně škálovatelné, cenově dostupné a vysoce dostupné úložiště. |Možnosti v části **Import dat** se mění v závislosti na tom, zda přistupujete k veřejným informacím nebo privátnímu účtu úložiště, který vyžaduje přihlašovací údaje. To je určeno <b>typem ověřování</b> , který může mít hodnotu "PublicOrSAS" nebo "Account", z nichž každá má svou vlastní sadu parametrů. <br/><br/><b>Identifikátor URI veřejného nebo sdíleného přístupového podpisu (SAS)</b>: parametry:<br/><br/><ul><b>Identifikátor URI tabulky</b>: Určuje veřejnou adresu URL nebo adresu URL SAS pro tabulku.<br/><br/><b>Určuje řádky, ve kterých se mají hledat názvy vlastností</b>: hodnoty jsou <i>nejlepší</i> ke skenování zadaného počtu řádků nebo <i>ScanAll</i> k získání všech řádků v tabulce. <br/><br/>Pokud jsou data homogenní a předvídatelné, doporučuje se vybrat *nejlepší* a zadat číslo pro N. U rozsáhlých tabulek to může vést k rychlejšímu čtení času.<br/><br/>Pokud jsou data strukturovaná pomocí sad vlastností, které se liší v závislosti na hloubkě a umístění tabulky, vyberte možnost *ScanAll* pro kontrolu všech řádků. Tím zajistíte integritu výsledný vlastností a metadat převodu.<br/><br/></ul><b>Privátní účet úložiště</b>: parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje tabulku, která se má číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidružený k účtu.<br/><br/><b>Název tabulky</b> : Určuje název tabulky obsahující data, která se mají číst.<br/><br/><b>Řádky, ve kterých se mají hledat názvy vlastností</b>: hodnoty jsou <i>nejlepší</i> ke skenování zadaného počtu řádků nebo <i>ScanAll</i> k získání všech řádků v tabulce.<br/><br/>Pokud jsou data homogenní a předvídatelné, doporučujeme vybrat *nejlepší* a zadat číslo pro N. U rozsáhlých tabulek to může vést k rychlejšímu čtení času.<br/><br/>Pokud jsou data strukturovaná pomocí sad vlastností, které se liší v závislosti na hloubkě a umístění tabulky, vyberte možnost *ScanAll* pro kontrolu všech řádků. Tím zajistíte integritu výsledný vlastností a metadat převodu.<br/><br/> |
| Azure Blob Storage |Přečte data uložená ve službě Blob service ve službě Azure Storage, včetně obrázků, ukládání nestrukturovaných textových nebo binárních dat.<br/><br/>Služba objektů Blob můžete použít veřejně zpřístupnit data, nebo k soukromému ukládání dat aplikací. Můžete přistupovat k datům z libovolného místa s využitím připojení protokolu HTTP nebo HTTPS. |Možnosti v modulu **importovat data** se mění v závislosti na tom, jestli přistupujete k veřejným informacím nebo privátnímu účtu úložiště, který vyžaduje přihlašovací údaje. To je určeno <b>typem ověřování</b> , který může mít hodnotu buď "PublicOrSAS" nebo "Account".<br/><br/><b>Identifikátor URI veřejného nebo sdíleného přístupového podpisu (SAS)</b>: parametry:<br/><br/><ul><b>URI</b>: Určuje adresu URL veřejného nebo SAS pro objekt BLOB úložiště.<br/><br/><b>Formát souboru</b>: Určuje formát dat v BLOB Service. Podporované formáty jsou CSV, TSV a ARFF.<br/><br/></ul><b>Privátní účet úložiště</b>: parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje objekt blob, který chcete číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidružený k účtu.<br/><br/><b>Cesta k kontejneru, adresáři nebo objektu BLOB</b> : Určuje název objektu blob, který obsahuje data, která se mají číst.<br/><br/><b>Formát souboru BLOB</b>: Určuje formát dat ve službě BLOB Service. Formáty dat podporovaných jsou uvedené kódování a Excel CSV, TSV, ARFF, sdíleného svazku clusteru. <br/><br/><ul>Pokud je ve formátu CSV nebo TSV, ujistěte se, k označení, zda soubor obsahuje řádek záhlaví.<br/><br/>Možnost aplikace Excel můžete použít ke čtení dat z Excelových sešitů. V možnosti <i>Formát excelových dat</i> určete, zda jsou data v oblasti listu aplikace Excel nebo v excelové tabulce. V <i>listu aplikace Excel nebo v možnosti vložená tabulka </i>zadejte název listu nebo tabulky, ze které chcete číst.</ul><br/> |
| Zprostředkovatel datových kanálů |Čte data z podporovaného poskytovatele informačního kanálu. Aktuálně se podporuje jenom formátu Open Data Protocol (OData). |<b>Datový typ obsahu</b>: Určuje formát OData.<br/><br/><b>Zdrojová adresa URL</b>: Určuje úplnou adresu URL pro datový kanál. <br/>Například následující adresa URL se načte z ukázkové databáze Northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importovat z jiného experimentu

V takovém případě budete chtít vzít v úvahu průběžný výsledek z jednoho experimentu a použít ho jako součást jiného experimentu. Chcete-li to provést, Uložte modul jako datovou sadu:

1. Klikněte na výstup modulu, který chcete uložit jako datovou sadu.
2. Klikněte na **Uložit jako datovou sadu**.
3. Po zobrazení výzvy zadejte název a popis, který vám umožní snadno identifikovat datovou sadu.
4. Zaškrtněte políčko **OK** .

Po dokončení ukládání bude datová sada dostupná pro použití v rámci libovolného experimentu v pracovním prostoru. Můžete ji najít v seznamu **uložených datových sad** v paletě modulu.

## <a name="next-steps"></a>Další kroky

[Nasazení Azure Machine Learning Studio webové služby, které používají moduly importu a exportu dat](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
