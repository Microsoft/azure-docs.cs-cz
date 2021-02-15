---
title: Řešení potíží s daty mapování toků
description: Naučte se řešit problémy toku dat v Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: a95cacafc5b1d00b1e4d04fd84cdda2de72b6a59
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523001"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Řešení potíží s mapováním toků dat v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje běžné metody řešení potíží pro mapování toků dat v Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Běžné kódy chyb a zprávy 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kód chyby: DF-exekutor-SourceInvalidPayload
- **Zpráva**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.
- **Příčiny**: když datová sada obsahuje kontejner, který v úložišti neexistuje.
- **Doporučení**: Ujistěte se, že kontejner odkazovaný ve vaší datové sadě existuje nebo je přístupný.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-exekutor-SystemImplicitCartesian

- **Zpráva**: implicitní kartézském produkt pro vnitřní spojení není podporován, místo toho použijte vzájemné spojení. Sloupce používané ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčiny**: implicitní kartézském produkt pro vnitřní spojení mezi logickými plány není podporován. Pokud jsou ve spojení použity sloupce, je vyžadován jedinečný klíč s alespoň jedním sloupcem na obou stranách relace.
- **Doporučení**: u spojení, která nejsou založená na rovnosti, musíte vyjádřit souhlas s vlastním VZÁJEMNÝm spojením.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kód chyby: DF-exekutor-SystemInvalidJson

- **Zpráva**: Chyba analýzy JSON, nepodporované kódování nebo víceřádkové
- **Příčiny**: možné problémy se souborem JSON: nepodporované kódování, poškozené bajty nebo použití zdroje JSON jako jednoho dokumentu na mnoha vnořených řádcích
- **Doporučení**: Ověřte, že se podporuje kódování souboru JSON. Ve zdrojové transformaci, která používá datovou sadu JSON, rozbalte ' nastavení JSON ' a zapněte ' jednotlivý dokument '.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kód chyby: DF-exekutor-BroadcastTimeout

- **Zpráva**: Chyba vypršení časového limitu spojení všesměrového vysílání, ujistěte se, že Stream všesměrového vysílání generuje data během 60 sekund v ladění běhu a 300 s ve spuštění úlohy
- **Příčiny**: všesměrové vysílání má výchozí časový limit 60 sekund v ladicích běhů a 300 sekund ve spuštění úlohy. Stream vybraný pro vysílání je moc velký pro vytváření dat v rámci tohoto limitu.
- **Doporučení**: Podívejte se na kartu optimalizace v části transformace toku dat pro spojení, EXISTS a vyhledávání. Výchozí možnost pro vysílání je "auto". Pokud je nastavená možnost automaticky, nebo pokud ručně nastavujete levou nebo pravou stranu na všesměrové vysílání v části "pevná", můžete buď nastavit větší konfiguraci Azure Integration Runtime, nebo vypnout všesměrové vysílání. Doporučený postup pro nejlepší výkon v datových tocích je, aby Spark mohl vysílat pomocí příkazu "auto" a používat paměťově optimalizovanou Azure IR. Spouštíte-li tok dat při spuštění testu ladění z běhu ladicího kanálu, můžete se do této podmínky spouštět častěji. Důvodem je to, že ADF omezuje časový limit všesměrového vysílání na 60 sekund, aby bylo možné udržet rychlejší ladicí prostředí. Pokud byste chtěli tento časový limit 300 sekund od aktivovaného spuštění napřed zadat, můžete použít možnost ladění > použít aktivitu použít k využití Azure IR definovaného v aktivitě kanálu vykonání toku dat.

- **Zpráva**: Chyba časového limitu připojení všesměrového vysílání. Chcete-li se tomuto problému vyhnout, můžete zvolit možnost Vypnuto u možnosti vysílání v nástroji JOIN/Exists nebo v transformaci vyhledávání. Pokud máte v úmyslu vysílat spojení s cílem zvýšit výkon, zajistěte, aby Stream všesměrového vysílání mohl vytvářet data během 60 sekund v ladicích běhůch a 300 sekund ve spuštění úlohy.
- **Příčiny**: všesměrové vysílání má výchozí časový limit 60 sekund v ladicích běhůch a 300 sekund ve spuštění úlohy. V případě připojení všesměrového vysílání je datový proud, který se vybral pro vysílání, moc velký, aby se data v rámci tohoto limitu Pokud se připojení všesměrového vysílání nepoužívá, může výchozí vysílání prováděné tokem dat dosáhnout stejného limitu.
- **Doporučení**: Vypněte možnost vysílání nebo Vyhněte se vysílání velkých datových proudů, kde zpracování může trvat více než 60 sekund. Místo toho vyberte menší datový proud, který se má vysílat. Rozsáhlé tabulky a zdrojové soubory SQL/DW jsou obvykle špatné kandidáty. Při nepřítomnosti připojení všesměrového vysílání použijte větší cluster, pokud dojde k chybě.

### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-prováděcí-převod

- **Zpráva**: převod na datum nebo čas se nezdařil z důvodu neplatného znaku.
- **Příčiny**: data nejsou v očekávaném formátu.
- **Doporučení**: Použijte správný datový typ

### <a name="error-code-df-executor-invalidcolumn"></a>Kód chyby: DF-exekutor-InvalidColumn
- **Zpráva**: v dotazu je nutné zadat název sloupce, nastavit alias, pokud se používá funkce SQL.
- **Příčiny**: nebyl zadán žádný název sloupce.
- **Doporučení**: Pokud používáte funkci SQL, jako je například min ()/Max (), nastavte alias ().

### <a name="error-code-df-executor-drivererror"></a>Kód chyby: DF-exekutor-DriverError
- **Zpráva**: INT96 je starší typ časového razítka, který není podporován TOKEM dat ADF. Zvažte prosím možnost upgradovat typ sloupce na nejnovější typy.
- **Příčiny**: Chyba ovladače
- **Doporučení**: INT96 je starší typ časového razítka, který není podporován TOKEM dat ADF. Zvažte možnost upgradovat typ sloupce na nejnovější typy.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kód chyby: DF-exekutor-BlockCountExceedsLimitError
- **Zpráva**: počet nepotvrzených bloků nemůže překročit maximální limit 100 000 bloků. Ověřte konfiguraci objektu BLOB.
- **Příčiny**: v objektu BLOB může být maximálně 100 000 nepotvrzených bloků.
- **Doporučení**: Další podrobnosti vám poskytne tým produktů Microsoftu týkající se tohoto problému.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kód chyby: DF-exekutor-PartitionDirectoryError
- **Zpráva**: Zadaná cesta ke zdroji má buď více adresářů s oddíly (například <Source Path> /<kořenový adresář 1>/a = 10/b = 20, <Source Path> /<kořenového adresáře oddílu 2>/c = 10/d = 30) nebo adresář s oddíly s jiným souborem nebo nerozděleným adresářem (například <Source Path> /<kořenový adresář 1>/a = 10/b = 20, <Source Path> /Directory 2/Soubor1), odeberte kořenový adresář oddílu ze zdrojové cesty a přečtěte ho pomocí samostatné transformace zdroje.
- **Příčiny**: zdrojová cesta má buď více oddílů v děleném oddílu, nebo oddíl s oddíly s jiným souborem nebo adresářem bez oddílů.
- **Doporučení**: Odeberte kořenový adresář rozdělený na oddíly ze zdrojové cesty a přečtěte ho pomocí samostatné transformace zdroje.

### <a name="error-code-df-executor-outofmemoryerror"></a>Kód chyby: DF-exekutor-OutOfMemoryError
- **Zpráva**: během provádění došlo k chybě clusteru z důvodu nedostatku paměti, zkuste to prosím znovu s prostředím Integration runtime s větším počtem jader nebo s paměťově optimalizovaným výpočetním typem.
- **Příčiny**: v clusteru dochází paměť
- **Doporučení**: clustery ladění jsou určeny pro účely vývoje. Využijte vzorkování dat, vhodný výpočetní typ a velikost pro spuštění datové části. Chcete-li dosáhnout nejlepšího výkonu, přečtěte si [Průvodce výkonem toku dat mapování](concepts-data-flow-performance.md) pro ladění.

### <a name="error-code-df-executor-invalidtype"></a>Kód chyby: DF-exekutor-InvalidType
- **Zpráva**: Ujistěte se prosím, že typ parametrů odpovídá typu předané hodnoty. Předávání parametrů float z kanálů není aktuálně podporováno.
- **Příčiny**: nekompatibilní datové typy mezi deklarovaným typem a skutečnou hodnotou parametru
- **Doporučení**: Ověřte, že hodnoty parametrů předané do toku dat odpovídají deklarovanému typu.

### <a name="error-code-df-executor-columnunavailable"></a>Kód chyby: DF-exekutor-ColumnUnavailable
- **Zpráva**: název sloupce použitý ve výrazu není k dispozici nebo je neplatný.
- **Příčiny**: neplatný nebo nedostupný název sloupce použitý ve výrazech
- **Doporučení**: Zkontrolujte názvy sloupců, které se používají ve výrazech.

### <a name="error-code-df-executor-parseerror"></a>Kód chyby: DF-exekutor-ParseError
- **Zpráva**: výraz nejde analyzovat.
- **Příčiny**: výraz obsahuje chyby při analýze z důvodu formátování
- **Doporučení**: Podívejte se prosím na formátování ve výrazu.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-exekutor-SystemImplicitCartesian
- **Zpráva**: implicitní kartézském produkt pro vnitřní spojení není podporován, místo toho použijte vzájemné spojení. Sloupce používané ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčiny**: implicitní kartézském produkt pro vnitřní spojení mezi logickými plány není podporován. Pokud sloupce používané ve spojení vytvoří jedinečný klíč
- **Doporučení**: u spojení, která nejsou založená na rovnosti, je nutné se rozhodnout pro vzájemné spojení.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kód chyby: DF-exekutor-SystemInvalidJson
- **Zpráva**: Chyba analýzy JSON, nepodporované kódování nebo víceřádkové
- **Příčiny**: možné problémy se souborem JSON: nepodporované kódování, poškozené bajty nebo použití zdroje JSON jako jednoho dokumentu na mnoha vnořených řádcích
- **Doporučení**: Ověřte, že se podporuje kódování souboru JSON. Ve zdrojové transformaci, která používá datovou sadu JSON, rozbalte ' nastavení JSON ' a zapněte ' jednotlivý dokument '.



### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-prováděcí-převod
- **Zpráva**: převod na datum nebo čas se nezdařil z důvodu neplatného znaku.
- **Příčiny**: data nejsou v očekávaném formátu.
- **Doporučení**: použijte prosím správný datový typ.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kód chyby: DF-exekutor-BlockCountExceedsLimitError
- **Zpráva**: počet nepotvrzených bloků nemůže překročit maximální limit 100 000 bloků. Ověřte konfiguraci objektu BLOB.
- **Příčiny**: v objektu BLOB může být maximálně 100 000 nepotvrzených bloků.
- **Doporučení**: Další podrobnosti získáte od oddělení produktů Microsoftu týkajících se tohoto problému.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kód chyby: DF-exekutor-PartitionDirectoryError
- **Zpráva**: Zadaná cesta ke zdroji má buď více adresářů s oddíly (například *<Source Path> /<kořenový adresář 1>/a = 10/b = 20, <Source Path> /<kořenového adresáře oddílu 2>/c = 10/d = 30*) nebo adresář s děleným jiným souborem nebo nerozděleným adresářem (například *<Source Path> kořenový adresář oddílu 1 <oddíl:/a = 10/b = 20,/Directory <Source Path> 2/Soubor1)*, odeberte kořenový adresář oddílu ze zdrojové cesty a přečtěte>ho pomocí samostatné transformace zdroje.
- **Příčiny**: zdrojová cesta má buď více oddílů v děleném oddílu, nebo oddíl s oddíly s jiným souborem nebo adresářem bez oddílů.
- **Doporučení**: Odeberte kořenový adresář rozdělený na oddíly ze zdrojové cesty a přečtěte ho pomocí samostatné transformace zdroje.

### <a name="error-code-getcommand-outputasync-failed"></a>Kód chyby: GetCommand OutputAsync se nezdařilo.
- **Zpráva**: během ladění a náhledu dat toku dat došlo k chybě příkazu GetCommand OutputAsync...
- **Příčiny**: Jedná se o chybu back-endové služby. Můžete to zkusit znovu a taky znovu spustit relaci ladění.
- **Doporučení**: Pokud akci opakovat a restartovat neodstraníte, obraťte se na zákaznickou podporu. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Kód chyby: DF-exekutor-OutOfMemoryError
 
- **Zpráva**: během provádění došlo k chybě clusteru z důvodu nedostatku paměti, zkuste to prosím znovu s prostředím Integration runtime s větším počtem jader nebo s paměťově optimalizovaným výpočetním typem.
- **Příčiny**: cluster nemá dostatek paměti.
- **Doporučení**: clustery ladění jsou určeny pro účely vývoje. Využijte ke spuštění datové části vhodný výpočetní typ a velikost vzorkování dat. Chcete-li vyladit tok dat pro nejlepší výkon, přečtěte si [Průvodce výkonem toku](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) dat.

### <a name="error-code-df-executor-illegalargument"></a>Kód chyby: DF-exekutor-illegalArgument
- **Zpráva**: Ujistěte se prosím, že přístupový klíč v propojené službě je správný.
- **Příčiny**: název účtu nebo přístupový klíč nejsou správné.
- **Doporučení**: Zadejte prosím správný název účtu nebo přístupový klíč.

- **Zpráva**: Ujistěte se prosím, že přístupový klíč v propojené službě je správný.
- **Příčiny**: nesprávný název účtu nebo přístupový klíč
- **Doporučení**: Ujistěte se, že název účtu nebo přístupový klíč zadaný v propojené službě jsou správné. 

### <a name="error-code-df-executor-invalidtype"></a>Kód chyby: DF-exekutor-InvalidType
- **Zpráva**: Ujistěte se prosím, že typ parametrů odpovídá typu předané hodnoty. Předávání parametrů float z kanálů není aktuálně podporováno.
- **Příčiny**: nekompatibilní datové typy mezi deklarovaným typem a skutečnou hodnotou parametru
- **Doporučení**: Poskytněte správné datové typy.

### <a name="error-code-df-executor-columnunavailable"></a>Kód chyby: DF-exekutor-ColumnUnavailable
- **Zpráva**: název sloupce použitý ve výrazu není k dispozici nebo je neplatný.
- **Příčiny**: ve výrazech se používá neplatný nebo nedostupný název sloupce.
- **Doporučení**: Zkontrolujte názvy sloupců, které se používají ve výrazech.


### <a name="error-code-df-executor-parseerror"></a>Kód chyby: DF-exekutor-ParseError
- **Zpráva**: výraz nelze analyzovat.
- **Příčina**: výraz obsahuje chyby analýzy z důvodu formátování.
- **Doporučení**: Podívejte se prosím na formátování ve výrazu.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Kód chyby: DF-exekutor-OutOfDiskSpaceError
- **Zpráva**: interní chyba serveru
- **Příčiny**: v clusteru dochází místo na disku.
- **Doporučení**: Zkuste prosím kanál znovu. Pokud potíže potrvají, obraťte se na zákaznickou podporu.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Kód chyby: DF-exekutor-StoreIsNotDefined
- **Zpráva**: konfigurace úložiště není definována. Tato chyba je potenciálně způsobena neplatným přiřazením parametru v kanálu.
- **Příčiny**: Neurčeno
- **Doporučení**: Zkontrolujte prosím přiřazení hodnoty parametrů v kanálu. Výraz parametru může obsahovat neplatné znaky.

### <a name="error-code-df-excel-invalidconfiguration"></a>Kód chyby: DF-Excel-InvalidConfiguration
- **Zpráva**: vyžaduje se název nebo index excelového listu.
- **Příčiny**: Neurčeno
- **Doporučení**: Zkontrolujte hodnotu parametru a zadáním názvu nebo indexu listu načtěte data z Excelu.

- **Zpráva**: název a index listu aplikace Excel nemohou existovat současně.
- **Příčiny**: Neurčeno
- **Doporučení**: Zkontrolujte hodnotu parametru a zadáním názvu nebo indexu listu načtěte data z Excelu.

- **Zpráva**: je zadaný neplatný rozsah.
- **Příčiny**: Neurčeno
- **Doporučení**: Zkontrolujte prosím hodnotu parametru a zadejte platný rozsah podle odkazu: [vlastnosti Excelu](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).

- **Zpráva**: je zadaný neplatný excelový soubor, ale podporují se jenom soubory. xlsx a. xls.
- **Příčiny**: Neurčeno
- **Doporučení**: Ujistěte se, že je přípona souboru aplikace Excel buď. xlsx nebo. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Kód chyby: DF-Excel-InvalidData
- **Zpráva**: excelový list neexistuje.
- **Příčiny**: Neurčeno
- **Doporučení**: Zkontrolujte prosím hodnotu parametru a zadejte platný název nebo index listu pro čtení dat Excelu.

- **Zpráva**: čtení excelových souborů s jiným schématem není nyní podporováno.
- **Příčiny**: Neurčeno
- **Doporučení**: Použijte správný excelový soubor.

- **Zpráva**: datový typ není podporován.
- **Příčiny**: Neurčeno
- **Doporučení**: použijte prosím správné datové typy excelového souboru.

### <a name="error-code-4502"></a>Kód chyby: 4502
- **Zpráva**: existují značná souběžná spuštění MappingDataflow, která způsobují selhání kvůli omezení v Integration runtime.
- **Příčiny**: na Integration runtime souběžně probíhá spousta spuštění aktivity toku dat. Přečtěte si další informace o [omezeních Azure Data Factory](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#data-factory-limits).
- **Doporučení**: v případě, že chcete souběžně spustit více aktivit toku dat, distribuujte je prosím v několika prostředích Integration runtime.


### <a name="error-code-invalidtemplate"></a>Kód chyby: InvalidTemplate
- **Zpráva**: výraz kanálu nelze vyhodnotit.
- **Příčiny**: výraz kanálu předaný do aktivity toku dat není zpracován správně z důvodu chyby syntaxe.
- **Doporučení**: Zkontrolujte aktivitu aktivity v monitorování aktivit a ověřte výraz.

### <a name="error-code-2011"></a>Kód chyby: 2011
- **Zpráva**: aktivita běžela na Azure Integration runtime a nedokázala dešifrovat přihlašovací údaje úložiště dat nebo výpočetní prostředky připojené prostřednictvím Integration runtime v místním prostředí. Zkontrolujte prosím konfiguraci propojených služeb přidružených k této aktivitě a ujistěte se, že používáte správný typ prostředí Integration runtime.
- **Příčiny**: tok dat nepodporuje propojené služby s místním prostředím Integration runtime.
- **Doporučení**: Nakonfigurujte prosím tok dat, který se má spustit v modulu runtime integrace se spravovaným Virtual Network.

## <a name="miscellaneous-troubleshooting-tips"></a>Různé tipy pro řešení potíží
- **Problém**: došlo k neočekávané výjimce a spuštění selhalo.
    - **Zpráva**: během provádění aktivity toku dat došlo k neočekávané výjimce a spuštění se nezdařilo.
    - **Příčiny**: Jedná se o chybu back-endové služby. Můžete to zkusit znovu a taky znovu spustit relaci ladění.
    - **Doporučení**: Pokud akci opakovat a restartovat neodstraníte, obraťte se na zákaznickou podporu.

-  **Problém**: náhled dat ladění při spojení neobsahují výstupní data
    - **Zpráva**: existuje velký počet hodnot null nebo chybějící hodnoty, které mohou být způsobeny příliš malým počtem řádků vzorků. Zkuste aktualizovat limit řádků ladění a aktualizovat data.
    - **Příčiny**: podmínka spojení se neshoduje s žádnými řádky nebo způsobila vysoký počet hodnot null během období náhledu dat.
    - **Doporučení**: Přejít na nastavení ladění a zvýšit počet řádků v limitu zdrojového řádku. Ujistěte se, že jste vybrali Azure IR s velkým dostatečným clusterem toku dat pro zpracování více dat.
    
- **Problém**: Chyba ověřování ve zdroji pomocí víceřádkových souborů CSV 
    - **Zpráva**: může se zobrazit jedna z následujících chybových zpráv:
        - Poslední sloupec má hodnotu null nebo chybí.
        - Ověření schématu ve zdroji se nezdařilo.
        - Import schématu se nepovede správně zobrazit v uživatelském prostředí a poslední sloupec má v názvu znak nového řádku.
    - **Příčiny**: v toku dat mapování aktuálně nefunguje víceřádkový zdroj CSV s \r\n jako oddělovačem řádků. Někdy se nadbytečné řádky na řádku vrátí zdrojové hodnoty. 
    - **Doporučení**: buď vygenerujte soubor ve zdroji s \n jako oddělovač řádků místo \r\n. Nebo pomocí aktivity kopírování převeďte soubor CSV s \r\n na \n jako oddělovač řádků.

## <a name="general-troubleshooting-guidance"></a>Obecné pokyny k odstraňování potíží
1. Ověřte stav připojení datové sady. V každé transformaci zdroje a jímky navštivte propojenou službu pro každou datovou sadu, kterou používáte, a otestujte připojení.
2. Ověřte stav připojení k souborům a tabulkám z návrháře toku dat. Přepněte na ladění a klikněte na náhled dat ve vašich zdrojových transformacích, abyste měli jistotu, že budete mít přístup k datům.
3. Pokud vše vypadá z verze Preview, přejdete do návrháře kanálů a zadáte tok dat do aktivity kanálu. Ladění kanálu pro ucelený test.

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
