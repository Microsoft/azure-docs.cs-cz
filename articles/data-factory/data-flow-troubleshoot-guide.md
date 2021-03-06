---
title: Řešení potíží s daty mapování toků
description: Naučte se řešit problémy toku dat v Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.openlocfilehash: 72ab685b58f7d940fe4d682cacba6212fe80ced8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933079"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Řešení potíží s mapováním toků dat v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro mapování toků dat v Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Běžné kódy chyb a zprávy 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kód chyby: DF-exekutor-SourceInvalidPayload
- **Zpráva**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.
- **Příčina**: datová sada obsahuje kontejner, který neexistuje v úložišti.
- **Doporučení**: Ujistěte se, že kontejner odkazovaný ve vaší datové sadě existuje a je k němu k dispozici.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kód chyby: DF-exekutor-SystemInvalidJson

- **Zpráva**: Chyba analýzy JSON, nepodporované kódování nebo víceřádkové
- **Příčina**: možné problémy se souborem JSON: nepodporované kódování, poškozené bajty nebo použití zdroje JSON jako jednoho dokumentu na mnoha vnořených řádcích.
- **Doporučení**: Ověřte, zda je kódování souboru JSON podporováno. Ve zdrojové transformaci, která používá datovou sadu JSON, rozbalte **Nastavení JSON** a zapněte **jeden dokument**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kód chyby: DF-exekutor-BroadcastTimeout

- **Zpráva**: Chyba vypršení časového limitu spojení všesměrového vysílání, ujistěte se, že Stream všesměrového vysílání generuje data během 60 sekund v ladění běhu a 300 s ve spuštění úlohy
- **Příčina**: všesměrové vysílání má výchozí časový limit 60 sekund na běhu ladění a 300 sekund při spuštění úlohy. Datový proud vybraný pro vysílání je příliš velký, aby mohl v rámci tohoto limitu vydávat data.
- **Doporučení**: Podívejte se na kartu **optimalizace** v části transformace toku dat pro spojení, EXISTS a vyhledávání. Výchozí možnost pro všesměrové vysílání je **Automatická**. Pokud je nastavená možnost **automaticky** , nebo pokud ručně nastavujete levou nebo pravou stranu na všesměrové **vysílání, můžete** buď nastavit větší konfiguraci prostředí Azure Integration runtime (IR), nebo vypnout všesměrové vysílání. Pro dosažení nejlepšího výkonu v tokůch dat doporučujeme, abyste povolili Spark pro všesměrové vysílání pomocí **auto** a používat paměťově optimalizovanou Azure IR. 
 
  Pokud spouštíte tok dat v rámci testovacího běhu ladicího programu z ladicího kanálu, může se tato podmínka spouštět častěji. Důvodem je to, že Azure Data Factory omezí časový limit vysílání na 60 sekund, aby bylo udržování rychlejšího prostředí ladění. Časový limit můžete roztáhnout na časový limit 300. sekundy aktivovaného spuštění. K tomu můžete použít možnost **ladit**  >  **použití aktivity použít** k použití Azure IR definovaného v aktivitě kanálu spuštění toku dat.

- **Zpráva**: Chyba časového limitu připojení všesměrového vysílání. Chcete-li se tomuto problému vyhnout, můžete zvolit možnost Vypnuto u možnosti vysílání v nástroji JOIN/Exists nebo v transformaci vyhledávání. Pokud máte v úmyslu vysílat spojení s cílem zvýšit výkon, zajistěte, aby Stream všesměrového vysílání mohl vytvářet data během 60 sekund v ladicích běhůch a 300 sekund ve spuštění úlohy.
- **Příčina**: všesměrové vysílání má výchozí časový limit 60 sekund v běhu ladění a 300 sekund ve spuštění úlohy. V připojení všesměrového vysílání je datový proud, který je vybraný pro vysílání, příliš velký na to, aby v rámci tohoto limitu vytvořil data. Pokud se připojení všesměrového vysílání nepoužívá, může výchozí vysílání datového toku dosáhnout stejného limitu.
- **Doporučení**: Vypněte možnost vysílání nebo Vyhněte se všesměrovému vysílání velkých datových proudů, pro které může zpracování trvat déle než 60 sekund. Vyberte menší datový proud, který se má vysílat. Velké Azure SQL Data Warehouse tabulky a zdrojové soubory nejsou obvykle dobrou možností. Pokud k této chybě dojde, použijte při absenci připojení všesměrového vysílání větší cluster.

### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-prováděcí-převod

- **Zpráva**: převod na datum nebo čas se nezdařil z důvodu neplatného znaku.
- **Příčina**: data nejsou v očekávaném formátu.
- **Doporučení**: Použijte správný datový typ.

### <a name="error-code-df-executor-invalidcolumn"></a>Kód chyby: DF-exekutor-InvalidColumn
- **Zpráva**: v dotazu je nutné zadat název sloupce, nastavit alias, pokud se používá funkce SQL.
- **Příčina**: není zadán žádný název sloupce.
- **Doporučení**: Pokud používáte funkci SQL jako min () nebo Max (), nastavte alias.

### <a name="error-code-df-executor-drivererror"></a>Kód chyby: DF-exekutor-DriverError
- **Zpráva**: INT96 je starší typ časového razítka, který není podporován TOKEM dat ADF. Zvažte prosím možnost upgradovat typ sloupce na nejnovější typy.
- **Příčina**: Chyba ovladače.
- **Doporučení**: INT96 je starší typ časového razítka, který není podporován Azure Data Factory toku dat. Zvažte možnost upgradovat typ sloupce na nejnovější typ.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kód chyby: DF-exekutor-BlockCountExceedsLimitError
- **Zpráva**: počet nepotvrzených bloků nemůže překročit maximální limit 100 000 bloků. Ověřte konfiguraci objektu BLOB.
- **Příčina**: maximální počet nepotvrzených bloků v objektu blob je 100 000.
- **Doporučení**: Další informace o tomto problému získáte od Microsoft Product Team.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kód chyby: DF-exekutor-PartitionDirectoryError
- **Zpráva**: Zadaná cesta ke zdroji má buď více adresářů s oddíly (například <Source Path> /<kořenový adresář 1>/a = 10/b = 20, <Source Path> /<kořenového adresáře oddílu 2>/c = 10/d = 30) nebo adresář s oddíly s jiným souborem nebo nerozděleným adresářem (například <Source Path> /<kořenový adresář 1>/a = 10/b = 20, <Source Path> /Directory 2/Soubor1), odeberte kořenový adresář oddílu ze zdrojové cesty a přečtěte ho pomocí samostatné transformace zdroje.
- **Příčina**: zdrojová cesta má buď více oddílů rozdělených na oddíly, nebo oddílů s oddíly, které mají jiný soubor nebo adresář bez oddílů.
- **Doporučení**: Odeberte kořenový adresář oddílů ze zdrojové cesty a přečtěte ho pomocí samostatné transformace zdroje.

### <a name="error-code-df-executor-invalidtype"></a>Kód chyby: DF-exekutor-InvalidType
- **Zpráva**: Ujistěte se prosím, že typ parametrů odpovídá typu předané hodnoty. Předávání parametrů float z kanálů není aktuálně podporováno.
- **Příčina**: datový typ pro deklarovaný typ není kompatibilní se skutečnou hodnotou parametru.
- **Doporučení**: Ověřte, zda hodnoty parametrů předané do toku dat odpovídají deklarovanému typu.

### <a name="error-code-df-executor-parseerror"></a>Kód chyby: DF-exekutor-ParseError
- **Zpráva**: výraz nejde analyzovat.
- **Příčina**: výraz vygeneroval chyby při analýze z důvodu nesprávného formátování.
- **Doporučení**: ve výrazu ověřte formátování.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-exekutor-SystemImplicitCartesian
- **Zpráva**: implicitní kartézském produkt pro vnitřní spojení není podporován, místo toho použijte vzájemné spojení. Sloupce používané ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčina**: implicitní kartézském produkty pro vnitřní spojení mezi logickými plány nejsou podporovány. Pokud ve spojení používáte sloupce, vytvořte jedinečný klíč.
- **Doporučení**: u spojení, která nejsou založená na rovnosti, použijte vzájemné spojení.

### <a name="error-code-getcommand-outputasync-failed"></a>Kód chyby: GetCommand OutputAsync se nezdařilo.
- **Zpráva**: během ladění a náhledu dat toku dat došlo k chybě příkazu GetCommand OutputAsync...
- **Příčina**: Tato chyba je chybná služba back-end. 
- **Doporučení**: zkuste operaci zopakovat a restartujte relaci ladění. Pokud se problém nevyřeší, obraťte se na zákaznickou podporu. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Kód chyby: DF-exekutor-OutOfMemoryError
 
- **Zpráva**: během provádění došlo k chybě clusteru z důvodu nedostatku paměti, zkuste to prosím znovu s prostředím Integration runtime s větším počtem jader nebo s paměťově optimalizovaným výpočetním typem.
- **Příčina**: cluster nemá dostatek paměti.
- **Doporučení**: clustery ladění jsou určeny pro vývoj. Pro spuštění datové části použijte vzorkování dat a příslušný výpočetní typ a velikost. Tipy ke zvýšení výkonu najdete v tématu [Průvodce mapováním výkonu toku dat](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Kód chyby: DF-exekutor-illegalArgument

- **Zpráva**: Ujistěte se prosím, že přístupový klíč v propojené službě je správný.
- **Příčina**: název účtu nebo přístupový klíč nejsou správné.
- **Doporučení**: Ujistěte se, že název účtu nebo přístupový klíč zadaný v propojené službě jsou správné. 

### <a name="error-code-df-executor-columnunavailable"></a>Kód chyby: DF-exekutor-ColumnUnavailable
- **Zpráva**: název sloupce použitý ve výrazu není k dispozici nebo je neplatný.
- **Příčina**: ve výrazu je použit neplatný nebo nedostupný název sloupce.
- **Doporučení**: Ověřte názvy sloupců používané ve výrazech.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Kód chyby: DF-exekutor-OutOfDiskSpaceError
- **Zpráva**: interní chyba serveru
- **Příčina**: v clusteru dochází místo na disku.
- **Doporučení**: zkuste kanál zopakovat. Pokud se tak problém nevyřeší, obraťte se na zákaznickou podporu.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Kód chyby: DF-exekutor-StoreIsNotDefined
- **Zpráva**: konfigurace úložiště není definována. Tato chyba je potenciálně způsobena neplatným přiřazením parametru v kanálu.
- **Příčina**: Neurčeno.
- **Doporučení**: Ověřte přiřazení hodnoty parametru v kanálu. Výraz parametru může obsahovat neplatné znaky.


### <a name="error-code-4502"></a>Kód chyby: 4502
- **Zpráva**: existují značná souběžná spuštění MappingDataflow, která způsobují selhání kvůli omezení v Integration runtime.
- **Příčina**: v prostředí Integration runtime se současně objevuje velký počet spuštění aktivit toku dat. Další informace najdete v tématu [omezení Azure Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Doporučení**: Pokud chcete souběžně spustit více aktivit toku dat, rozdělte je mezi několik prostředí Integration runtime.


### <a name="error-code-invalidtemplate"></a>Kód chyby: InvalidTemplate
- **Zpráva**: výraz kanálu nelze vyhodnotit.
- **Příčina**: výraz kanálu předaný do aktivity toku dat není zpracován správně z důvodu chyby syntaxe.
- **Doporučení**: Zkontrolujte aktivitu v monitorování aktivit a ověřte výraz.

### <a name="error-code-2011"></a>Kód chyby: 2011
- **Zpráva**: aktivita běžela na Azure Integration runtime a nedokázala dešifrovat přihlašovací údaje úložiště dat nebo výpočetní prostředky připojené prostřednictvím Integration runtime v místním prostředí. Zkontrolujte prosím konfiguraci propojených služeb přidružených k této aktivitě a ujistěte se, že používáte správný typ prostředí Integration runtime.
- **Příčina**: tok dat nepodporuje propojené služby v místních prostředích Integration runtime.
- **Doporučení**: Nakonfigurujte tok dat pro spuštění ve spravovaném prostředí integration Virtual Network.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Kód chyby: DF-XML-InvalidValidationMode
- **Zpráva**: byl zadán neplatný režim ověřování XML.
- **Doporučení**: ověřte hodnotu parametru a zadejte správný režim ověřování.

### <a name="error-code-df-xml-invaliddatafield"></a>Kód chyby: DF-XML-InvalidDataField
- **Zpráva**: pole pro poškozené záznamy musí být typu řetězec a může mít hodnotu null.
- **Doporučení**: Ujistěte se, že sloupec `\"_corrupt_record\"` ve zdrojovém projektu má datový typ String.

### <a name="error-code-df-xml-malformedfile"></a>Kód chyby: DF-XML-MalformedFile
- **Zpráva**: nesprávně vytvořený kód XML v ' FailFastMode '.
- **Doporučení**: aktualizujte obsah souboru XML do správného formátu.

### <a name="error-code-df-xml-invaliddatatype"></a>Kód chyby: DF-XML-InvalidDataType
- **Message**: element XML má dílčí elementy nebo atributy a nemůže být převeden.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Kód chyby: DF-XML-InvalidReferenceResource
- **Zpráva**: prostředek reference v datovém souboru XML nelze přeložit.
- **Doporučení**: měli byste ověřit referenční prostředek v datovém souboru XML.

### <a name="error-code-df-xml-invalidschema"></a>Kód chyby: DF-XML-InvalidSchema
- **Zpráva**: ověření schématu se nezdařilo.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Kód chyby: DF-XML-UnsupportedExternalReferenceResource
- **Zpráva**: prostředek externího odkazu v datovém souboru XML není podporován.
- **Doporučení**: aktualizujte obsah souboru XML, pokud není prostředek externích referencí teď podporovaný.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Kód chyby: DF-GEN2-InvalidAccountConfiguration
- **Zpráva**: měla by se zadat buď klíč účtu, nebo tenant/SpnId/SpnCredential/spnCredentialType, nebo MiServiceUri/miServiceToken.
- **Doporučení**: Nakonfigurujte správný účet v související propojené službě Gen2.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Kód chyby: DF-GEN2-InvalidAuthConfiguration
- **Zpráva**: je možné zadat jenom jednu ze tří metod ověřování (klíč, SERVICEPRINCIPAL a mi). 
- **Doporučení**: v související propojené službě Gen2 vyberte správný typ ověřování.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Kód chyby: DF-GEN2-InvalidServicePrincipalCredentialType
- **Zpráva**: ServicePrincipalCredentialType je neplatná.

### <a name="error-code-df-gen2-invaliddatatype"></a>Kód chyby: DF-GEN2-InvalidDataType
- **Zpráva**: typ cloudu je neplatný.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Kód chyby: DF-BLOB-InvalidAccountConfiguration
- **Zpráva**: měla by být zadána jedna z klíčů účtu nebo sas_token.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Kód chyby: DF-BLOB-InvalidAuthConfiguration
- **Zpráva**: je možné zadat jenom jednu ze dvou metod ověřování (klíč, SAS).

### <a name="error-code-df-blob-invaliddatatype"></a>Kód chyby: DF-BLOB-InvalidDataType
- **Zpráva**: typ cloudu je neplatný.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Kód chyby: DF-Cosmos-PartitionKeyMissed
- **Zpráva**: cesta ke klíči oddílu by měla být zadána pro operace aktualizace a odstranění.
- **Doporučení**: v nastavení jímky Cosmos použijte zadání klíče oddílu.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Kód chyby: DF-Cosmos-InvalidPartitionKey
- **Zpráva**: cesta ke klíči oddílu nemůže být pro operace aktualizace a odstranění prázdná.
- **Doporučení**: v nastavení jímky Cosmos použijte zadání klíče oddílu.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Kód chyby: DF-Cosmos-IdPropertyMissed
- **Message**: vlastnost ID by měla být namapována pro operace odstranění a aktualizace.
- **Doporučení**: Ujistěte se, že vstupní data obsahují `id` sloupec v nastavení jímky Cosmos. Pokud ne, použijte **transformaci SELECT nebo odvoditelné** pro vygenerování tohoto sloupce před jímky.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Kód chyby: DF-Cosmos-InvalidPartitionKeyContent
- **Zpráva**: klíč oddílu by měl začínat na/.
- **Doporučení**: Zajistěte, aby klíč oddílu začínal `/` v nastavení jímky Cosmos, například: `/movieId` .

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Kód chyby: DF-Cosmos-InvalidPartitionKey
- **Zpráva**: partitionKey není mapován v jímky pro operace odstranění a aktualizace.
- **Doporučení**: v nastavení jímky Cosmos použijte klíč oddílu, který je stejný jako klíč oddílu vašeho kontejneru.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Kód chyby: DF-Cosmos-InvalidConnectionMode
- **Zpráva**: neplatný connectionMode.
- **Doporučení**: potvrďte, že podporovaný režim je **Gateway** a **DirectHttps** v nastavení Cosmos.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Kód chyby: DF-Cosmos-InvalidAccountConfiguration
- **Zpráva**: je třeba zadat buď parametr Account nebo accountEndpoint.

### <a name="error-code-df-github-writenotsupported"></a>Kód chyby: DF-GitHub-WriteNotSupported
- **Zpráva**: úložiště GitHub nepovoluje zápisy.

### <a name="error-code-df-pgsql-invalidcredential"></a>Kód chyby: DF-PGSQL-InvalidCredential
- **Zpráva**: je třeba zadat uživatele nebo heslo.
- **Doporučení**: Ujistěte se, že máte v související propojené službě PostgreSQL k dispozici správná nastavení přihlašovacích údajů.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Kód chyby: DF-Snowflake-InvalidStageConfiguration
- **Zpráva**: jako fázi v Snowflake operace čtení/zápisu se dá použít jenom typ úložiště BLOB.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Kód chyby: DF-Snowflake-InvalidStageConfiguration
- **Zpráva**: vlastnosti fáze Snowflake by měly být zadány pomocí Azure Blob + ověřování SAS.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Kód chyby: DF-Snowflake-InvalidDataType
- **Zpráva**: typ Spark není v Snowflake podporován.
- **Doporučení**: použijte **transformaci odvozenou** pro změnu sloupce vstupu vstupních dat na typ řetězce před Snowflake jímka. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Kód chyby: DF-podregistr-InvalidBlobStagingConfiguration
- **Zpráva**: je třeba zadat vlastnosti pracovního objektu BLOB Storage.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Kód chyby: DF-podregistr-InvalidGen2StagingConfiguration
- **Zpráva**: adls Gen2 úložiště podporuje pouze pověření klíčového objektu služby.
- **Doporučení**: Ověřte, že jste v propojené službě adls Gen2 použili přihlašovací údaje klíčového objektu, který se používá jako pracovní.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Kód chyby: DF-podregistr-InvalidGen2StagingConfiguration
- **Zpráva**: je třeba zadat vlastnosti přípravy adls Gen2 úložiště. Je vyžadován buď jeden klíč, nebo tenant/spnId/spnKey nebo miServiceUri/miServiceToken.
- **Doporučení**: použijte správné přihlašovací údaje, které se používají jako pracovní v podregistru v související adls Gen2 propojené službě. 

### <a name="error-code-df-hive-invaliddatatype"></a>Kód chyby: DF-podregistr-InvalidDataType
- **Zpráva**: nepodporované sloupce.
- **Doporučení**: aktualizujte sloupec vstupních dat tak, aby odpovídal datovému typu podporovanému podregistru.

### <a name="error-code-df-hive-invalidstoragetype"></a>Kód chyby: DF-podregistr-InvalidStorageType
- **Zpráva**: typ úložiště může být buď blob, nebo Gen2.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Kód chyby: DF-relimited – InvalidConfiguration
- **Zpráva**: měla by být zadána buď jedna z prázdných řádků, nebo vlastní záhlaví.
- **Doporučení**: v nastavení sdíleného svazku clusteru zadejte prázdné řádky nebo vlastní záhlaví.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Kód chyby: DF-relimited – ColumnDelimiterMissed
- **Zpráva**: pro analýzu je vyžadován Oddělovač sloupců.
- **Doporučení**: potvrďte, že máte ve svém nastavení sdíleného svazku clusteru Oddělovač sloupců.

### <a name="error-code-df-mssql-invalidcredential"></a>Kód chyby: DF-MSSQL-InvalidCredential
- **Zpráva**: měla by být zadána buď jedna z uživatelů/PWD, nebo tenant/SpnId/SpnKey nebo MiServiceUri/miServiceToken.
- **Doporučení**: použijte správné přihlašovací údaje v související propojené službě MSSQL.

### <a name="error-code-df-mssql-invaliddatatype"></a>Kód chyby: DF-MSSQL-InvalidDataType
- **Zpráva**: Nepodporovaná pole (polí).
- **Doporučení**: Upravte sloupec vstupní data tak, aby odpovídal datovému typu podporovanému serverem MSSQL.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Kód chyby: DF-MSSQL-InvalidAuthConfiguration
- **Zpráva**: je možné zadat jenom jednu ze tří metod ověřování (klíč, SERVICEPRINCIPAL a mi).
- **Doporučení**: v související propojené službě MSSQL můžete zadat jenom jednu ze tří metod ověřování (Key, SERVICEPRINCIPAL a mi).

### <a name="error-code-df-mssql-invalidcloudtype"></a>Kód chyby: DF-MSSQL-InvalidCloudType
- **Zpráva**: typ cloudu je neplatný.
- **Doporučení**: Ověřte typ cloudu v související propojené službě MSSQL.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Kód chyby: DF-SQLDW-InvalidBlobStagingConfiguration
- **Zpráva**: je třeba zadat vlastnosti pracovního objektu BLOB Storage.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Kód chyby: DF-SQLDW-InvalidStorageType
- **Zpráva**: typ úložiště může být buď blob, nebo Gen2.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Kód chyby: DF-SQLDW-InvalidGen2StagingConfiguration
- **Zpráva**: adls Gen2 úložiště podporuje pouze pověření klíčového objektu služby.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Kód chyby: DF-SQLDW-InvalidConfiguration
- **Zpráva**: je třeba zadat vlastnosti přípravy adls Gen2 úložiště. Je požadován buď klíč, nebo tenant/spnId/spnCredential/spnCredentialType nebo miServiceUri/miServiceToken.

### <a name="error-code-df-delta-invalidconfiguration"></a>Kód chyby: DF-DELTA-InvalidConfiguration
- **Zpráva**: časová razítka a verze se nedají nastavit ve stejnou dobu.

### <a name="error-code-df-delta-keycolumnmissed"></a>Kód chyby: DF-DELTA-KeyColumnMissed
- **Zpráva**: pro operace, které nelze vkládat, by měly být zadány klíčové sloupce.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Kód chyby: DF-DELTA-InvalidTableOperationSettings
- **Zpráva**: možnosti opětovného vytvoření a zkrácení nelze zadat současně.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Kód chyby: DF-Excel-WorksheetConfigMissed
- **Zpráva**: vyžaduje se název nebo index excelového listu.
- **Doporučení**: ověřte hodnotu parametru a zadáním názvu nebo indexu listu načtěte data z Excelu.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Kód chyby: DF-Excel-InvalidWorksheetConfiguration
- **Zpráva**: název a index listu aplikace Excel nemohou existovat současně.
- **Doporučení**: ověřte hodnotu parametru a zadáním názvu nebo indexu listu načtěte data z Excelu.

### <a name="error-code-df-excel-invalidrange"></a>Kód chyby: DF-Excel-InvalidRange
- **Zpráva**: je zadaný neplatný rozsah.
- **Doporučení**: ověřte hodnotu parametru a zadejte platný rozsah podle následujícího odkazu: [excelový formát v Azure Data Factory-Dataset Properties](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Kód chyby: DF-Excel-WorksheetNotExist
- **Zpráva**: excelový list neexistuje.
- **Doporučení**: ověřte hodnotu parametru a zadáním platného názvu nebo indexu listu načtěte data z Excelu.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Kód chyby: DF-Excel-DifferentSchemaNotSupport
- **Zpráva**: čtení souborů aplikace Excel s jiným schématem není nyní podporováno.

### <a name="error-code-df-excel-invaliddatatype"></a>Kód chyby: DF-Excel-InvalidDataType
- **Zpráva**: datový typ není podporován.

### <a name="error-code-df-excel-invalidfile"></a>Kód chyby: DF-Excel-InvalidFile
- **Zpráva**: je zadaný neplatný excelový soubor, ale podporují se jenom soubory. xlsx a. xls.


## <a name="miscellaneous-troubleshooting-tips"></a>Různé tipy pro řešení potíží
- **Problém**: došlo k neočekávané výjimce a spuštění selhalo.
    - **Zpráva**: během provádění aktivity toku dat došlo k neočekávané výjimce a spuštění se nezdařilo.
    - **Příčina**: Tato chyba je chybná služba back-end. Zkuste operaci zopakovat a restartujte relaci ladění.
    - **Doporučení**: Pokud opakováním a restartováním problém nevyřešíte, obraťte se na zákaznickou podporu.

-  **Problém**: během ladění nejsou k dispozici žádná výstupní data při připojení Preview.
    - **Zpráva**: existuje velký počet hodnot null nebo chybějící hodnoty, které mohou být způsobeny příliš malým počtem řádků vzorků. Zkuste aktualizovat limit řádků ladění a aktualizovat data.
    - **Příčina**: podmínka spojení buď neodpovídala žádnému řádku, nebo způsobila velké množství hodnot null během náhledu dat.
    - **Doporučení**: v **nastavení ladění** zvyšte počet řádků v limitu zdrojového řádku. Nezapomeňte vybrat Azure IR, která má cluster toku dat dostatečně velký pro zpracování více dat.
    
- **Problém**: Chyba ověřování ve zdroji pomocí víceřádkových souborů CSV. 
    - **Zpráva**: může se zobrazit jedna z těchto chybových zpráv:
        - Poslední sloupec má hodnotu null nebo chybí.
        - Ověření schématu ve zdroji se nezdařilo.
        - Import schématu se nepovede správně zobrazit v uživatelském prostředí a poslední sloupec má v názvu znak nového řádku.
    - **Příčina**: v toku dat mapování nejsou v současné době při použití hodnoty \r\n jako oddělovače řádků v současnosti fungovat zdrojové soubory CSV ve více řádcích. Někdy mohou způsobit chyby nadbytečné řádky při návratu na začátek řádku. 
    - **Doporučení**: vygenerujte soubor ve zdroji pomocí znaku \n jako oddělovače řádků místo \r\n. Nebo pomocí aktivity kopírování převeďte soubor CSV tak, aby používal \n jako oddělovač řádků.

## <a name="general-troubleshooting-guidance"></a>Obecné pokyny k odstraňování potíží
1. Ověřte stav připojení datové sady. V každé transformaci zdroje a jímky použijte propojenou službu pro každou datovou sadu, kterou používáte, a otestujte připojení.
2. Ověřte stav připojení k souborům a tabulkám v Návrháři toku dat. V režimu ladění vyberte možnost **data Preview** na vašich zdrojových transformacích, abyste měli jistotu, že budete mít přístup k datům.
3. Pokud vše vypadá správně v náhledu dat, přejdete do návrháře kanálů a zadáte tok dat do aktivity kanálu. Ladění kanálu pro ucelený test.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>Zlepšení formátu CSV/CDM v toku dat 

Pokud použijete **formátování s oddělovači textu nebo CDM pro mapování toku dat ve službě Azure Data Factory v2**, může dojít ke změnám chování stávajících kanálů z důvodu vylepšení oddělovače text/CDM v toku dat, který začíná **1. května 2021**. 

Před vylepšením může dojít k následujícím problémům, ale po vylepšení byly problémy vyřešeny. Přečtěte si následující obsah, abyste zjistili, jestli vám toto vylepšení mělo vliv. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>Scénář 1: narazí na neočekávaný problém s oddělovačem řádků

 Máte vliv na tyto podmínky:
 - Použití textu s oddělovači s nastavením víceřádkového nastavení nastaveným na hodnotu true nebo CDM jako zdroj.
 - První řádek obsahuje více než 128 znaků. 
 - Oddělovač řádků v datových souborech není `\n` .

 Před zlepšením může být výchozí oddělovač řádků `\n` neočekávaně použit k analýze textových souborů s oddělovači, protože pokud je nastavení víceřádkového nastavení nastaveno na hodnotu true, zruší platnost nastavení oddělovače řádků a oddělovač řádků se automaticky detekuje na základě prvních 128 znaků. Pokud se vám nedaří zjistit skutečný oddělovač řádků, vrátí se do `\n` .  

 Po vylepšení by se měly provádět všechny tři oddělovače řádků: `\r` , `\n` , `\r\n`
 
 Následující příklad ukazuje jednu změnu chování kanálu po vylepšení:

 **Příklad**:<br/>
   Pro následující sloupec:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   Před vylepšením `\r` se udržuje v hodnotě sloupce. Výsledek analyzovaného sloupce je:<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   Po zlepšení by měl výsledek analyzovaného sloupce:<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>Scénář 2: narazí na problém nesprávně čtených hodnot sloupců obsahujících "\r\n"

 Máte vliv na tyto podmínky:
 - Použití textu s oddělovači s nastavením víceřádkové hodnoty nastavenou na hodnotu true nebo CDM jako zdroj. 
 - Oddělovač řádků je `\r\n` .

 Před vylepšením při čtení hodnoty sloupce `\r\n` může být v něm nesprávně nahrazeno `\n` . 

 Po vylepšení nebude `\r\n` hodnota ve sloupci nahrazena hodnotou `\n` .

 Následující příklad ukazuje jednu změnu chování kanálu po vylepšení:
 
 **Příklad**:<br/>
  
 Pro následující sloupec:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 Před vylepšení je výsledkem analyzovaného sloupce:<br/>
  **`A\n`**` B C`<br/>

 Po zlepšení by měl výsledek analyzovaného sloupce:<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>Scénář 3: narazí na problém nesprávně zapsaných hodnot sloupce obsahující ' \n '

 Máte vliv na tyto podmínky:
 - Použití textu s oddělovači jako jímky.
 - Hodnota sloupce obsahuje `\n` .
 - Oddělovač řádků je nastaven na hodnotu `\r\n` .
 
 Před vylepšením při psaní hodnoty sloupce `\n` může být v něm nesprávně nahrazeno `\r\n` . 

 Po vylepšení nebude `\n` hodnota ve sloupci nahrazena hodnotou `\r\n` .
 
 Následující příklad ukazuje jednu změnu chování kanálu po vylepšení:

 **Příklad**:<br/>

 Pro následující sloupec:<br/>
 **`A\n`**` B C`<br/>

 Před vylepšením je jímka sdíleného svazku clusteru:<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 Po zlepšení by měla jímka sdíleného svazku clusteru:<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>Scénář 4: narazí na problém s nesprávně čitelným prázdným řetězcem jako s hodnotou NULL
 
 Máte vliv na tyto podmínky:
 - Použití textu s oddělovači jako zdroje. 
 - Hodnota NULL je nastavena na hodnotu, která není prázdná. 
 - Hodnota sloupce je prázdný řetězec a není v uvozovkách. 
 
 Před vylepšením je hodnota sloupce prázdného řetězce bez uvozovek přečtena jako hodnota NULL. 

 Po zlepšení se prázdný řetězec neanalyzuje jako hodnota NULL. 
 
 Následující příklad ukazuje jednu změnu chování kanálu po vylepšení:

 **Příklad**:<br/>

 Pro následující sloupec:<br/>
  `A, ,B, `<br/>

 Před vylepšení je výsledkem analyzovaného sloupce:<br/>
  `A null B null`<br/>

 Po zlepšení by měl výsledek analyzovaného sloupce:<br/>
  `A "" (empty string) B "" (empty string)`<br/>


## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v těchto materiálech:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
