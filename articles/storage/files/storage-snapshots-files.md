---
title: Přehled snímků sdílených složek pro soubory Azure | Dokumentace Microsoftu
description: Snímek sdílené složky je jen pro čtení verze do sdílené složky Azure Files, která je provedena v bodě v čase, jako způsob, jak zálohovat sdílenou složku.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: b261ec5fb0ad437202df1a8fd8683a095cb1bb96
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42060532"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Přehled snímků sdílených složek pro soubory Azure 
Služba soubory Azure umožňuje využít snímky sdílené složky nebo sdílené složky. Sdílejte zachytávání snímků sdílené složky stavu v daném okamžiku v čase. V tomto článku popisujeme, jaké možnosti poskytuje snímky sdílené složky a jak můžete využít z nich ve vašem případě vlastní použití.

## <a name="when-to-use-share-snapshots"></a>Kdy použít snímky sdílené složky

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrana proti poškození chyba a data aplikací
Aplikace, které používají sdílené složky provádět operace, jako je například psaní, čtení, úložiště, předávání a zpracování. Pokud aplikace není správně nakonfigurovaný. nebo se používá neúmyslnému chyb, může dojít náhodnému přepsání nebo poškození na několik bloků. K ochraně proti těchto scénářů, může trvat snímek sdílené složky před nasazením nového kódu aplikace. Pokud chyba chybu nebo aplikací je součástí nového nasazení, můžete přejít zpět k předchozí verzi vašich dat v dané sdílené složce souborů. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrana proti náhodnému odstranění nebo nežádoucí změny
Představte si, že pracujete s textovým souborem ve sdílené složce. Po zavření textový soubor, ztratíte schopnost vrátit zpět provedené změny. V takových případech musíte pak obnovit předchozí verze souboru. Snímky sdílené složky můžete obnovit předchozí verze souboru, pokud se omylem přejmenovali nebo odstranili.

### <a name="general-backup-purposes"></a>Obecné účely zálohování
Po vytvoření sdílené složky můžete pravidelně vytvářet sdílené složky souboru určené k použití pro zálohování dat snímku sdílené složky. Sdílenou složku snímku, při pravidelně se usnadní zachování předchozí verze dat, které lze použít pro budoucí auditu požadavky nebo zotavení po havárii.

## <a name="capabilities"></a>Možnosti
Snímek sdílené složky je kopii dat bodu v čase, jen pro čtení. Můžete vytvářet, odstraňovat a spravovat snímky pomocí rozhraní REST API. Stejné možnosti jsou dostupné v klientské knihovny, rozhraní příkazového řádku Azure a webu Azure portal. 

Snímky sdílené složky můžete zobrazit pomocí protokolu SMB i rozhraní REST API. Můžete načíst seznam verzí adresáře nebo souboru a konkrétní verzi přímo jako jednotku můžete připojit. 

Po vytvoření snímku sdílené složky ho můžete číst, kopírovat, nebo odstranit, ale nedojde ke změně. Snímek celé sdílené složky nelze zkopírovat do jiného účtu úložiště. Budete muset provést to soubor po souboru, pomocí nástroje AzCopy nebo jiných mechanismů kopírování.

Snímků sdílené složky je k dispozici na úrovni sdílené složky souborů. Načítání je k dispozici na úrovni jednotlivých souborů umožňuje obnovení jednotlivých souborů. Kompletní sdílené složky můžete obnovit pomocí protokolu SMB, rozhraní REST API, na portálu, klientské knihovny nebo prostředí PowerShell nebo rozhraní příkazového řádku nástroje.

Snímek sdílené složky sdílené složky je stejný jako jeho základní sdílené složky. Jediným rozdílem je, že **DateTime** do sdílené složky identifikátor URI, který označuje datum a čas, kdy pořízení snímku sdílené složky se připojí hodnota. Například pokud sdílenou složku identifikátoru URI je http://storagesample.core.file.windows.net/myshare, identifikátor URI je podobný snímku sdílené složky:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Snímky sdílené složky budou zachovány, dokud sami výslovně neodstraníte. Snímek sdílené složky nelze něj jeho základní sdílené složky. Můžete zobrazit výčet snímků přidružených k základní sdílenou složku ke sledování aktuálního snímky. 

Při vytváření snímku sdílené složky sdílené soubory v systémových vlastnostech sdílené složce se zkopírují do snímku sdílené složky se stejnými hodnotami. Základní soubory a metadata sdílené složky jsou zkopírovány také do snímku sdílené složky, pokud nezadáte samostatné metadat snímku při vytváření sdílené složky.

Nelze odstranit sdílenou složku, která má snímky sdílené složky, pokud nejprve odstraňte všechny snímky sdílené složky.

## <a name="space-usage"></a>Využití místa 
Snímky sdílené složky se přičítají ze své podstaty. Pouze data, která se změnila po uložení vaší poslední snímek sdílené složky. Tím se minimalizuje dobu potřebnou k vytvoření snímku sdílené složky a uloží na náklady na úložiště. Všechny operace zápisu do objektu nebo vlastnosti nebo metadata operace aktualizace se počítá směrem k "změněný obsah" a je uložený do snímku sdílené složky. 

Uchovejte prostor na disku, můžete odstranit snímek sdílené složky pro období, kdy se nejvyšší.

I v případě, že snímky sdílené složky jsou postupně uloženy, je potřeba zachovat pouze nejnovější snímek sdílené složky k obnovení sdílené složky. Po odstranění snímku sdílené složky, odeberou se jenom data jedinečný k tomuto snímku sdílené složky. Aktivní snímky obsahují všechny informace, které potřebujete k procházení a obnovení dat (od doby pořízení snímku sdílené složky) k umístění původního nebo alternativního umístění. Můžete obnovit na úrovni položek.

Snímky se nepočítají směrem k vaší sdílené složky 5 TB omezení. Neexistuje žádné omezení, kolik místa na snímky sdílené složky zabírat celkem. Stále platí omezení účtu úložiště.

## <a name="limits"></a>Omezení
Maximální počet snímků sdílené složky, které Azure Files umožňuje ještě dnes je 200. Po snímků 200 sdílených složek je nutné odstranit starší snímků sdílené složky chcete-li vytvořit nové. 

Neexistuje žádné omezení souběžných volání pro vytvoření snímků sdílené složky. Neexistuje žádné omezení množství místa na tuto sdílenou složku, kterou můžete využívat snímky sdílené složky konkrétní. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopírování dat zpět do sdílené složky ze snímku sdílené složky
Operace kopírování, které zahrnují soubory a sdílet snímky dodržovat tato pravidla:

Jednotlivé soubory v souboru snímku sdílené složky můžete zkopírovat na jeho základní sdílenou složku nebo v jiném umístění. Dokáže obnovit starší verzi souboru nebo úplnou sdílenou složku obnovit tak, že zkopírujete soubor po souboru ze snímku sdílené složky. Snímek sdílené složky není povýšen na základní sdílenou složku. 

Snímek sdílené složky zůstanou beze změny po zkopírování, ale základní sdílenou je přepsána kopii dat, která byla k dispozici ve snímku sdílené složky. Všechny počet obnovených souborů směrem k "změnil obsah."

Soubor ve snímku sdílené složky můžete zkopírovat do cíle s jiným názvem. Výsledný cílový soubor je zapisovatelný soubor a snímek sdílené složky.

Pokud cílový soubor se přepíše kopií, všechny snímky sdílené složky přidružené k původní cílový soubor nijak nezmění.

## <a name="general-best-practices"></a>Obecné osvědčené postupy 
Pokud používáte infrastrukturu v Azure, Automatizujte zálohy pro obnovení dat, kdykoli je to možné. Automatické akce jsou spolehlivější než ručních procesů, pomáhá vylepšit ochranu dat a možnosti obnovení. Můžete použít rozhraní REST API, klientské sady SDK nebo skriptování pro automatizaci.

Před nasazením Plánovač snímku sdílené složky, pečlivě zvažte frekvence pořizování snímků sdílené složky a nastavení uchovávání vyhnout zbytečným poplatkům.

Snímky sdílené složky zadejte pouze ochrany na úrovni souboru. Snímky sdílené složky není brání odstranění fat prstem na účet sdílené složky nebo úložiště souborů. Pomáhá chránit před náhodným odstraněním účtu úložiště, můžete zamknout účet úložiště nebo skupinu prostředků.

## <a name="next-steps"></a>Další postup
- Práce s snímků sdílené složky v:
    - [Azure Portal](storage-how-to-use-files-portal.md#create-and-modify-share-snapshots)
    - [PowerShell](storage-how-to-use-files-powershell.md#create-and-modify-share-snapshots)
    - [Rozhraní příkazového řádku](storage-how-to-use-files-cli.md#create-and-modify-share-snapshots)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
- [Nejčastější dotazy k snímku složek](storage-files-faq.md#share-snapshots)
