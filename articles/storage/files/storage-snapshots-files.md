---
title: Přehled snímků sdílené složky pro soubory Azure | Dokumenty společnosti Microsoft
description: Snímek sdílené složky je verze sdílené složky Azure Files jen pro čtení, která je přijata v určitém okamžiku, jako způsob, jak zálohovat sdílenou složku.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333184"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Přehled snímků sdílené složky pro Soubory Azure 
Azure Files poskytuje možnost pořizovat snímky sdílených složek sdílení. Sdílené snímky zachycují stav sdílené složky v tomto okamžiku. V tomto článku popisujeme, jaké možnosti sdílení snímků poskytují a jak můžete využít jejich využití v případě vlastního použití.

## <a name="when-to-use-share-snapshots"></a>Kdy použít snímky sdílené složky

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrana proti chybám aplikace a poškození dat
Aplikace, které používají sdílené složky, provádějí operace, jako je zápis, čtení, ukládání, přenos a zpracování. Pokud je aplikace nesprávně nakonfigurována nebo je zavedena neúmyslná chyba, může dojít k náhodnému přepsání nebo poškození několika bloků. Chcete-li chránit před těmito scénáři, můžete pořizovat snímek sdílené složky před nasazením nového kódu aplikace. Pokud je s novým nasazením zavedena chyba nebo chyba aplikace, můžete se vrátit k předchozí verzi dat ve sdílené složce. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrana proti nechtěnému odstranění nebo nechtěným změnám
Představte si, že pracujete na textovém souboru ve sdílené složce. Po zavření textového souboru ztratíte možnost vrátit změny zpět. V těchto případech je pak nutné obnovit předchozí verzi souboru. Snímky sdílené složky můžete použít k obnovení předchozích verzí souboru, pokud je omylem přejmenován nebo odstraněn.

### <a name="general-backup-purposes"></a>Obecné účely zálohování
Po vytvoření sdílené složky můžete pravidelně vytvářet snímek sdílené složky pro sdílení souborů a použít ji pro zálohování dat. Snímek sdílené složky při pravidelném pořizování pomáhá udržovat předchozí verze dat, které lze použít pro budoucí požadavky na audit nebo zotavení po havárii.

## <a name="capabilities"></a>Možnosti
Snímek sdílené složky je kopie dat jen v čase, jen pro čtení. Snímky můžete vytvářet, odstraňovat a spravovat pomocí rozhraní REST API. Stejné možnosti jsou k dispozici také v klientské knihovně, Azure CLI a na webu Azure. 

Snímky sdílené složky můžete zobrazit pomocí rozhraní REST API a Protokolu SMB. Můžete načíst seznam verzí adresáře nebo souboru a určitou verzi můžete připojit přímo jako jednotku (k dispozici pouze v systému Windows - viz [Limity](#limits)). 

Po vytvoření snímku sdílené složky jej lze číst, kopírovat nebo odstraňovat, ale nelze jej upravovat. Nemůžete zkopírovat celý snímek sdílené složky do jiného účtu úložiště. Musíte to udělat soubor po souboru, pomocí AzCopy nebo jiných kopírovacích mechanismů.

Funkce snímkového snímku sdílení je k dispozici na úrovni sdílené složky. Načítání je k dispozici na úrovni jednotlivých souborů, aby bylo možné obnovit jednotlivé soubory. Úplnou sdílenou složku můžete obnovit pomocí rozhraní SMB, rozhraní REST API, portálu, klientské knihovny nebo nástrojů PowerShell/CLI.

Snímek sdílené složky sdílené složky je shodný se základní sdílenou složkou. Jediným rozdílem je, že **datetime** hodnota je připojen a identifikátor URI sdílené složky k označení času, kdy byl pořízen snímek sdílené složky. Pokud je http://storagesample.core.file.windows.net/mysharenapříklad identifikátor URI sdílené složky , identifikátor URI snímku sdílené složky je podobný:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Sdílené snímky přetrvávají, dokud nejsou explicitně odstraněny. Snímek sdílené složky nemůže přežít svou základní sdílenou složku. Můžete vytvořit výčet snímků přidružených ke sdílené složce základní soubor sledovat aktuální snímky. 

Když vytvoříte snímek sdílené složky, soubory ve vlastnostech systému sdílené složky se zkopírují do snímku sdílené složky se stejnými hodnotami. Základní soubory a metadata sdílené složky se také zkopírují do snímku sdílené složky, pokud při vytváření nezadáte samostatná metadata pro snímek sdílené složky.

Sdílenou složku, která má snímky sdílené složky, nelze odstranit, pokud nejprve neodstraníte všechny snímky sdílené složky.

## <a name="space-usage"></a>Využití místa 
Sdílené snímky jsou přírůstkové povahy. Uloží se pouze data, která se změnila po posledním snímku sdílené složky. Tím se minimalizuje čas potřebný k vytvoření snímku sdílené složky a šetří náklady na úložiště. Každá operace zápisu do operace aktualizace objektu nebo vlastnosti nebo metadat se započítává do "změněného obsahu" a je uložena ve snímku sdílené složky. 

Chcete-li ušetřit místo, můžete odstranit snímek sdílené složky pro období, kdy byla konve nejvyšší.

I když jsou snímky sdílené složky uloženy postupně, je třeba zachovat pouze nejnovější snímek sdílené složky, aby bylo možné obnovit sdílenou složku. Když odstraníte snímek sdílené složky, odeberou se pouze data, která jsou pro tento snímek sdílené složky jedinečná. Aktivní snímky obsahují všechny informace, které potřebujete k procházení a obnovení dat (od okamžiku pořízení snímku sdílené složky) do původního umístění nebo do alternativního umístění. Můžete obnovit na úrovni položky.

Snímky se do limitu sdílení 5 TB nezapočítávají. Neexistuje žádné omezení, kolik snímků sdílené složky místa zabírají celkem. Limity účtu úložiště stále platí.

## <a name="limits"></a>Omezení
Maximální počet snímků sdílené složky, které azure soubory umožňuje dnes je 200. Po 200 snímků sdílené složky, budete muset odstranit starší snímky sdílené složky s cílem vytvořit nové. 

Neexistuje žádné omezení pro souběžné volání pro vytváření snímků sdílené složky. Neexistuje žádné omezení na množství místa, které sdílejí snímky určité sdílené složky může spotřebovat. 

Dnes není možné připojit sdílené snímky na Linuxu. Důvodem je, že klient SMB Linux nepodporuje montážní snímky jako Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopírování dat zpět do sdílené složky ze snímku sdílené složky
Operace kopírování, které zahrnují soubory a snímky sdílené složky, se řídí těmito pravidly:

Jednotlivé soubory ve snímku sdílené složky můžete zkopírovat do základní sdílené složky nebo do jiného umístění. Starší verzi souboru můžete obnovit nebo obnovit úplnou sdílenou složku zkopírováním souboru po souboru ze snímku sdílené složky. Snímek sdílené složky není povýšen na základní sdílené složky. 

Snímek sdílené složky zůstane po zkopírování beze změny, ale základní sdílená složka je přepsána kopií dat, která byla k dispozici ve snímku sdílené složky. Všechny obnovené soubory se započítávají do "změněného obsahu".

Soubor ve snímku sdílené složky můžete zkopírovat do jiného cíle s jiným názvem. Výsledný cílový soubor je zapisovatelný soubor a nikoli snímek sdílené složky. V takovém případě zůstane základní sdílená složka beze změny.

Pokud je cílový soubor přepsán kopií, všechny snímky sdílené složky přidružené k původnímu cílovému souboru zůstanou beze změny.

## <a name="general-best-practices"></a>Obecné osvědčené postupy 
Když v Azure spouštět infrastrukturu, automatizujte zálohy pro obnovu dat, kdykoli je to možné. Automatizované akce jsou spolehlivější než manuální procesy, což pomáhá zlepšit ochranu a obnovitelnost dat. Můžete použít rozhraní REST API, client SDK nebo skriptování pro automatizaci.

Před nasazením plánovače snímků sdílené složky pečlivě zvažte frekvenci složky a nastavení uchovávání informací, abyste zabránili zbytečným poplatkům.

Snímky sdílené složky poskytují pouze ochranu na úrovni souboru. Snímky sdílené složky nezabrání odstranění tukových prstů na sdílené složce nebo účtu úložiště. Chcete-li chránit účet úložiště před náhodným odstraněním, můžete účet úložiště nebo skupinu prostředků uzamknout.

## <a name="next-steps"></a>Další kroky
- Práce se snímky sdílené složky v:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [Cli](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Nejčastější dotazy ke snímku sdílení](storage-files-faq.md#share-snapshots)
