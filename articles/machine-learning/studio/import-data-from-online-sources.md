---
title: Import dat do nástroje Machine Learning Studio z online zdrojů dat | Dokumentace Microsoftu
description: Jak import cvičných dat Azure Machine Learning Studio z různých online zdrojů.
keywords: Importujte dat, formát dat, datové typy, zdroje dat, trénovacích dat
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: f48bb2e81114793c1c28227aacebaeedce693d63
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820932"
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Import dat do nástroje Azure Machine Learning Studio z různých online zdrojů dat pomocí modulu Import Dat
Tento článek popisuje podporu pro import online data z různých zdrojů a informace potřebné k přesunu dat z těchto zdrojů do experimentu Azure Machine Learning.

> [!NOTE]
> Tento článek obsahuje obecné informace o [Import dat] [ import-data] modulu. Podrobné informace o typech dat můžete přistupovat, formátů, parametry a odpovědi na běžné dotazy, naleznete v tématu referenční modulu pro [Import dat] [ import-data] modulu.
> 
> 

## <a name="introduction"></a>Úvod
S použitím [Import dat] [ import-data] modul, můžete přístup k datům z jednoho z několika zdrojů dat online spuštěného experimentu [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* Adresu URL webu pomocí protokolu HTTP
* Hadoop pomocí HiveQL
* Úložiště blobů v Azure
* Tabulka Azure
* Azure SQL database nebo SQL Server na virtuálním počítači Azure
* Místní databáze SQL serveru
* Datový kanál zprostředkovatele, aktuálně OData
* Azure Cosmos DB

Chcete-li získat přístup k online zdroje dat v experimentu Studio, přidejte [Import dat] [ import-data] modulu, vyberte **zdroj dat**a pak zadejte parametry potřebné pro přístup k data. V následující tabulce je uvedeno online zdroje dat, které jsou podporovány. Tato tabulka shrnuje také formátů souborů, které jsou podporovány a parametry, které se používají pro přístup k datům.

Mějte na paměti, protože tento trénovacích dat se přistupuje během spuštění experimentu, je dostupná jenom v testu. Naproti tomu data, která jsou uložená v modulu datové sady jsou k dispozici žádné experimentu do pracovního prostoru.

> [!IMPORTANT]
> V současné době [Import dat] [ import-data] a [exportovat Data] [ export-data] může číst a zapisovat data pouze z úložiště Azure, které jsou vytvořeny pomocí klasického moduly model nasazení. Jinými slovy nový typ účtu úložiště objektů Blob v Azure, která nabízí úroveň přístupu horkého úložiště nebo studené úrovni přístupu úložiště zatím nepodporuje. 
> 
> Obecně platí, žádné účty úložiště Azure může mít vytvořené předtím, než tuto možnost služby jsou dostupné by neměly být ovlivněny. 
> Pokud je potřeba vytvořit nový účet, vyberte **Classic** pro nasazení modelu, nebo použijte Resource manager a vyberte **Obecné** spíše než **úložiště objektů Blob** pro  **Druh účtu**. 
> 
> Další informace najdete v tématu [Azure Blob Storage: horká a studená úroveň úložiště](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Podporované zdroje dat online
Azure Machine Learning **Import dat** modul podporuje následující zdroje dat:

| Zdroj dat | Popis | Parametry |
| --- | --- | --- |
| Adresa URL webu přes protokol HTTP |Čte data v hodnot oddělených čárkami (CSV), hodnoty oddělené tabulátorem (TSV), atribut vztah formátu arff (Format) a formáty Support Vector počítače (SVM light) z jakékoli webové adresy URL, která používá protokol HTTP |<b>Adresa URL</b>: Určuje úplný název souboru, včetně adresy URL webu a název souboru s jakoukoli příponou. <br/><br/><b>Formát dat</b>: Určuje jeden z podporovaných data formátů: CSV, TSV, ARFF nebo SVM-light. Pokud data obsahují řádek záhlaví, používá se k přiřazení názvy sloupců. |
| Hadoop/HDFS |Čte data z distribuovaného úložiště v systému Hadoop. Určíte data, která chcete, aby pomocí HiveQL SQL jako dotazovací jazyk. HiveQL slouží také k agregovat data a provádět filtrování předtím, než přidáte data do nástroje Machine Learning Studio dat. |<b>Databázový dotaz Hive</b>: Určuje sloužící ke generování dat dotaz Hive.<br/><br/><b>Identifikátor URI serveru HCatalog </b> : Zadaný název vašeho clusteru ve formátu  *&lt;název clusteru&gt;. azurehdinsight.net.*<br/><br/><b>Název uživatelského účtu systému Hadoop</b>: Určuje název uživatelského účtu systému Hadoop, slouží ke zřizování clusteru.<br/><br/><b>Heslo uživatelského účtu systému Hadoop</b> : Určuje přihlašovací údaje použité při zřizování clusteru. Další informace najdete v tématu [vytváření clusterů Hadoop v HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Umístění výstupních dat</b>: Určuje, zda jsou data uložená v Hadoop distributed file system (HDFS) nebo v Azure. <br/><ul>Pokud uchováváte výstupní data v HDFS, zadejte identifikátor URI serveru HDFS. (Nezapomeňte použít název clusteru HDInsight bez předpony HTTPS://). <br/><br/>Pokud ukládáte výstupní data v Azure, musíte zadat název účtu služby Azure storage, úložiště přístupový klíč a název kontejneru úložiště.</ul> |
| SQL database |Přečte data, která je uložená ve službě Azure SQL database nebo databáze systému SQL Server běžící na virtuálním počítači Azure. |<b>Název databázového serveru</b>: Určuje název serveru, na kterém běží databáze.<br/><ul>V případě Azure SQL Database zadejte název serveru, který je generován. Obvykle má formát  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>V případě SQL serveru hostované na Azure Virtual machine zadejte *tcp:&lt;název DNS virtuálního počítače&gt;, 1433*</ul><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má oprávnění k přístupu k databázi. <br/><br/><b>Heslo uživatelského účtu serveru</b>: Určuje heslo pro uživatelský účet.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data chcete číst. |
| Místní databáze SQL |Přečte data, která je uložená do místní databáze SQL. |<b>Brána data gateway</b>: Určuje název brána správy dat nainstalovaná na počítači, kde má přístup k vaší databázi SQL serveru. Informace o nastavení brány najdete v tématu [provádět pokročilé analýzy pomocí služby Azure Machine Learning pomocí dat z SQL serveru v místním](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Název databázového serveru</b>: Určuje název serveru, na kterém běží databáze.<br/><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má oprávnění k přístupu k databázi. <br/><br/><b>Uživatelské jméno a heslo</b>: klikněte na tlačítko <b>zadejte hodnoty</b> zadávat přihlašovací údaje databáze. Můžete použít integrované ověřování Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data chcete číst. |
| Tabulka Azure |Přečte data ze služby Table service ve službě Azure Storage.<br/><br/>Pokud načtete velké množství dat jen zřídka, použijte službu Azure Table Service. Nabízí flexibilní, nerelačních (NoSQL), řešení masivně škálovatelné, cenově dostupné a vysoce dostupné úložiště. |Možnosti **Import dat** měnit v závislosti na tom, zda přistupujete informací veřejného nebo privátního úložiště účtu, který vyžaduje přihlašovací údaje. Toto je určeno <b>typ ověřování</b> která může mít hodnotu "PublicOrSAS" nebo "Account", z nichž každá má svou vlastní sadu parametrů. <br/><br/><b>Veřejný nebo sdíleného přístupového podpisu (SAS) URI</b>: parametry jsou:<br/><br/><ul><b>Tabulka URI</b>: Určuje Public nebo adresa URL SAS pro tabulku.<br/><br/><b>Určuje řádků, které chcete vyhledat názvy vlastností</b>: hodnoty jsou <i>prvních n hodnot</i> kontrolovat zadaný počet řádků, nebo <i>ScanAll</i> zobrazíte všechny řádky v tabulce. <br/><br/>Pokud jsou data homogenní a předvídatelné, doporučuje se, že vyberete *prvních n hodnot* a zadejte číslo pro N. Pro velké tabulky to může způsobit rychlejší časy čtení.<br/><br/>Pokud se nastaví vlastnosti, které se liší v závislosti na hloubka a pozice tabulce strukturovaná data, zvolte *ScanAll* možnosti prohledávání všech řádků. Tím zajistíte integritu výsledný vlastností a metadat převodu.<br/><br/></ul><b>Účet úložiště privátního</b>: parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje tabulku ke čtení.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidruženého k účtu.<br/><br/><b>Název tabulky</b> : Určuje název tabulky, která obsahuje data ke čtení.<br/><br/><b>Řádky, které chcete vyhledat názvy vlastností</b>: hodnoty jsou <i>prvních n hodnot</i> kontrolovat zadaný počet řádků, nebo <i>ScanAll</i> zobrazíte všechny řádky v tabulce.<br/><br/>Pokud jsou data homogenní a předvídatelné, doporučujeme vám, že vyberete *prvních n hodnot* a zadejte číslo pro N. Pro velké tabulky to může způsobit rychlejší časy čtení.<br/><br/>Pokud se nastaví vlastnosti, které se liší v závislosti na hloubka a pozice tabulce strukturovaná data, zvolte *ScanAll* možnosti prohledávání všech řádků. Tím zajistíte integritu výsledný vlastností a metadat převodu.<br/><br/> |
| Azure Blob Storage |Přečte data uložená ve službě Blob service ve službě Azure Storage, včetně obrázků, ukládání nestrukturovaných textových nebo binárních dat.<br/><br/>Služba objektů Blob můžete použít veřejně zpřístupnit data, nebo k soukromému ukládání dat aplikací. Můžete přistupovat k datům z libovolného místa s využitím připojení protokolu HTTP nebo HTTPS. |Možnosti **Import dat** modulu změnit v závislosti na tom, zda přistupujete informací veřejného nebo privátního úložiště účtu, který vyžaduje přihlašovací údaje. Toto je určeno <b>typ ověřování</b> která může mít hodnotu "PublicOrSAS" nebo "Účet".<br/><br/><b>Veřejný nebo sdíleného přístupového podpisu (SAS) URI</b>: parametry jsou:<br/><br/><ul><b>Identifikátor URI</b>: Určuje Public nebo adresa URL SAS pro objekt blob úložiště.<br/><br/><b>Formát souborů</b>: Určuje formát dat ve službě Blob service. Podporované formáty jsou CSV, TSV a ARFF.<br/><br/></ul><b>Účet úložiště privátního</b>: parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje objekt blob, který chcete číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidruženého k účtu.<br/><br/><b>Cesta ke kontejneru, adresáře nebo objekt blob </b> : Určuje název objektu blob, který obsahuje data ke čtení.<br/><br/><b>Formát souboru objektu BLOB</b>: Určuje formát dat ve službě blob service. Formáty dat podporovaných jsou uvedené kódování a Excel CSV, TSV, ARFF, sdíleného svazku clusteru. <br/><br/><ul>Pokud je ve formátu CSV nebo TSV, ujistěte se, k označení, zda soubor obsahuje řádek záhlaví.<br/><br/>Možnost aplikace Excel můžete použít ke čtení dat z Excelových sešitů. V <i>formát dat aplikace Excel</i> možnost určete, zda jsou data uložena v oblasti listu aplikace Excel nebo v Excelové tabulce. V <i>listu aplikace Excel nebo vložené tabulky </i>určete název listu nebo tabulky, který chcete číst z.</ul><br/> |
| Zprostředkovatel datových kanálů |Čte data z podporovaného poskytovatele informačního kanálu. Aktuálně se podporuje jenom formátu Open Data Protocol (OData). |<b>Datový typ obsahu, který</b>: Určuje formát protokolu OData.<br/><br/><b>Adresa URL zdroje</b>: Určuje úplnou adresu URL datového kanálu. <br/>Například na následující adrese URL čte z ukázkové databáze Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Další postup

[Nasazení webové služby Azure ML, používajících moduly Import dat a Export dat](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
