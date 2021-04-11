---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491665"
---
Rychlost a rychlost úspěšnosti daného spuštění nástroje Robocopy budou záviset na několika faktorech:

* IOPS na zdrojovém a cílovém úložišti
* dostupná šířka pásma sítě mezi zdrojem a cílem
* možnost rychlého zpracování souborů a složek v oboru názvů
* počet změn mezi spuštěním nástroje Robocopy


### <a name="iops-and-bandwidth-considerations"></a>Požadavky na IOPS a šířku pásma

V této kategorii je potřeba zvážit schopnosti **zdrojového úložiště**, **cílového úložiště** a **sítě** , které je propojuje. Maximální možná propustnost závisí na nejpomalejších těchto třech součástech. Ujistěte se, že je vaše síťová infrastruktura nakonfigurovaná tak, aby podporovala optimální přenosové rychlosti a jejich nejlepší možnosti.

> [!CAUTION]
> Kopírování co nejrychlejší je často nejužitečnější, zvažte použití vaší místní sítě a zařízení NAS pro jiné, často důležité úkoly.

Pokud existuje riziko, že migrace může monopolizovat dostupné prostředky, nemusí se co nejrychleji kopírovat co nejrychlejší.

* Vezměte v úvahu, kdy je nejlepší ve vašem prostředí spouštět migrace: během dne, mimo špičku nebo během víkendu.
* Zvažte také možnost sítě QoS na Windows serveru, abyste omezili rychlost nástroje Robocopy.
* Vyhněte se zbytečné práci na nástrojích pro migraci.

RobCopy může vkládat zpoždění mezi pakety zadáním `/IPG:n` přepínače, který `n` se měří v milisekundách mezi pakety Robocopy. Použití tohoto přepínače vám může pomoci vyhnout se monopolizationí prostředků u omezených vstupně-výstupních zařízení a přeplněných síťových propojení.

`/IPG:n` nedá se použít pro přesné síťové omezení pro určité MB/s. Místo toho použijte Windows Server Network QoS. Robocopy zcela spoléhá na protokol SMB pro všechny potřeby sítě. Použití protokolu SMB je důvod, proč program Robocopy nemůže ovlivnit propustnost sítě samotné, ale může zpomalit jeho použití. 

Podobná řádková podobnost se vztahuje na IOPS zjištěné na serveru NAS. Velikost clusteru na svazku serveru NAS, velikost paketů a pole dalších faktorů ovlivňují pozorovaný IOPS. Zavedení mezipaketového zpoždění je často nejjednodušší způsob, jak řídit zatížení na serveru NAS. Testuje více hodnot, například z přibližně 20 milisekund (n = 20) na násobky daného čísla. Po zavedení prodlevy můžete vyhodnotit, jestli vaše jiné aplikace teď můžou fungovat podle očekávání. Tato strategie optimalizace vám umožní najít optimální rychlost nástroje Robocopy ve vašem prostředí.

### <a name="processing-speed"></a>Rychlost zpracování

Robocopy projde obor názvů, na který ukazuje, a vyhodnotí každý soubor a složku pro kopírování. Každý soubor bude vyhodnocen při počátečním kopírování a při zachytávání kopií. Například opakované spuštění nástroje Robocopy/MIR proti stejnému zdrojovému a cílovému umístění úložiště. Tato opakovaná spuštění jsou užitečná pro minimalizaci výpadků uživatelů a aplikací a ke zvýšení celkové úspěšnosti migrovaných souborů.

Často jsme standardně posuzujete šířku pásma jako nejúčinnější faktor migrace – a to může být pravdivé. Možnost vytvoření výčtu oboru názvů může mít vliv na celkovou dobu kopírování ještě více pro větší obory názvů s menšími soubory. Zvažte, že kopírování 1 TiB malých souborů bude trvat mnohem déle než kopírování 1 TiB méně, ale větší soubory. Za předpokladu, že všechny ostatní proměnné zůstávají stejné.

Příčinou tohoto rozdílu je výpočetní výkon potřebný k procházení oboru názvů. Robocopy podporuje kopie s více vlákny prostřednictvím `/MT:n` parametru, který n představuje počet vláken procesoru. Takže při zřizování počítače speciálně pro Robocopy zvažte počet jader procesoru a jejich vztah k počtu vláken, která poskytují. Nejběžnější jsou dvě vlákna na jádro. Počet jader a vláken počítače je důležitým datovým bodem pro rozhodování o tom, jaké hodnoty násobného vlákna `/MT:n` byste měli zadat. Zvažte také, kolik úloh nástroje Robocopy máte v plánu paralelně na daném počítači.

Další vlákna budou kopírovat náš TiB příklad malých souborů podstatně rychleji než méně vláken. Navíc výhoda dalších prostředků na 1 TiB většího počtu souborů v současné době nemusí poskytovat proporcionální výhody. Velký počet vláken se pokusí zkopírovat více velkých souborů současně do sítě. Tato dodatečná síťová aktivita zvyšuje pravděpodobnost omezení v případě propustnosti nebo IOPS úložiště.

### <a name="avoid-unnecessary-work"></a>Vyhnout se zbytečné práci

Vyhněte se velkým změnám v oboru názvů. Například přesouvání souborů mezi adresáři, změna vlastností ve velkém měřítku nebo změna oprávnění (seznamy řízení přístupu systému souborů NTFS). Obzvláště změny seznamů ACL můžou mít vysoký dopad, protože mají často vliv na změny v souborech, které jsou v hierarchii složek nižší. Důsledky můžou být:

* Rozšířená doba běhu úlohy nástroje Robocopy, protože všechny soubory a složky ovlivněné změnou seznamu ACL se vyžadují k aktualizaci
* překopírování dat, která byla přesunuta dříve, může být nutné. Například je nutné zkopírovat více dat, když se struktury složek změní poté, co již byly dříve zkopírovány soubory. Úloha Robocopy nemůže přejít zpět na změnu oboru názvů. Další úloha musí Vyprázdnit soubory, které byly dříve přeneseny do staré struktury složek, a znovu odeslat soubory do nové struktury složek.

Dalším důležitým aspektem je použití nástroje Robocopy efektivně. Pomocí doporučeného skriptu Robocopy vytvoříte a uložíte soubor protokolu pro chyby. Může dojít k chybám kopírování – to je normální. Tyto chyby často potřebují spustit více zaokrouhlení kopírovacího nástroje, jako je například Robocopy. Počáteční spuštění od serveru NAS až po DataBox nebo na server do sdílené složky Azure. A jeden nebo více dalších spuštění s přepínačem/MIR zachytí a zopakuje soubory, které se nezkopírovaly.

Měli byste být připravení spustit více hodnot Robocopy proti danému oboru názvů. Po sobě jdoucí běhy dokončí rychleji, protože jejich kopírování je méně, ale omezuje se tím rychlost zpracování tohoto oboru názvů. Když spustíte více zaoblení, můžete každou kulatou hodnotu zrychlit tak, že se příkaz Robocopy nesnaží nepřiměřeně Kopírovat vše v daném běhu. Tyto přepínače Robocopy můžou mít výrazný rozdíl:

* `/R:n` n = jak často se pokusíte zkopírovat soubor, který selhal a 
* `/W:n` n = kolik sekund se má čekat mezi opakovanými pokusy

`/R:5 /W:5` je rozumné nastavení, které můžete upravit na své míru. V tomto příkladu se neúspěšný soubor znovu pokusí o pět sekund, přičemž doba čekání mezi opakovanými pokusy proběhne znovu. Pokud se soubor stále nedaří zkopírovat, pokusí se další úloha Robocopy zopakovat. Často se jedná o soubory, které selhaly, protože se používají, nebo kvůli problémům s vypršením časového limitu může být někdy zkopírován tímto způsobem.
   
