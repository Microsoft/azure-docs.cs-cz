---
title: Poradce při potížích s datovými toky
description: Zjistěte, jak řešit problémy s tokem dat v Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.openlocfilehash: a5244086c185c111762496086f8044f12f52be14
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632591"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Poradce při potížích s toky dat v Azure Data Factory

Tento článek zkoumá běžné metody řešení potíží pro toky dat v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kód chyby: DF-Executor-SourceInvalidPayload
- **Zpráva**: Spuštění toku dat náhledu dat, ladění a kanálu dat se nezdařilo, protože kontejner neexistuje.
- **Příčiny**: Pokud datová sada obsahuje kontejner, který neexistuje v úložišti
- **Doporučení:** Ujistěte se, že kontejner odkazovaný ve vaší datové sadě existuje nebo je přístupný.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kód chyby: DF-Executor-SystemImplicitCartesian

- **Zpráva**: Implicitní kartézský produkt pro vnitřní spojení není podporován, použijte cross join místo. Sloupce použité ve spojení by měly vytvořit jedinečný klíč pro řádky.
- **Příčiny**: Implicitní kartézský produkt pro vnitřní spojení mezi logickými plány není podporován. Pokud sloupce použité ve spojení vytvoří jedinečný klíč, je vyžadován alespoň jeden sloupec z obou stran relace.
- **Doporučení:** Pro nerovné spojení se musíte rozhodnout pro CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kód chyby: DF-Executor-SystemInvalidJson

- **Zpráva**: Chyba analýzy JSON, nepodporované kódování nebo víceřádkové
- **Příčiny**: Možné problémy se souborem JSON: nepodporované kódování, poškození bajtů nebo použití zdroje JSON jako jediného dokumentu na mnoha vnořených řádcích
- **Doporučení:** Ověřte, zda je kódování souboru JSON podporováno. Na source transformace, která používá datovou sadu JSON rozbalte 'JSON Nastavení' a zapněte 'Jeden dokument'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kód chyby: DF-Executor-BroadcastTimeout

- **Zpráva**: Chyba časového doby spojení vysílání, ujistěte se, že datový proud vysílání vytváří data v rámci 60 sekund při spuštění ladění a 300 sekund v spuštění úlohy
- **Příčiny**: Vysílání má výchozí časový limit 60 sekund v ladění spustí a 300 sekund v spuštění úlohy. Zdá se, že datový proud vybraný pro vysílání je velký, aby vytvořil data v rámci tohoto limitu.
- **Doporučení:** Vyhněte se vysílání velkých datových proudů, kde zpracování může trvat více než 60 sekund. Místo toho zvolte menší datový proud, který chcete vysílat. Velké tabulky SQL/DW a zdrojové soubory jsou obvykle chybné kandidáty.

### <a name="error-code-df-executor-conversion"></a>Kód chyby: DF-Executor-Conversion

- **Zpráva**: Převod na datum nebo čas se nezdařil z důvodu neplatného znaku
- **Příčiny**: Data nejsou v očekávaném formátu.
- **Doporučení:** Použití správného datového typu

### <a name="error-code-df-executor-invalidcolumn"></a>Kód chyby: DF-Executor-InvalidColumn

- **Zpráva**: Název sloupce musí být zadán v dotazu, nastavit alias, pokud používáte funkci SQL
- **Příčiny:** Nebyl zadán žádný název sloupce.
- **Doporučení:** Nastavte alias, pokud používáte funkci SQL, jako je min()/max(), atd.

### <a name="error-code-getcommand-outputasync-failed"></a>Kód chyby: Příkaz GetCommand OutputAsync se nezdařil.

- **Zpráva**: Během ladění toku dat a náhledu dat: GetCommand OutputAsync se nezdařilo s ...
- **Příčiny**: Jedná se o chybu služby back-end. Můžete opakovat operaci a také restartovat relaci ladění.
- **Doporučení:** Pokud opakování a restartování problém nevyřeší, obraťte se na zákaznickou podporu.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Kód chyby: Přístup neočekávaná výjimka a spuštění se nezdařilo.

- **Zpráva**: Během provádění aktivity toku dat: Přístup k neočekávané výjimce a spuštění se nezdařilo.
- **Příčiny**: Jedná se o chybu služby back-end. Můžete opakovat operaci a také restartovat relaci ladění.
- **Doporučení:** Pokud opakování a restartování problém nevyřeší, obraťte se na zákaznickou podporu.

## <a name="general-troubleshooting-guidance"></a>Obecné pokyny pro řešení potíží

1. Zkontrolujte stav připojení datové sady. V každé transformaci zdroj a jímky navštivte propojené služby pro každou datovou sadu, kterou používáte a testování připojení.
1. Zkontrolujte stav připojení souborů a tabulek z návrháře toku dat. Zapněte ladění a klikněte na náhled dat na zdrojtransformace, aby zajistily, že budete mít přístup k datům.
1. Pokud vše vypadá dobře z náhledu dat, přejděte do návrháře kanálu a umístit tok dat do aktivity kanálu. Ladění kanálu pro test konce na konec.

## <a name="next-steps"></a>Další kroky

Další nápovědu k řešení potíží naleznete v těchto zdrojích:
*  [Blog data factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Požadavky na funkce datové továrny](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Fórum přetečení zásobníku pro datovou továrnu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informace o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce výkonem toků mapových dat adf](concepts-data-flow-performance.md)
