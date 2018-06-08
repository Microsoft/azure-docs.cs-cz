---
title: Umožňuje importovat data do nástroje Machine Learning Studio ze zdroje dat online | Microsoft Docs
description: Jak importovat data školení Azure Machine Learning Studio z různých zdrojů online.
keywords: Importujte dat, formát dat, datové typy, zdroje dat, Cvičná data
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: ab806f8191bc8edc4d5be6003cd177d8c24d71e7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834671"
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Import dat do nástroje Azure Machine Learning Studio z různých online zdrojů dat pomocí modulu Import Dat
Tento článek popisuje podporu pro import dat online z různých zdrojů a informace potřebné pro přesun dat z těchto zdrojů do experimentu Azure Machine Learning.

> [!NOTE]
> Tento článek poskytuje obecné informace o [importovat Data] [ import-data] modulu. Podrobné informace o typech dat dostanete, formátů, parametry a odpovědi na časté otázky, naleznete v tématu referenční modulu pro [importovat Data] [ import-data] modulu.
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Úvod
Pomocí [importovat Data] [ import-data] modul, můžete přistupujete k datům z několika zdrojů dat online spuštěného experimentu [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* Adresa URL webového pomocí protokolu HTTP
* Hadoop pomocí HiveQL
* Úložiště blobů v Azure
* Tabulka Azure
* Azure SQL database nebo SQL Server na virtuálním počítači Azure
* Místní databáze systému SQL Server
* Datový kanál poskytovatele, aktuálně OData
* Azure Cosmos DB

Chcete-li přistupovat ke zdrojům dat online v experimentu Studio, přidejte [importovat Data] [ import-data] modulu vaše, vyberte **zdroj dat**a pak zadejte parametry, které jsou potřebné pro přístup k data. Zdroje dat online, které jsou podporovány je uvedeno v následující tabulce. Tato tabulka shrnuje formáty souborů, které jsou podporovány a parametry, které se používají pro přístup k datům.

Všimněte si, že vzhledem k tomu, že tato data školení přistupuje spuštěného experimentu, je k dispozici pouze v tomto experimentu. Pro srovnání data, která byla uložena v modulu datové sady jsou k dispozici žádné experimentu v pracovním prostoru.

> [!IMPORTANT]
> V současné době [importovat Data] [ import-data] a [Export dat] [ export-data] moduly lze číst a zapisovat data pouze z úložiště Azure, které jsou vytvořené pomocí Classic model nasazení. Jinými slovy nový typ účtu úložiště objektů Blob Azure, která nabízí úroveň přístupu horkého úložiště nebo úroveň přístupu studeného úložiště ještě nepodporuje. 
> 
> Obecně platí, všechny účty úložiště Azure, že jste mohli vytvořit předtím, než jsou k dispozici možnost této služby by neměl mít vliv. 
> Pokud potřebujete vytvořit nový účet, vyberte **Classic** pro nasazení modelu, nebo pomocí Správce prostředků a vyberte **obecné účely** místo **úložiště objektů Blob** pro  **Účet typu**. 
> 
> Další informace najdete v tématu [Azure Blob Storage: horká a studená vrstvy úložiště](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Podporované zdroje dat online
Azure Machine Learning **importovat Data** modul podporuje následující zdroje dat:

| Zdroj dat | Popis | Parametry |
| --- | --- | --- |
| Adresa URL webové prostřednictvím protokolu HTTP |Čte data hodnot oddělených čárkami (CSV), hodnoty oddělené tabulátory (TSV), formát souboru atribut vztah (ARFF) a formáty Support Vector počítače (SVM-light) z jakékoli webové adresy URL, která používá protokol HTTP |<b>Adresa URL</b>: Určuje úplný název souboru, včetně názvu souboru s jakoukoli příponou a adresu URL webu. <br/><br/><b>Formát dat</b>: Určuje jeden z podporovaných datových formátů: CSV, TSV, ARFF nebo SVM-light. Pokud data má řádek záhlaví, je použít k přiřazování názvy sloupců. |
| Hadoop/HDFS |Čte data z distribuovaného úložiště v Hadoop. Můžete zadat data, která chcete pomocí HiveQL, jako SQL dotazovací jazyk. HiveQL lze také provést filtrování předtím, než přidáte data do nástroje Machine Learning Studio dat a agregovat data. |<b>Databázový dotaz Hive</b>: Určuje dotaz Hive sloužící ke generování data.<br/><br/><b>Identifikátor URI serveru HCatalog </b> : Zadaný název clusteru formátu  *&lt;název clusteru&gt;. azurehdinsight.net.*<br/><br/><b>Název uživatelského účtu Hadoop</b>: Určuje název účtu uživatele Hadoop používají ke zřízení clusteru.<br/><br/><b>Heslo uživatelského účtu Hadoop</b> : Určuje, že pověření použitá při zřizování clusteru. Další informace najdete v tématu [vytvoření Hadoop clusterů v HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Umístění výstupu dat</b>: Určuje, zda jsou data uložená v Hadoop distributed file system (HDFS) nebo v Azure. <br/><ul>Pokud ukládáte výstupní data v HDFS, zadejte identifikátor URI serveru HDFS. (Nezapomeňte použít název clusteru HDInsight bez předponu HTTPS://). <br/><br/>Pokud výstupní data ukládáte v Azure, musíte zadat název účtu úložiště Azure, přístupový klíč k úložišti a název kontejneru úložiště.</ul> |
| SQL database |Čte data, která je uložená v databázi Azure SQL nebo v databázi systému SQL Server spuštěna na virtuálním počítači Azure. |<b>Název databázového serveru</b>: Určuje název serveru, na kterém běží databáze.<br/><ul>V případě Azure SQL Database zadejte název serveru, aby se vygenerovala. Obvykle je formulář  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>V případě systému SQL server, který je hostitelem je Azure virtuálního počítače zadejte *tcp:&lt;název DNS virtuálního počítače&gt;, 1433*</ul><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má přístupová oprávnění k databázi. <br/><br/><b>Heslo uživatelského účtu serveru</b>: Určuje heslo pro uživatelský účet.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data chcete číst. |
| Místní databáze SQL |Čte data, která je uložená v místní databázi. |<b>Brána data gateway</b>: Určuje název brány pro správu dat nainstalovat do počítače, kde má přístup k databázi SQL serveru. Informace o nastavení brány najdete v tématu [provést pokročilou analýzu pomocí Azure Machine Learning pomocí dat z SQL serveru místní](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Název databázového serveru</b>: Určuje název serveru, na kterém běží databáze.<br/><br/><b>Název databáze </b>: Určuje název databáze na serveru. <br/><br/><b>Název uživatelského účtu serveru</b>: Určuje uživatelské jméno pro účet, který má přístupová oprávnění k databázi. <br/><br/><b>Uživatelské jméno a heslo</b>: klikněte na tlačítko <b>zadejte hodnoty</b> zadat přihlašovací údaje databáze. Můžete použít integrované ověřování systému Windows nebo ověřování systému SQL Server v závislosti na konfiguraci vaší místní SQL Server.<br/><br/><b>Databázový dotaz</b>: Zadejte příkaz SQL, který popisuje data chcete číst. |
| Tabulka Azure |Čte data ze služby Table v Azure Storage.<br/><br/>Pokud si přečíst velké objemy dat zřídka, použijte služby Azure Table. Poskytuje flexibilní, nerelačních (NoSQL), řešení nesmírně škálovatelná služba, nenákladné a vysoce dostupné úložiště. |Možnosti v **importovat Data** změnit v závislosti na tom, zda přistupujete k informací veřejného nebo soukromého skladování účet, který vyžaduje přihlašovací údaje. To je dáno <b>typ ověřování</b> která může mít hodnotu "PublicOrSAS" nebo "Account", z nichž každá má svou vlastní sadu parametrů. <br/><br/><b>Veřejné nebo sdíleného přístupového podpisu (SAS) URI</b>: parametry jsou:<br/><br/><ul><b>Tabulka URI</b>: Určuje veřejné nebo SAS adresa URL pro tuto tabulku.<br/><br/><b>Určuje řádky, které vyhledat názvy vlastností</b>: hodnoty jsou <i>TopN</i> ke kontrole zadaný počet řádků, nebo <i>ScanAll</i> získat všechny řádky v tabulce. <br/><br/>Pokud jsou data homogenní a předvídatelné, je vhodné vybrat *TopN* a zadejte číslo pro N. Pro rozsáhlé tabulky to může způsobit rychlejší doby čtení.<br/><br/>Pokud se nastaví vlastnosti, které se liší v závislosti na hloubka a umístění tabulky strukturovaná data, vyberte *ScanAll* možnosti prohledávání všechny řádky. Tím se zajistí integritu výsledné vlastnost a převod metadat.<br/><br/></ul><b>Účet úložiště privátního</b>: parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje tabulku, kterou chcete číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidružené k účtu.<br/><br/><b>Název tabulky</b> : Určuje název tabulky, která obsahuje data ke čtení.<br/><br/><b>Řádky, které chcete vyhledat názvy vlastností</b>: hodnoty jsou <i>TopN</i> ke kontrole zadaný počet řádků, nebo <i>ScanAll</i> získat všechny řádky v tabulce.<br/><br/>Pokud se data homogenní a předvídatelné, doporučujeme vybrat *TopN* a zadejte číslo pro N. Pro rozsáhlé tabulky to může způsobit rychlejší doby čtení.<br/><br/>Pokud se nastaví vlastnosti, které se liší v závislosti na hloubka a umístění tabulky strukturovaná data, vyberte *ScanAll* možnosti prohledávání všechny řádky. Tím se zajistí integritu výsledné vlastnost a převod metadat.<br/><br/> |
| Azure Blob Storage |Čte data uložená ve službě Blob v Azure Storage, včetně obrázků, nestrukturovaných textu nebo binárních dat.<br/><br/>Služba objektů Blob můžete použít veřejně vystavit data a soukromě ukládání dat aplikací. Data můžete přístup odkudkoli pomocí připojení HTTP a HTTPS. |Možnosti v **importovat Data** modulu změnit v závislosti na tom, zda přistupujete k informací veřejného nebo soukromého skladování účet, který vyžaduje přihlašovací údaje. To je dáno <b>typ ověřování</b> která může mít hodnotu "PublicOrSAS" nebo "Účet".<br/><br/><b>Veřejné nebo sdíleného přístupového podpisu (SAS) URI</b>: parametry jsou:<br/><br/><ul><b>Identifikátor URI</b>: Určuje veřejné nebo SAS adresa URL pro tento objekt blob úložiště.<br/><br/><b>Formát souboru</b>: Určuje formát dat ve službě Blob. Podporované formáty jsou sdíleného svazku clusteru, TSV a ARFF.<br/><br/></ul><b>Účet úložiště privátního</b>: parametry jsou: <br/><br/><ul><b>Název účtu</b>: Určuje název účtu, který obsahuje objekt blob, který chcete číst.<br/><br/><b>Klíč účtu</b>: Určuje klíč úložiště přidružené k účtu.<br/><br/><b>Cesta ke kontejneru, adresáře nebo objekt blob </b> : Určuje název objektu blob, který obsahuje data ke čtení.<br/><br/><b>Formát souboru BLOB</b>: Určuje formát dat ve službě blob. Podporované datové formáty jsou sdíleného svazku clusteru, TSV, ARFF, CSV s zadané kódování a Excel. <br/><br/><ul>Pokud je ve formátu CSV nebo TSV, ujistěte se, že jste označuje, zda tento soubor obsahuje řádek záhlaví.<br/><br/>Možnost aplikace Excel můžete číst data ze sešitů aplikace Excel. V <i>formát dat aplikace Excel</i> možnost, znamenat, zda jsou data uložena v oblasti sešitu aplikace Excel nebo v tabulce aplikace Excel. V <i>listu aplikace Excel nebo vložené tabulky </i>možnost, zadejte název listu nebo tabulky, který chcete číst z.</ul><br/> |
| Zprostředkovatel dat informačního kanálu |Čte data z podporovaných informačního kanálu zprostředkovatele. Aktuálně se podporuje jenom formát Open Data Protocol (OData). |<b>Data obsahu typu</b>: Určuje formát OData.<br/><br/><b>Adresa URL zdroje</b>: Určuje úplnou adresu URL pro datový kanál. <br/>Například následující adresu URL čte z ukázková databáze Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Další postup

[Nasazení webové služby Azure ML, které používají Data importu a exportu dat moduly](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
