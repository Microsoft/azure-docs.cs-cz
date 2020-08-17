---
title: Řešení potíží s toky dat
description: Naučte se řešit problémy toku dat v Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/16/2020
ms.openlocfilehash: 0a691b562ebf030712eb0c13a688ea9a52fdb164
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263465"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Řešení potíží s toky dat v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje běžné metody řešení potíží pro toky dat v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kód chyby: DF-exekutor-SourceInvalidPayload
- **Zpráva**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.
- **Příčiny**: když datová sada obsahuje kontejner, který v úložišti neexistuje.
- **Doporučení**: Ujistěte se, že kontejner odkazovaný ve vaší datové sadě existuje nebo je přístupný.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-exekutor-SystemImplicitCartesian

- **Zpráva**: implicitní kartézském produkt pro vnitřní spojení není podporován, místo toho použijte vzájemné spojení. Sloupce používané ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčiny**: implicitní kartézském produkt pro vnitřní spojení mezi logickými plány není podporován. Pokud sloupce používané ve spojení vytvoří jedinečný klíč, je nutné aspoň jeden sloupec z obou stran relace.
- **Doporučení**: u spojení, která nejsou založená na rovnosti, musíte vyjádřit souhlas s vlastním VZÁJEMNÝm spojením.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kód chyby: DF-exekutor-SystemInvalidJson

- **Zpráva**: Chyba analýzy JSON, nepodporované kódování nebo víceřádkové
- **Příčiny**: možné problémy se souborem JSON: nepodporované kódování, poškozené bajty nebo použití zdroje JSON jako jednoho dokumentu na mnoha vnořených řádcích
- **Doporučení**: Ověřte, že se podporuje kódování souboru JSON. Ve zdrojové transformaci, která používá datovou sadu JSON, rozbalte ' nastavení JSON ' a zapněte ' jednotlivý dokument '.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kód chyby: DF-exekutor-BroadcastTimeout

- **Zpráva**: Chyba vypršení časového limitu spojení všesměrového vysílání, ujistěte se, že Stream všesměrového vysílání generuje data během 60 sekund v ladění běhu a 300 s ve spuštění úlohy
- **Příčiny**: všesměrové vysílání má výchozí časový limit 60 sekund v ladicích běhůch a 300 sekund ve spuštění úlohy. Datový proud, který se vybral pro vysílání, se jeví jako velký a v rámci tohoto omezení vytváří data.
- **Doporučení**: Podívejte se na kartu optimalizace v části transformace toku dat pro spojení, EXISTS a vyhledávání. Výchozí možnost pro vysílání je "auto". Pokud je tato možnost nastavená nebo pokud ručně nastavujete levou nebo pravou stranu na všesměrové vysílání v části "pevná", můžete buď nastavit větší konfiguraci Azure Integration Runtime, nebo vypnout všesměrové vysílání. Doporučený postup pro nejlepší výkon v datových tocích je, aby Spark mohl vysílat pomocí příkazu "auto" a používat paměťově optimalizovanou Azure IR.

### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-prováděcí-převod

- **Zpráva**: převod na datum nebo čas se nezdařil z důvodu neplatného znaku.
- **Příčiny**: data nejsou v očekávaném formátu.
- **Doporučení**: Použijte správný datový typ

### <a name="error-code-df-executor-invalidcolumn"></a>Kód chyby: DF-exekutor-InvalidColumn

- **Zpráva**: v dotazu je nutné zadat název sloupce, nastavit alias, pokud se používá funkce SQL.
- **Příčiny**: nebyl zadán žádný název sloupce.
- **Doporučení**: Pokud používáte funkci SQL, jako je například min ()/Max (), nastavte alias ().

### <a name="error-code-getcommand-outputasync-failed"></a>Kód chyby: GetCommand OutputAsync se nezdařilo.

- **Zpráva**: během ladění a náhledu dat toku dat došlo k chybě příkazu GetCommand OutputAsync...
- **Příčiny**: Jedná se o chybu back-endové služby. Můžete to zkusit znovu a taky znovu spustit relaci ladění.
- **Doporučení**: Pokud akci opakovat a restartovat neodstraníte, obraťte se na zákaznickou podporu.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Kód chyby: došlo k neočekávané výjimce a spuštění selhalo.

- **Zpráva**: během provádění aktivity toku dat došlo k neočekávané výjimce a spuštění se nezdařilo.
- **Příčiny**: Jedná se o chybu back-endové služby. Můžete to zkusit znovu a taky znovu spustit relaci ladění.
- **Doporučení**: Pokud akci opakovat a restartovat neodstraníte, obraťte se na zákaznickou podporu.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Kód chyby: náhled dat ladění při spojení neobsahují výstupní data.

- **Zpráva**: existuje velký počet hodnot null nebo chybějící hodnoty, které mohou být způsobeny příliš malým počtem řádků vzorků. Zkuste aktualizovat limit řádků ladění a aktualizovat data.
- **Příčiny**: podmínka spojení se neshoduje s žádnými řádky nebo způsobila vysoký počet hodnot null během období náhledu dat.
- **Doporučení**: Přejít na nastavení ladění a zvýšit počet řádků v limitu zdrojového řádku. Ujistěte se, že máte vybrat a Azure IR s velkým dostatečným clusterem toku dat pro zpracování více dat.


## <a name="general-troubleshooting-guidance"></a>Obecné pokyny k odstraňování potíží

1. Ověřte stav připojení datové sady. V každé transformaci zdroje a jímky navštivte propojenou službu pro každou datovou sadu, kterou používáte, a otestujte připojení.
1. Ověřte stav připojení k souborům a tabulkám z návrháře toku dat. Přepněte na ladění a klikněte na náhled dat ve vašich zdrojových transformacích, abyste měli jistotu, že budete mít přístup k datům.
1. Pokud vše vypadá z verze Preview, přejdete do návrháře kanálů a zadáte tok dat do aktivity kanálu. Ladění kanálu pro ucelený test.

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:
*  [Blog Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce výkonem datových toků mapování ADF](concepts-data-flow-performance.md)
