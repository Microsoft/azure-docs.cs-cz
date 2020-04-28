---
title: Přehled snímků sdílené složky pro soubory Azure | Microsoft Docs
description: Snímek sdílené složky je verze sdílené složky Azure Files, která je určená k určitému časovému okamžiku, jako je třeba pro zálohování sdílené složky.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e09887b8000a0aeb52879d5306bc0a00da5141f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176137"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Přehled snímků sdílené složky pro Soubory Azure 
Azure Files nabízí možnost převzít snímky sdílení sdílených složek. Snímky sdílené složky zachytí stav sdílení v daném časovém okamžiku. V tomto článku jsme popsali, co poskytují snímky pro sdílení a jak je můžete využít ve svém vlastním případu použití.

## <a name="when-to-use-share-snapshots"></a>Kdy použít snímky sdílené složky

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrana proti chybě aplikace a poškození dat
Aplikace, které používají sdílené složky, provádějí operace, jako je například zápis, čtení, ukládání, přenos a zpracování. Pokud je aplikace nesprávně nakonfigurovaná nebo je zavedená neúmyslná chyba, může dojít k náhodnému přepsání nebo poškození na několik bloků. Pro lepší ochranu proti těmto scénářům můžete před nasazením nového kódu aplikace pořídit snímek sdílené složky. Pokud se s novým nasazením zavede chyba nebo Chyba aplikace, můžete se vrátit k předchozí verzi vašich dat v této sdílené složce. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrana před náhodným odstraněním nebo nezamýšlenými změnami
Představte si, že pracujete s textovým souborem ve sdílené složce. Po zavření textového souboru ztratíte možnost vrátit změny zpět. V těchto případech je potřeba obnovit předchozí verzi souboru. Snímky sdílené složky můžete použít k obnovení předchozích verzí souboru, pokud je omylem přejmenováno nebo odstraněno.

### <a name="general-backup-purposes"></a>Obecné účely zálohování
Po vytvoření sdílené složky můžete pravidelně vytvářet snímky sdílené složky, které ji budou používat pro zálohování dat. Snímek sdílené složky při pravidelném provádění pomáhá zachovat předchozí verze dat, které lze použít pro budoucí požadavky na audit nebo zotavení po havárii.

## <a name="capabilities"></a>Možnosti
Snímek sdílené složky je kopie vašich dat jen pro čtení. Snímky můžete vytvářet, odstraňovat a spravovat pomocí REST API. Stejné možnosti jsou také k dispozici v klientské knihovně, rozhraní příkazového řádku Azure a Azure Portal. 

Snímky sdílené složky můžete zobrazit pomocí REST API a SMB. Můžete načíst seznam verzí adresáře nebo souboru a konkrétní verzi můžete připojit přímo jako jednotku (k dispozici pouze v systému Windows – viz [omezení](#limits)). 

Po vytvoření snímku sdílené složky ho můžete číst, kopírovat nebo odstranit, ale nemůžete ho upravovat. Nemůžete zkopírovat celý snímek sdílené složky do jiného účtu úložiště. Tento soubor je nutné provést podle souboru pomocí AzCopy nebo jiného mechanismu kopírování.

Schopnost sdílet snímky je k dispozici na úrovni sdílené složky. Načtení je k dispozici na jednotlivých úrovních souborů, aby bylo možné obnovit jednotlivé soubory. Úplnou sdílenou složku můžete obnovit pomocí protokolu SMB, REST API, portálu, klientské knihovny nebo nástrojů PowerShellu a rozhraní příkazového řádku.

Snímek sdílené složky sdíleného souboru je stejný jako základní sdílená složka. Jediným rozdílem je, že hodnota **DateTime** je připojená k identifikátoru URI sdílené složky, aby označovala čas, kdy byl snímek sdílené složky proveden. Pokud například identifikátor URI sdílené složky je http:\//storagesample.Core.File.Windows.NET/myshare, je identifikátor URI snímku sdílené složky podobný tomuto:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Sdílejte snímky, dokud je explicitně neodstraníte. Snímek sdílené složky nemůže být živý základní sdílenou složkou souborů. Pomocí výčtu snímků přidružených k základní sdílené složce můžete sledovat aktuální snímky. 

Když vytvoříte snímek sdílené složky, soubory ve vlastnostech systému sdílené složky se zkopírují do snímku sdílené složky se stejnými hodnotami. Základní soubory a metadata sdílené složky se zkopírují také do snímku sdílené složky, pokud při vytváření snímku sdílené složky neurčíte samostatná metadata.

Sdílenou složku, která má snímky sdílené složky, nemůžete odstranit, pokud nejprve neodstraníte všechny snímky sdílené složky.

## <a name="space-usage"></a>Využití místa 
Snímky sdílené složky jsou přírůstkové. Uloží se jenom data, která se změnila po uložení posledního snímku sdílené složky. Tím se minimalizuje čas potřebný k vytvoření snímku sdílené složky a ušetří se náklady na úložiště. Každá operace zápisu do objektu nebo vlastnosti nebo operace aktualizace metadat se počítá směrem k změně obsahu a ukládá se do snímku sdílené složky. 

Chcete-li ušetřit místo, můžete snímek sdílené složky odstranit po dobu, kdy byla četnost změn nejvyšší.

I když se snímky sdílené složky ukládají přírůstkově, je třeba zachovat pouze nejnovější snímek sdílené složky, aby bylo možné sdílenou složku obnovit. Když odstraníte snímek sdílené složky, odeberou se jenom data, která jsou pro tento snímek sdílené složky jedinečná. Aktivní snímky obsahují všechny informace, které potřebujete k procházení a obnovování dat (od doby pořízení snímku sdílené složky) do původního umístění nebo do alternativního umístění. Můžete obnovit na úrovni položky.

Počet snímků se nepočítá směrem k limitu sdílení o velikosti 5 TB. Neexistuje žádné omezení velikosti snímků sdílené složky. Omezení účtu úložiště se pořád používají.

## <a name="limits"></a>Omezení
Maximální počet snímků sdílených složek, které soubory Azure umožňují dnes, je 200. Po 200 sdílení snímků musíte odstranit starší snímky sdílené složky, aby bylo možné vytvořit nové. 

Neexistuje žádné omezení počtu současných volání pro vytváření snímků sdílené složky. Velikost místa, které sdílí snímky konkrétní sdílené složky, nemůže spotřebovat žádná omezení. 

V současné době není možné připojit snímky sdílené složky v systému Linux. Důvodem je to, že klient systému Linux SMB nepodporuje připojování snímků, jako je Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopírování dat zpět do sdílené složky ze snímku sdílené složky
Operace kopírování, které zahrnují soubory a sdílené snímky, dodržují tato pravidla:

Jednotlivé soubory ve snímku sdílené složky můžete zkopírovat do své základní sdílené složky nebo do jiného umístění. Můžete obnovit dřívější verzi souboru nebo obnovit úplnou sdílenou složku zkopírováním souboru File by ze snímku sdílené složky. Snímek sdílené složky není povýšen na základní sdílenou složku. 

Snímek sdílené složky zůstane po zkopírování beze změn, ale základní sdílená složka je přepsána kopií dat, která byla k dispozici ve snímku sdílené složky. Počet obnovených souborů se počítá směrem k změně obsahu.

Soubor můžete zkopírovat ve snímku sdílené složky do jiného umístění s jiným názvem. Výsledný cílový soubor je zapisovatelný soubor, nikoli snímek sdílené složky. V takovém případě zůstane vaše základní sdílená složka nedotčená.

Když je cílový soubor přepsán kopií, všechny snímky sdílené složky přidružené k původnímu cílovému souboru zůstanou beze změny.

## <a name="general-best-practices"></a>Obecné osvědčené postupy 
Když provozujete infrastrukturu v Azure, Automatizujte zálohování pro obnovu dat, kdykoli to bude možné. Automatizované akce jsou spolehlivější než ruční procesy a pomáhají zlepšit ochranu dat a možnosti obnovení. Můžete použít REST API, sadu SDK klienta nebo skriptování pro automatizaci.

Než nasadíte Plánovač snímků sdílené složky, pečlivě zvažte četnost snímků snímků sdílené složky a nastavení uchovávání, aby nedocházelo k zbytečným poplatkům.

Sdílet snímky poskytují pouze ochranu na úrovni souborů. Snímky sdílené složky nebrání odstranění prstů v systému souborů ve sdílené složce nebo účtu úložiště. Pro lepší ochranu účtu úložiště před náhodným odstraněním můžete uzamknout účet úložiště nebo skupinu prostředků.

## <a name="next-steps"></a>Další kroky
- Práce s snímky sdílené složky v:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Nejčastější dotazy ke sdílení snímků](storage-files-faq.md#share-snapshots)
