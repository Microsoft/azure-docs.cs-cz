---
title: Řešení potíží s daty mapování toků
description: Naučte se řešit problémy toku dat v Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: f8a852a8c4197169061a9c7633f4f363ad057337
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505796"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Řešení potíží s mapováním toků dat v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje běžné metody řešení potíží pro mapování toků dat v Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Běžné kódy chyb a zprávy 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kód chyby: DF-exekutor-SourceInvalidPayload
- **Zpráva**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.
- **Příčina**: datová sada obsahuje kontejner, který neexistuje v úložišti.
- **Doporučení**: Ujistěte se, že kontejner odkazovaný ve vaší datové sadě existuje a je k němu k dispozici.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-exekutor-SystemImplicitCartesian

- **Zpráva**: implicitní kartézském produkt pro vnitřní spojení není podporován, místo toho použijte vzájemné spojení. Sloupce používané ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčina**: implicitní kartézském produkty pro vnitřní spojení mezi logickými plány nejsou podporovány. Pokud ve spojení používáte sloupce, vytvořte jedinečný klíč s alespoň jedním sloupcem na obou stranách relace.
- **Doporučení**: u spojení, která nejsou založená na rovnosti, použijte vlastní křížové spojení.

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

### <a name="error-code-df-executor-columnunavailable"></a>Kód chyby: DF-exekutor-ColumnUnavailable
- **Zpráva**: název sloupce použitý ve výrazu není k dispozici nebo je neplatný.
- **Příčina**: ve výrazu je použit neplatný nebo nedostupný název sloupce.
- **Doporučení**: ve výrazech ověřte názvy sloupců.

### <a name="error-code-df-executor-parseerror"></a>Kód chyby: DF-exekutor-ParseError
- **Zpráva**: výraz nejde analyzovat.
- **Příčina**: výraz vygeneroval chyby při analýze z důvodu nesprávného formátování.
- **Doporučení**: ve výrazu ověřte formátování.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-exekutor-SystemImplicitCartesian
- **Zpráva**: implicitní kartézském produkt pro vnitřní spojení není podporován, místo toho použijte vzájemné spojení. Sloupce používané ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčina**: implicitní kartézském produkty pro vnitřní spojení mezi logickými plány nejsou podporovány. Pokud ve spojení používáte sloupce, vytvořte jedinečný klíč.
- **Doporučení**: u spojení, která nejsou založená na rovnosti, použijte vzájemné spojení.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kód chyby: DF-exekutor-SystemInvalidJson
- **Zpráva**: Chyba analýzy JSON, nepodporované kódování nebo víceřádkové
- **Příčina**: možné problémy se souborem JSON: nepodporované kódování, poškozené bajty nebo použití zdroje JSON jako jednoho dokumentu na mnoha vnořených řádcích.
- **Doporučení**: Ověřte, zda je kódování souboru JSON podporováno. Ve zdrojové transformaci, která používá datovou sadu JSON, rozbalte **Nastavení JSON** a zapněte **jeden dokument**.



### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-prováděcí-převod
- **Zpráva**: převod na datum nebo čas se nezdařil z důvodu neplatného znaku.
- **Příčina**: data nejsou v očekávaném formátu.
- **Doporučení**: Použijte správný datový typ.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kód chyby: DF-exekutor-BlockCountExceedsLimitError
- **Zpráva**: počet nepotvrzených bloků nemůže překročit maximální limit 100 000 bloků. Ověřte konfiguraci objektu BLOB.
- **Příčina**: maximální počet nepotvrzených bloků v objektu blob je 100 000.
- **Doporučení**: Další informace o tomto problému získáte od Microsoft Product Team.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kód chyby: DF-exekutor-PartitionDirectoryError
- **Zpráva**: Zadaná cesta ke zdroji má buď více adresářů s oddíly (například *<Source Path> /<kořenový adresář 1>/a = 10/b = 20, <Source Path> /<kořenového adresáře oddílu 2>/c = 10/d = 30*) nebo adresář s děleným jiným souborem nebo nerozděleným adresářem (například *<Source Path> kořenový adresář oddílu 1 <oddíl:/a = 10/b = 20,/Directory <Source Path> 2/Soubor1)*, odeberte kořenový adresář oddílu ze zdrojové cesty a přečtěte>ho pomocí samostatné transformace zdroje.
- **Příčina**: zdrojová cesta má buď více oddílů rozdělených na oddíly, nebo oddílů s oddíly, které mají jiný soubor nebo adresář bez oddílů. 
- **Doporučení**: Odeberte kořenový adresář oddílů ze zdrojové cesty a přečtěte ho pomocí samostatné transformace zdroje.

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

### <a name="error-code-df-executor-invalidtype"></a>Kód chyby: DF-exekutor-InvalidType
- **Zpráva**: Ujistěte se prosím, že typ parametrů odpovídá typu předané hodnoty. Předávání parametrů float z kanálů není aktuálně podporováno.
- **Příčina**: datový typ pro deklarovaný typ není kompatibilní se skutečnou hodnotou parametru. 
- **Doporučení**: zadejte správné datové typy.

### <a name="error-code-df-executor-columnunavailable"></a>Kód chyby: DF-exekutor-ColumnUnavailable
- **Zpráva**: název sloupce použitý ve výrazu není k dispozici nebo je neplatný.
- **Příčina**: ve výrazu je použit neplatný nebo nedostupný název sloupce.
- **Doporučení**: Ověřte názvy sloupců používané ve výrazech.


### <a name="error-code-df-executor-parseerror"></a>Kód chyby: DF-exekutor-ParseError
- **Zpráva**: výraz nelze analyzovat.
- **Příčina**: výraz vygeneroval chyby při analýze z důvodu nesprávného formátování. 
- **Doporučení**: ve výrazu ověřte formátování.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Kód chyby: DF-exekutor-OutOfDiskSpaceError
- **Zpráva**: interní chyba serveru
- **Příčina**: v clusteru dochází místo na disku.
- **Doporučení**: zkuste kanál zopakovat. Pokud se tak problém nevyřeší, obraťte se na zákaznickou podporu.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Kód chyby: DF-exekutor-StoreIsNotDefined
- **Zpráva**: konfigurace úložiště není definována. Tato chyba je potenciálně způsobena neplatným přiřazením parametru v kanálu.
- **Příčina**: Neurčeno.
- **Doporučení**: Ověřte přiřazení hodnoty parametru v kanálu. Výraz parametru může obsahovat neplatné znaky.

### <a name="error-code-df-excel-invalidconfiguration"></a>Kód chyby: DF-Excel-InvalidConfiguration
- **Zpráva**: vyžaduje se název nebo index excelového listu.
- **Příčina**: Neurčeno.
- **Doporučení**: ověřte hodnotu parametru. Zadejte název nebo index listu pro čtení dat aplikace Excel.

- **Zpráva**: název a index listu aplikace Excel nemohou existovat současně.
- **Příčina**: Neurčeno.
- **Doporučení**: ověřte hodnotu parametru. Zadejte název nebo index listu pro čtení dat aplikace Excel.

- **Zpráva**: je zadaný neplatný rozsah.
- **Příčina**: Neurčeno.
- **Doporučení**: ověřte hodnotu parametru. Zadejte platný rozsah podle odkazu. Další informace najdete v tématu [vlastnosti Excelu](./format-excel.md#dataset-properties).

- **Zpráva**: je zadaný neplatný excelový soubor, ale podporují se jenom soubory. xlsx a. xls.
- **Příčina**: Neurčeno.
- **Doporučení**: Ujistěte se, že je přípona souboru aplikace Excel buď. xlsx nebo. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Kód chyby: DF-Excel-InvalidData
- **Zpráva**: excelový list neexistuje.
- **Příčina**: Neurčeno.
- **Doporučení**: ověřte hodnotu parametru. Zadejte platný název nebo index listu pro čtení dat aplikace Excel.

- **Zpráva**: čtení excelových souborů s jiným schématem není nyní podporováno.
- **Příčina**: Neurčeno.
- **Doporučení**: použijte podporovaný excelový soubor.

- **Zpráva**: datový typ není podporován.
- **Příčina**: Neurčeno.
- **Doporučení**: použijte podporované datové typy excelového souboru.

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

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v těchto materiálech:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)

