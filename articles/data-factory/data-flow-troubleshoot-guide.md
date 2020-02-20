---
title: Řešení potíží s toky dat
description: Naučte se řešit problémy toku dat v Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472124"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Řešení potíží s toky dat v Azure Data Factory

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
- **Doporučení**: Vyhněte se všesměrovému vysílání velkých datových proudů, kde zpracování může trvat více než 60 sekund. Místo toho vyberte menší datový proud, který se má vysílat. Rozsáhlé tabulky a zdrojové soubory SQL/DW jsou obvykle špatné kandidáty.

### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-prováděcí-převod

- **Zpráva**: převod na datum nebo čas se nezdařil z důvodu neplatného znaku.
- **Příčiny**: data nejsou v očekávaném formátu.
- **Doporučení**: Použijte správný datový typ

### <a name="error-code-df-executor-invalidcolumn"></a>Kód chyby: DF-exekutor-InvalidColumn

- **Zpráva**: v dotazu je nutné zadat název sloupce, nastavit alias, pokud se používá funkce SQL.
- **Příčiny**: nebyl zadán žádný název sloupce.
- **Doporučení**: Pokud používáte funkci SQL, jako je například min ()/Max (), nastavte alias ().

## <a name="general-troubleshooting-guidance"></a>Obecné pokyny k odstraňování potíží

1. Ověřte stav připojení datové sady. V každé transformaci zdroje a jímky navštivte propojenou službu pro každou datovou sadu, kterou používáte, a otestujte připojení.
1. Ověřte stav připojení k souborům a tabulkám z návrháře toku dat. Přepněte na ladění a klikněte na náhled dat ve vašich zdrojových transformacích, abyste měli jistotu, že budete mít přístup k datům.
1. Pokud vše vypadá z verze Preview, přejdete do návrháře kanálů a zadáte tok dat do aktivity kanálu. Ladění kanálu pro ucelený test.

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce výkonem datových toků mapování ADF](concepts-data-flow-performance.md)
