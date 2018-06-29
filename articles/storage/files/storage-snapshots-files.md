---
title: Přehled snímků sdílenou složku pro soubory Azure | Microsoft Docs
description: Sdílené složky snímků je jen pro čtení verze Azure Files sdílené složky, která je provedená v bodě v čase, jako způsob, jak zálohovat sdílenou složku.
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: af113ae76d81c82ff6c4ced1569aa16f3a9ee27c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063785"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Přehled sdílené složky snímků pro Azure Files 
Soubory Azure poskytuje schopnost pořizovat snímky sdílenou složku Sdílené složky. Sdílet snímky zachycení stavu sdílení v tomto bodě v čase. V tomto článku jsme popisují, jaké funkce poskytovat sdílené složky snímků a jak můžete využít výhod těchto ve vašem případě vlastní použití.

## <a name="when-to-use-share-snapshots"></a>Kdy použít sdílené složky snímků

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrana proti poškození chyba a data aplikací
Aplikace, které používají sdílené složky provádět operace, jako je například zápis, čtení, úložiště, přenos a zpracování. Pokud je špatně nakonfigurovaný. aplikace nebo uvádíme neúmyslným chyb, může dojít náhodnému přepsání nebo poškození na několik bloků. K ochraně před těmito případy, může trvat sdílenou složku snímku před nasazením nového kódu aplikace. Jestliže chyb nebo aplikace chyba s nové nasazení, můžete přejít zpět na předchozí verzi vaše data v této sdílené složky. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochranu před náhodným odstraněním nebo nezamýšleným změny
Představte si, že pracujete s textovým souborem do sdílené složky. Po zavření textového souboru, ztratíte možnost vrátit zpět. V těchto případech je pak potřeba obnovit dřívější verzi souboru. Můžete použít sdílené složky snímků a obnovit předchozí verze souboru v případě, že omylem přejmenován nebo odstraněn.

### <a name="general-backup-purposes"></a>Obecné účely zálohování
Po vytvoření sdílené složky, můžete vytvořit pravidelně snímku sdílenou složku používat k zálohování dat sdílené složky. Sdílenou složku snímku, při pořízení pravidelně pomáhá udržovat předchozí verze dat, která lze použít pro požadavky budoucí auditu nebo zotavení po havárii.

## <a name="capabilities"></a>Možnosti
Sdílené složky snímků je kopii dat v okamžiku, jen pro čtení. Můžete vytvořit, odstranit a spravovat snímky pomocí rozhraní REST API. Stejné funkce jsou dostupné i v klientské knihovny, rozhraní příkazového řádku Azure a portálu Azure. 

Snímky sdílené složky můžete zobrazit pomocí rozhraní REST API a protokolu SMB. Můžete načíst seznam verzí adresář nebo soubor, a můžete připojit přímo jako jednotka na konkrétní verzi. 

Po vytvoření snímku sdílené složky ji můžete číst, kopírovat, nebo odstranit, ale nedojde ke změně. Nelze zkopírovat celou sdílenou složku snímku na jiný účet úložiště. Budete muset udělat to soubor po souboru, pomocí nástroje AzCopy nebo jiných mechanismů kopírování.

Vytváření sdílené složky snímků je k dispozici na úrovni sdílené složky souborů. Načtení je k dispozici na úrovni jednotlivých souborů, umožňující obnovení jednotlivých souborů. Dokončení sdílené složky můžete obnovit pomocí protokolu SMB, rozhraní API REST, na portálu, klientské knihovny nebo PowerShell nebo rozhraní příkazového řádku nástroje.

Sdílenou složku snímku sdílené složky je stejný jako jeho základní sdílené složky. Jediným rozdílem je, že **data a času** hodnota se připojí ke sdílené složce identifikátor URI, který označuje datum a čas, kdy pořízení snímku sdílené složky. Například pokud sdílenou identifikátor URI je http://storagesample.core.file.windows.net/myshare, identifikátor URI je podobná snímku sdílené složky:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Sdílené složky snímků uchová, dokud explicitně odstranit. Sdílenou složku snímku nelze outlive jeho základní sdílené složky. Můžete vytvořit výčet snímků přidružených základní sdílené složky sledovat vaše aktuální snímky. 

Když vytvoříte sdílenou složku snímku sdílené složky, soubory ve vlastnostech systému sdílenou složku se zkopírují do snímku sdílenou složku se stejnými hodnotami. Základní soubory a metadata sdílené složky jsou také zkopírován do snímek sdílené složky, pokud nezadáte samostatné metadata pro sdílenou složku snímku při jeho vytvoření.

Sdílené složky, který má snímky, sdílené složky, pokud nejprve odstraňte všechny snímky sdílené složky nelze odstranit.

## <a name="space-usage"></a>Využití místa 
Sdílené složky snímky jsou ve své podstatě přírůstkové. Pouze data, která se změnila po uložení poslední sdílenou složku snímku. To snižuje čas potřebný k vytvoření snímku sdílené složky a šetří náklady na úložiště. Všechny operace zápisu do objektu nebo vlastnost nebo metadata operace aktualizace se počítá směrem k "změněný obsah" a je uložen ve sdílené složce snímku. 

Kvůli úspoře místa, můžete odstranit sdílenou složku snímku dobu, kdy byl nejvyšší provozu.

Přestože sdílené složky snímků ukládají postupně, je potřeba zachovat pouze nejnovější sdílenou složku snímku obnovit sdílenou složku. Při odstranění snímku sdílenou složku, odeberou se jenom data pro tuto sdílenou složku snímku jedinečné. Aktivní snímky obsahovat veškeré informace, které budete muset procházet a obnovit data (od doby pořízení snímku sdílené složky) do původního umístění nebo alternativního umístění. Můžete obnovit na úrovni položek.

Snímky nemáte započítávat limit 5 TB sdílené složky. Neexistuje žádné omezení, kolik místa na sdílené složky snímků zabírat celkem. Limity účtu úložiště se stále účtují.

## <a name="limits"></a>Omezení
Maximální počet snímků sdílené složky, které soubory Azure umožňuje dnes je 200. Po 200 sdílené složky snímků budete muset odstranit starší sdílené složky snímků Chcete-li vytvořit nové. 

Neexistuje žádné omezení souběžných volání pro vytvoření sdílené složky snímků. Neexistuje žádné omezení množství místa na tuto sdílenou složku, kterou může využít snímky konkrétní sdílenou složku. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopírování dat zpět do sdílené složky ze sdílené složky snímku
Operace kopírování, které zahrnují soubory a sdílet snímky postupujte podle těchto pravidel:

Jednotlivé soubory ve sdílené složce snímku souboru přes můžete zkopírovat do jeho základní složky nebo jiného umístění. Můžete obnovit dřívější verzi souboru nebo obnovit dokončení sdílené složky tak, že zkopírujete soubor po souboru ze sdílené složky snímku. Sdílenou složku snímku není povýšen na základní sdílenou složku. 

Po zkopírování zůstane beze změn sdílenou složku snímku, ale základní sdílené složky dojde k přepsání kopii dat, která byla k dispozici ve sdílené složce snímku. Všechny počet obnovených souborů směrem k "změnit obsah."

Můžete zkopírovat do souboru ve sdílené složce snímku do cílového umístění s jiným názvem. Výsledný cílového souboru je soubor s možností zápisu a není snímek sdílené složky.

Pokud cílový soubor se přepíše s kopií, zachovají všechny snímky sdílené složky přidružené k původní cílového souboru.

## <a name="general-best-practices"></a>Obecné doporučené postupy 
Pokud používáte infrastrukturu v Azure, automatizovat zálohy pro obnovení dat, kdykoli je to možné. Automatické akce jsou spolehlivější než manuální procesy, pomáhá zvýšit ochranu dat a obnovitelnost. Můžete použít rozhraní REST API, Client SDK nebo skriptování pro automatizaci.

Před nasazením Plánovač sdílenou složku snímku, pečlivě zvažte frekvence snímků sdílené složky a nastavení uchovávání účtovány poplatky zbytečné.

Sdílené složky snímků zajišťují jenom ochranu souborů. Sdílené složky snímků nemáte zabránit odstranění prstem fat na účet sdílenou složku nebo úložiště souborů. K ochraně účtu úložiště před náhodným odstraněním, můžete uzamčení účtu úložiště nebo skupinu prostředků.

## <a name="next-steps"></a>Další postup
- Práce s sdílené složky snímků v:
    - [Azure Portal](storage-how-to-use-files-portal.md#create-and-modify-share-snapshots)
    - [PowerShell](storage-how-to-use-files-powershell.md#create-and-modify-share-snapshots)
    - [Rozhraní příkazového řádku](storage-how-to-use-files-cli.md#create-and-modify-share-snapshots)
- [Sdílená složka snímku – nejčastější dotazy](storage-files-faq.md#share-snapshots)
