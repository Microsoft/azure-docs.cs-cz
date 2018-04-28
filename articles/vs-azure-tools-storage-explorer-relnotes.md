---
title: Poznámky k verzi Microsoft Azure Storage Explorer
description: Poznámky k verzi pro Microsoft Azure Storage Explorer
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: 6274e4ebbc9f3c5b21c479b10e112459000fd28b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Poznámky k verzi Microsoft Azure Storage Explorer

Tento článek obsahuje verze, kterou verzi poznámky pro Azure Storage Explorer 0.9.6 (Preview), a také poznámky k verzi pro předchozí verze.

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadno pracovat s daty Azure Storage ve Windows, systému macOS a Linux.

## <a name="version-096"></a>Verze 0.9.6
02/28/2018

### <a name="download-azure-storage-explorer-096-preview"></a>Stažení Azure Storage Explorer 0.9.6 (Preview)
- [Azure Storage Explorer (Preview) 0.9.6 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer (Preview) 0.9.6 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer (Preview) 0.9.6 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="fixes"></a>Opravy
* Problém zabránila očekávané objektů BLOB nebo soubory není uvedena v editoru. To byl opraven.
* Problém způsobila přepínání mezi snímku pohledů pro zobrazení položek nesprávně. To byl opraven.

### <a name="known-issues"></a>Známé problémy
* Storage Explorer nepodporuje účty služby AD FS.
* Pokud je cílem zásobník Azure, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko Storno"na úlohu, může trvat nějakou dobu tuto úlohu zrušit. Důvodem je, že používáme popsané řešení filtru Storno [zde](https://github.com/Azure/azure-storage-node/issues/317).
* Pokud si zvolíte nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje k filtrování odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Prostředí elektronovým používáno Průzkumníkem úložiště má potíže s hardwarovou akceleraci některé grafický procesor (grafický procesor). Pokud Storage Explorer je zobrazení prázdné hlavní okno (prázdná), můžete zkusit spustit Průzkumníka úložiště z příkazového řádku a zákaz akcelerace GPU přidáním `--disable-gpu` přepínače:

```
./StorageExplorer.exe --disable-gpu
```

* Pro uživatele v Ubuntu 14.04, budete muset zajistit RSZ je aktuální – tento krok můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="previous-releases"></a>Předchozí verze

* [Verze 0.9.5](#version-095)
* [Verze 0.9.4 a 0.9.3](#version-094-and-093)
* [Verze 0.9.2](#version-092)
* [Verze 0.9.1 a 0.9.0](#version-091-and-090)
* [Verze 0.8.16](#version-0816)
* [Verze 0.8.14](#version-0814)
* [Verze 0.8.13](#version-0813)
* [Verze 0.8.12 a 0.8.11 a 0.8.10](#version-0812-and-0811-and-0810)
* [Verze 0.8.9 a 0.8.8](#version-089-and-088)
* [Verze 0.8.7](#version-087)
* [Verze 0.8.6](#version-086)
* [Verze 0.8.5](#version-085)
* [Verze 0.8.4](#version-084)
* [Verze 0.8.3](#version-083)
* [Verze 0.8.2](#version-082)
* [Verze 0.8.0](#version-080)
* [Verze 0.7.20160509.0](#version-07201605090)
* [Verze 0.7.20160325.0](#version-07201603250)
* [Verze 0.7.20160129.1](#version-07201601291)
* [Verze 0.7.20160105.0](#version-07201601050)
* [Verze 0.7.20151116.0](#version-07201511160)

## <a name="version-095"></a>Verze 0.9.5
02/06/2018

### <a name="download-azure-storage-explorer-095-preview"></a>Stažení Azure Storage Explorer 0.9.5 (Preview)
- [Azure Storage Explorer (Preview) 0.9.5 pro Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer (Preview) 0.9.5 pro Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer (Preview) 0.9.5 pro Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nová

* Podpora pro sdílené složky snímky:
    * Vytvořit a spravovat snímky pro sdílené složky.
    * Jako prozkoumávání snadno přepněte zobrazení mezi snímky sdílených složek.
    * Obnovte předchozí verze souborů.
* Náhled podpora pro Azure Data Lake Store:
    * Připojte k prostředkům ADLS napříč více účtů.
    * Připojit k a sdílet prostředky ADLS pomocí ADL identifikátory URI.
    * Provedení operace rekurzivně základní soubor nebo složku.
    * Režim PIN kódu jednotlivými složkami pro rychlý přístup.
    * Zobrazí statistické údaje o složky.

### <a name="fixes"></a>Opravy
* Vylepšení výkonu při spuštění.
* Různé opravy chyb.

### <a name="known-issues"></a>Známé problémy
* Storage Explorer nepodporuje účty služby AD FS.
* Pokud je cílem zásobník Azure, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko Storno"na úlohu, může trvat nějakou dobu tuto úlohu zrušit. To je proto zde používáme popsané řešení filtru Storno.
* Pokud si zvolíte nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje k filtrování odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Prostředí elektronovým používáno Průzkumníkem úložiště má potíže s hardwarovou akceleraci některé grafický procesor (grafický procesor). Pokud Storage Explorer je zobrazení prázdné hlavní okno (prázdná), můžete zkusit spustit Průzkumníka úložiště z příkazového řádku a zákaz akcelerace GPU přidáním `--disable-gpu` přepínače:

```
./StorageExplorer.exe --disable-gpu
```

* Pro uživatele v Ubuntu 14.04, budete muset zajistit RSZ je aktuální – tento krok můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Verze 0.9.4 a 0.9.3
01/21/2018

### <a name="download-azure-storage-explorer-094-preview"></a>Stažení Azure Storage Explorer 0.9.4 (Preview)
* [Stažení Azure Storage Explorer (Preview) 0.9.4 pro Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Stažení Azure Storage Explorer (Preview) 0.9.4 pro Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Stažení Azure Storage Explorer (Preview) 0.9.4 pro Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Nová
* Vaše stávající okno Průzkumníka úložiště bude znovu použít při:
    * Otevírání přímé odkazy vygenerovaných Storage Explorer.
    * Otevřete Storage Explorer z portálu.
    * Otevřete Storage Explorer z rozšíření Azure úložiště VS Code (už brzy).
* Přidání možnosti otevřete nové okno Průzkumníka úložiště z v rámci Storage Explorer.
    * Pro systém Windows existuje možnost 'nové okno, v nabídce Soubor a v místní nabídce na hlavním panelu.
    * Pro počítače Mac je možnost "nové okno, v nabídce aplikace.

### <a name="fixes"></a>Opravy
* Vyřešený problém zabezpečení. Proveďte upgrade 0.9.4 na vám nejdřívější pohodlí.
* Původní aktivity nebyly se odpovídajícím způsobem vyčistit. To vliv na výkon dlouhotrvající úlohy. Že jsou teď ještě čistí správně.
* Akce zahrnující velkého počtu souborů a adresářů by způsobilo příležitostně Storage Explorer chcete ukotvit. Požadavky na Azure pro sdílené složky jsou nyní trottled k omezení využití prostředků systému.

### <a name="known-issues"></a>Známé problémy
* Storage Explorer nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Explorer" a "Správa zobrazení účet" by měla být Ctrl nebo Cmd + Shift + E a Ctrl nebo Cmd + Shift + A v uvedeném pořadí.
* Pokud je cílem zásobník Azure, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko Storno"na úlohu, může trvat nějakou dobu tuto úlohu zrušit. To je proto zde používáme popsané řešení filtru Storno.
* Pokud si zvolíte nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje k filtrování odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Prostředí elektronovým používáno Průzkumníkem úložiště má potíže s hardwarovou akceleraci některé grafický procesor (grafický procesor). Pokud Storage Explorer je zobrazení prázdné hlavní okno (prázdná), můžete zkusit spustit Průzkumníka úložiště z příkazového řádku a zákaz akcelerace GPU přidáním `--disable-gpu` přepínače:
```
./StorageExplorer --disable-gpu
```
* Pro uživatele v Ubuntu 14.04, budete muset zajistit RSZ je aktuální – tento krok můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Verze 0.9.2
11/01/2017

### <a name="hotfixes"></a>Opravy hotfix
* Při úpravě hodnoty Edm.DateTime pro entity tabulky v závislosti na místní časové pásmo nebyly možné změny neočekávaná data. Editor teď používá pole prostý text, poskytuje přesné a konzistentní kontrolu nad Edm.DateTime hodnoty.
* Nahrávání nebo stahování skupinu objektů BLOB při připojené k název a klíč se nespustí. To byl opraven.
* Dříve Storage Explorer by pouze vyzve k novému ověření starý účet, pokud jeden nebo více odběrů na účet byl vybrán. Nyní Storage Explorer zobrazí výzvu i v případě, že účet je plně odfiltrována.
* V doméně koncové body Azure US Government byla chybná. Byl opraven.
* Tlačítko použít na panelu Spravovat účty se někdy těžko klikněte na tlačítko. K tomu by už dojít.

### <a name="new"></a>Nová
* Náhled podpora pro Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytvoření databáze a kolekce
    * Pracovat s daty
    * Dotaz, vytvoření nebo odstranění dokumentů
    * Aktualizace uložené procedury, uživatelem definované funkce nebo aktivační události
    * Použijte připojovací řetězce pro připojení k a spravovat databáze
* Zvýšení výkonu nahrávání nebo stahování mnoho malých objekty BLOB.
* Přidat "Opakujte vše" akce, pokud tam jsou chyby v objektu blob nahrávání skupině nebo skupinu stažení objektů blob.
* Storage Explorer se teď pozastaví iterace během blob nahrávání nebo stahování, pokud je zjištěno, že došlo ke ztrátě připojení k síti. Iterace můžete poté obnovit, jakmile už znovu navázáno síťové připojení.
* Přidání možnosti "Zavřít vše", "Zavřít jiné" a "Zavřít" karty pomocí místní nabídky.
* Storage Explorer teď používá nativní dialogová okna a nativní kontextové nabídky.
* Přístupnější je nyní Průzkumníka úložiště. Mezi vylepšení patří:
    * Vylepšené obrazovky čtečky podporu pro NVDA v systému Windows a VoiceOver v systému Mac
    * Vylepšené motivů vysoký kontrast
    * Použití tabulátoru a klávesnice fokus klávesnice opravy

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stažení objektů blob s neplatným názvem souboru systému Windows, operace selže. Storage Explorer bude nyní zjistí, jestli název objektu blob je neplatný a požádejte, pokud chcete zakódovat je nebo přeskočit objektu blob. Storage Explorer taky bude zjišťovat, pokud název souboru zdá být zakódován a zeptá, pokud chcete dekódovat před nahráním.
* Během nahrávání objekt blob se editor pro cílový kontejner objektu blob nebude někdy aktualizovat správně. To byl opraven.
* Který poklesl podporu pro několik formy připojovací řetězce a identifikátory URI SAS. Jsme vyřešili všechny známé problémy, ale odešlete názor, pokud dojde k další problémy.
* Oznámení o aktualizaci bylo přerušeno pro některé uživatele v 0.9.0. Tento problém byl opraven a u uživatelů, vliv chyb, můžete ručně stáhnout nejnovější verzi Storage Explorer [zde](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Známé problémy
* Storage Explorer nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Explorer" a "Správa zobrazení účet" by měla být Ctrl nebo Cmd + Shift + E a Ctrl nebo Cmd + Shift + A v uvedeném pořadí.
* Pokud je cílem zásobník Azure, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko Storno"na úlohu, může trvat nějakou dobu tuto úlohu zrušit. To je proto zde používáme popsané řešení filtru Storno.
* Pokud si zvolíte nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje k filtrování odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Prostředí elektronovým používáno Průzkumníkem úložiště má potíže s hardwarovou akceleraci některé grafický procesor (grafický procesor). Pokud Storage Explorer je zobrazení prázdné hlavní okno (prázdná), můžete zkusit spustit Průzkumníka úložiště z příkazového řádku a zákaz akcelerace GPU přidáním `--disable-gpu` přepínače:
```
./StorageExplorer --disable-gpu
```
* Pro uživatele v Ubuntu 14.04, budete muset zajistit RSZ je aktuální – tento krok můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Verze 0.9.1 a 0.9.0
10/20/2017
### <a name="new"></a>Nová
* Náhled podpora pro Azure Cosmos DB:
    * [Online dokumentace](./cosmos-db/storage-explorer.md)
    * Vytvoření databáze a kolekce
    * Pracovat s daty
    * Dotaz, vytvoření nebo odstranění dokumentů
    * Aktualizace uložené procedury, uživatelem definované funkce nebo aktivační události
    * Použijte připojovací řetězce pro připojení k a spravovat databáze
* Zvýšení výkonu nahrávání nebo stahování mnoho malých objekty BLOB.
* Přidat "Opakujte vše" akce, pokud tam jsou chyby v objektu blob nahrávání skupině nebo skupinu stažení objektů blob.
* Storage Explorer se teď pozastaví iterace během blob nahrávání nebo stahování, pokud je zjištěno, že došlo ke ztrátě připojení k síti. Iterace můžete poté obnovit, jakmile už znovu navázáno síťové připojení.
* Přidání možnosti "Zavřít vše", "Zavřít jiné" a "Zavřít" karty pomocí místní nabídky.
* Storage Explorer teď používá nativní dialogová okna a nativní kontextové nabídky.
* Přístupnější je nyní Průzkumníka úložiště. Mezi vylepšení patří:
    * Vylepšené obrazovky čtečky podporu pro NVDA v systému Windows a VoiceOver v systému Mac
    * Vylepšené motivů vysoký kontrast
    * Použití tabulátoru a klávesnice fokus klávesnice opravy

### <a name="fixes"></a>Opravy
* Pokud jste se pokusili otevřít nebo stažení objektů blob s neplatným názvem souboru systému Windows, operace selže. Storage Explorer bude nyní zjistí, jestli název objektu blob je neplatný a požádejte, pokud chcete zakódovat je nebo přeskočit objektu blob. Storage Explorer taky bude zjišťovat, pokud název souboru zdá být zakódován a zeptá, pokud chcete dekódovat před nahráním.
* Během nahrávání objekt blob se editor pro cílový kontejner objektu blob nebude někdy aktualizovat správně. To byl opraven.
* Který poklesl podporu pro několik formy připojovací řetězce a identifikátory URI SAS. Jsme vyřešili všechny známé problémy, ale odešlete názor, pokud dojde k další problémy.
* Oznámení o aktualizaci bylo přerušeno pro některé uživatele v 0.9.0. Tento problém byl opraven a u uživatelů, vliv chyb, můžete ručně stáhnout nejnovější verzi Storage Explorer [sem](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Známé problémy
* Storage Explorer nepodporuje účty služby AD FS.
* Klávesové zkratky pro "Zobrazení Explorer" a "Správa zobrazení účet" by měla být Ctrl nebo Cmd + Shift + E a Ctrl nebo Cmd + Shift + A v uvedeném pořadí.
* Pokud je cílem zásobník Azure, odesílání určité soubory jako doplňovací objekty BLOB může selhat.
* Po kliknutí na tlačítko Storno"na úlohu, může trvat nějakou dobu tuto úlohu zrušit. To je proto zde používáme popsané řešení filtru Storno.
* Pokud si zvolíte nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje k filtrování odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Prostředí elektronovým používáno Průzkumníkem úložiště má potíže s hardwarovou akceleraci některé grafický procesor (grafický procesor). Pokud Storage Explorer je zobrazení prázdné hlavní okno (prázdná), můžete zkusit spustit Průzkumníka úložiště z příkazového řádku a zákaz akcelerace GPU přidáním `--disable-gpu` přepínače:
```
./StorageExplorer --disable-gpu
```
* Pro uživatele v Ubuntu 14.04, budete muset zajistit RSZ je aktuální – tento krok můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Verze 0.8.16
8/21/2017

### <a name="new"></a>Nová
* Při otevření objektu blob Storage Explorer zobrazí výzvu k nahrání stažený soubor, pokud je zjištěna změna
* Rozšířené zásobník Azure přihlašování uživatelů
* Zvýšení výkonu nahrávání nebo stahování mnoho malých souborů ve stejnou dobu


### <a name="fixes"></a>Opravy
* Pro některé typy objektů blob rozhodnete "replace" při nahrávání došlo ke konfliktu někdy výsledkem by nahrávání Probíhá restartování.
* Ve verzi 0.8.15 by někdy nahrávání stalace v 99 %.
* Při nahrávání souborů do sdílené složky, pokud jste se rozhodli odeslat do adresáře, která nebyla ještě neexistuje, dojde k selhání odeslání.
* Storage Explorer byla nesprávně generování časová razítka pro sdílené přístupové podpisy a tabulku.


### <a name="known-issues"></a>Známé problémy
* Pomocí názvu a klíče připojovací řetězec momentálně nefunguje. Ho bude vyřešený v příští verzi. Do té doby můžete připojit pomocí názvu a klíče.
* Pokud se pokusíte otevřít soubor s neplatným názvem souboru systému Windows, povede ke stažení souboru nebyla nalezena chyba.
* Po kliknutí na tlačítko Storno"na úlohu, může trvat nějakou dobu tuto úlohu zrušit. Jedná se o omezení knihovny uzlu úložiště Azure.
* Po dokončení nahrávání objektů blob, je aktualizovat na kartě, který inicializován nahrávání. Toto je liší od předchozí chování a také způsobí přesměrováni zpět do kořenového adresáře, které jsou v kontejneru.
* Pokud si zvolíte nesprávný certifikát PIN kód nebo čipová karta, je potřeba restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje k filtrování odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Pro uživatele v Ubuntu 14.04, budete muset zajistit RSZ je aktuální – tento krok můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Pro uživatele v Ubuntu č. 17.04 budete muset nainstalovat GConf – to můžete provést spuštěním následujících příkazů a restartujte svůj počítač:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Verze 0.8.14
06/22/2017

### <a name="new"></a>Nová

* Aktualizovaná verze elektronovým k 1.7.2 chcete využít několik důležité aktualizace zabezpečení
* Nyní můžete rychle k online průvodci odstraňováním potíží z nabídky Nápověda
* Storage Explorer řešení potíží s [Průvodce][2]
* [Pokyny] [ 3] o připojení k předplatnému Azure zásobníku

### <a name="known-issues"></a>Známé problémy

* Tlačítka v dialogovém okně Odstranit složku potvrzení nezaregistrujete kliknutími myši v systému Linux. Alternativní řešení je použití klávesy Enter
* Pokud zvolíte nesprávný certifikát PIN kód nebo čipová karta pak budete muset restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje, chcete-li filtrovat odběrů
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílené složky, sdílené složky uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Ubuntu 14.04 instalace musí aktualizovat verzi RSZ nebo upgradovat – kroky pro upgrade jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Verze 0.8.13
05/12/2017

#### <a name="new"></a>Nová

* Storage Explorer řešení potíží s [Průvodce][2]
* [Pokyny] [ 3] o připojení k předplatnému Azure zásobníku

#### <a name="fixes"></a>Opravy

* Pevná: Odeslání souboru měl vysokou možnost, že nedostatku paměti
* Opravené: Můžete teď přihlásit pomocí PIN kódu nebo čipová karta
* Pevná: Otevřete portálu teď funguje s Azure China, Azure v Německu, Azure US Government a Azure zásobníku
* Pevná: Při odesílání do složky na kontejner objektů blob, "Neplatnou operaci" by někdy dojde k chybě
* Pevná: Vybrat vše zakázal při správě snímků
* Opravené: Může získat metadata objektu blob základní přepsána po z jeho snímků vlastností

#### <a name="known-issues"></a>Známé problémy

* Pokud zvolíte nesprávný certifikát PIN kód nebo čipová karta pak budete muset restartovat, aby bylo možné používat Storage Explorer zapomněli rozhodnutí
* Při možnosti příchozí nebo odchozí, může úroveň přiblížení na okamžik resetovat na výchozí úrovni
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje, chcete-li filtrovat odběrů
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Ubuntu 14.04 instalace musí aktualizovat verzi RSZ nebo upgradovat – kroky pro upgrade jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Verze 0.8.12 a 0.8.11 a 0.8.10
04/07/2017

#### <a name="new"></a>Nová

* Storage Explorer se teď automaticky zavře po instalaci aktualizace z oznámení o aktualizaci
* Rychlý přístup na místě poskytuje lepší prostředí pro práci se často používané prostředky
* V editoru kontejner objektů Blob se nyní zobrazí které nástroj virtual machine zapůjčených blob patří do
* Nyní lze sbalit na levé straně panelu
* Zjišťování se teď spustí ve stejnou dobu jako soubor ke stažení
* Statistik použití v kontejneru objektů Blob, sdílené složky a tabulku editory zobrazíte velikost prostředku nebo výběr
* Můžete se teď může přihlásit k Azure Active Directory (AAD) na základě účtů Azure zásobníku.
* Teď můžete nahrát archivní soubory přes 32MB na prémiové účty úložiště
* Vylepšená podpora usnadnění přístupu
* Nyní můžete přidat důvěryhodný Base-64 kódovaný certifikáty X.509 SSL přejdete na Upravit -&gt; certifikáty SSL -&gt; Import certifikátů

#### <a name="fixes"></a>Opravy

* Pevné: po aktualizovat přihlašovací údaje účtu, stromovém zobrazení by někdy aktualizace automaticky
* Pevná: generování SAS pro emulátor fronty a tabulky by způsobilo neplatná adresa URL
* Opravené: prémiové účty úložiště lze nyní rozbalit proxy server je povolen program
* Pevné: tlačítka použít na stránce Správa účtů nebude fungovat, pokud jste měli 1 nebo 0 účty vybrané
* Pevné: odesílání objekty BLOB, které vyžadují řešení konfliktu může selhat - fixed v 0.8.11
* Pevná: odeslání názoru byla porušena v 0.8.11 - fixed v 0.8.12

#### <a name="known-issues"></a>Známé problémy

* Po upgradu na 0.8.10, musíte aktualizovat všechny svoje přihlašovací údaje.
* Při možnosti příchozí nebo odchozí, úroveň přiblížení může na okamžik resetovat na výchozí úrovni.
* Více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby.
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje, chcete-li filtrovat odběry.
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entity.
* I když zásobník Azure aktuálně nepodporuje sdílených složek, sdílených složek uzel se objeví stále v připojené účtu úložiště Azure zásobníku.
* Ubuntu 14.04 instalace musí aktualizovat verzi RSZ nebo upgradovat – kroky pro upgrade jsou níže:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Verze 0.8.9 a 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nová

* Storage Explorer 0.8.9 automaticky stáhne nejnovější aktualizace.
* Opravy hotfix: portálu generovaný identifikátor URI SAS pro připojení k účtu úložiště by způsobilo chybu.
* Teď můžete vytvářet, spravovat a zvýšit úroveň snímky objektů blob.
* Můžete teď přihlášení k účtům Azure China Azure v Německu a Azure US Government.
* Nyní můžete měnit úroveň přiblížení. Pomocí možností v nabídce zobrazení přiblížit, oddálit a resetovat přiblížení.
* Uživatel metadat pro objekty BLOB a soubory jsou nyní podporovány znaky znakové sady Unicode.
* Vylepšení přístupnosti.
* Poznámky k verzi na další verzi si můžete prohlížet oznámení o aktualizaci. Můžete také zobrazit aktuální poznámky k verzi z nabídky Nápověda.

#### <a name="fixes"></a>Opravy

* Opravené: číslo verze je nyní správně zobrazen v Ovládacích panelech v systému Windows
* Opravené: vyhledávání již není omezeno na 50 000 uzly
* Opravené: nahrát do sdílené složky navždy spouštějí, pokud cílový adresář ještě neexistovaly
* Opravené: vylepšení stability dlouho nahrávání a stahování

#### <a name="known-issues"></a>Známé problémy

* Při možnosti příchozí nebo odchozí, úroveň přiblížení může na okamžik resetovat na výchozí úrovni.
* Rychlý přístup pracuje pouze s odběru na základě položky. V této verzi nepodporuje místních prostředků nebo prostředky, které jsou připojené prostřednictvím klíč nebo tokenu SAS.
* Rychlý přístup může trvat a přejděte do cílového prostředku, v závislosti na tom, kolik prostředků, máte několik sekund.
* Více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby.

12/16/2016
### <a name="version-087"></a>Verze 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nová

* Můžete vybrat, jak se vyřešit konflikty na začátku relaci aktualizace, stažení nebo kopírování v okně aktivity
* Najeďte myší na kartě zobrazit úplnou cestu prostředků úložiště
* Když kliknete na kartě, synchronizuje s jeho umístění v navigačním podokně levé straně

#### <a name="fixes"></a>Opravy

* Opravené: Storage Explorer je nyní důvěryhodné aplikace v systému Mac
* Opravené: Ubuntu 14.04 se znovu podporuje
* Opravené: Někdy přidat účet uživatelského rozhraní bliká při načítání odběrů
* Opravené: Někdy ne všechny prostředky úložiště byly uvedené v navigačním podokně levé straně
* Pevná: V podokně Akce někdy zobrazí prázdné akce
* Opravené: Velikost okna z poslední uzavřené relace je nyní uchována
* Opravené: Můžete otevřít několik karet pro stejný prostředek pomocí místní nabídky

#### <a name="known-issues"></a>Známé problémy

* Rychlý přístup pracuje pouze s odběru na základě položky. V této verzi nepodporuje místních prostředků nebo prostředky, které jsou připojené prostřednictvím klíč nebo tokenu SAS
* Rychlý přístup může trvat a několik sekund, přejděte na cílový prostředek, v závislosti na tom, kolik prostředků máte
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Obslužné rutiny vyhledávání hledání mezi uzly přibližně 50 000 - poté výkonu může být ovlivněno nebo může způsobit, že neošetřené výjimky
* Pro první přihlášení pomocí Průzkumníka úložiště v systému macOS může se zobrazit více výzev žádostí o oprávnění uživatele pro přístup k řetězci klíčů. Doporučujeme že vybrat vždy povolit tak nebude znovu objeví řádku

11/18/2016
### <a name="version-086"></a>Verze 0.8.6

#### <a name="new"></a>Nová

* Můžete teď pin nejčastěji používá služby pro rychlý přístup snadno navigace
* Nyní lze otevřít více editory v různých kartách. Jedním kliknutím zobrazíte dočasné kartu; Poklikejte na kartě trvalé. Můžete také kliknutím na kartu dočasné aby trvalé karta
* Jsme provedli znatelné výkonu a zlepšení stability pro odesílání a stahování souborů, zejména u velkých souborů na rychlé počítače
* Prázdné složky "virtuální" teď lze vytvořit v kontejnerech objektů blob
* Mít znovu zavedli jsme vymezené vyhledávání s naší nové rozšířené dílčí řetězec hledání, takže Teď máte dvě možnosti pro vyhledávání:
    * Globální vyhledávání – stačí do textového pole hledání zadejte hledaný termín
    * Prohledejte – klikněte na ikonu lupy vedle uzlu, pak přidejte hledaný termín na konec cesty, nebo klikněte pravým tlačítkem a vyberte "Vyhledávání z zde"
* Jsme přidali různé motivy: Light (výchozí), světlý, vysoce kontrastní černá a Vysoký kontrast prázdné. Přejděte na Úpravy -&gt; motivy, chcete-li změnit vaši volbu motivů
* Můžete upravit vlastnosti objektů Blob a souborů
* Teď podporujeme kódovaného (base64) a nekódovaného fronty zpráv
* V systému Linux, OS 64-bit je nyní vyžaduje. Pro tuto verzi podporujeme pouze 64bitové verze Ubuntu 16.04.1 LTS
* Aktualizovali jsme naši logo!

#### <a name="fixes"></a>Opravy

* Pevná: Obrazovky zmrazení problémy
* Opravené: Rozšířené zabezpečení
* Pevná: Zobrazovat může někdy duplicitní připojené účty
* Opravené: Objekt blob se typ obsahu definovaný může generovat výjimky
* Pevná: Otevření panelu dotaz na prázdná tabulka nebylo možné
* Opravené: Se liší chyby ve vyhledávání
* Pevné: Zvýšit počet prostředků načtených z 50 do 100 při kliknutí na "Více zatížení"
* Pevná: Při prvním spuštění, pokud je účet přihlášeni, jsme teď vybrat všechna předplatná pro tento účet ve výchozím nastavení

#### <a name="known-issues"></a>Známé problémy

* Tato verze Storage Explorer nefunguje na Ubuntu 14.04
* Chcete-li spustit několik karet pro stejný prostředek, neklikejte na nepřetržitě se stejným prostředkem. Klikněte na jiný prostředek a potom přejděte zpět a potom klikněte na původní prostředek, který ho znovu otevřete na jiné kartě
* Rychlý přístup pracuje pouze s odběru na základě položky. V této verzi nepodporuje místních prostředků nebo prostředky, které jsou připojené prostřednictvím klíč nebo tokenu SAS
* Rychlý přístup může trvat a několik sekund, přejděte na cílový prostředek, v závislosti na tom, kolik prostředků máte
* S více než 3 skupiny objektů BLOB nebo ve stejnou dobu ukládání souborů může způsobit chyby
* Obslužné rutiny vyhledávání hledání mezi uzly přibližně 50 000 - poté výkonu může být ovlivněno nebo může způsobit, že neošetřené výjimky

10/03/2016
### <a name="version-085"></a>Verze 0.8.5

#### <a name="new"></a>Nová

* Teď můžete připojit k účtům úložiště a prostředky použít klíče generované portál SAS

#### <a name="fixes"></a>Opravy

* Pevná: časování při hledání někdy způsobila uzly k jiné rozšíření
* Opravené: "Použít protokol HTTP" nefunguje při připojování k účtům úložiště s názvem účtu a klíč
* Opravené: Klíče SAS (ty speciálně generované portálu) vrátí chybu "koncové lomítko."
* Opravené: import tabulky problémy
    * Někdy měla vrátit klíč oddílu a klíč řádku
    * Nelze číst "null" klíče oddílů

#### <a name="known-issues"></a>Známé problémy

* Obslužné rutiny vyhledávání hledání mezi uzly přibližně 50 000 - poté může mít dopad na výkon
* Azure zásobník nepodporuje aktuálně soubory, tak při pokusu o rozbalení souborů se zobrazí chyba

09/12/2016
### <a name="version-084"></a>Verze 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nová

* Generovat přímé odkazy na účty úložiště, kontejnery, fronty, tabulky nebo sdílené složky pro sdílení a podporu snadný přístup k prostředkům – Windows a Mac OS
* Hledat kontejnery objektů blob, tabulek, front, sdílené složky nebo účty úložiště ze do vyhledávacího pole
* Nyní můžete seskupit klauzule v Tvůrce dotazů tabulky
* Přejmenujte a zkopírujte a vložte kontejnery objektů blob, sdílené složky, tabulky, objekty BLOB, objektů blob složek, souborů a adresářů z v rámci SAS připojené účty a kontejnery
* Přejmenování a kopírování objektu blob kontejnery a sdílené složky teď zachovat vlastnosti a metadata

#### <a name="fixes"></a>Opravy

* Opravené: nelze upravit entity tabulky, pokud obsahují logická hodnota nebo binární vlastnosti

#### <a name="known-issues"></a>Známé problémy

* Obslužné rutiny vyhledávání hledání mezi uzly přibližně 50 000 - poté může mít dopad na výkon

08/03/2016
### <a name="version-083"></a>Verze 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nová

* Přejmenujte kontejnery, tabulky, sdílené složky
* Vylepšené prostředí Tvůrce dotazů
* Možnost uložení a načtení dotazů
* Přímé odkazy na úložiště účtů nebo kontejnerů, fronty, tabulky nebo sdílené složky pro sdílení a snadno přístup k prostředkům (pouze pro systém Windows - systému macOS podporu brzo!)
* Schopnost spravovat a konfigurovat pravidla CORS

#### <a name="fixes"></a>Opravy

* Opravené: Accounts Microsoft vyžadovat opakované ověření každých 8 – 12 hodin

#### <a name="known-issues"></a>Známé problémy

* Někdy uživatelského rozhraní se může objevit ukotvené – tím se maximalizuje okno pomůže tento problém vyřešit
* instalace systému macOS může vyžadovat zvýšenými oprávněními
* Panel nastavení účtu může zobrazovat, je potřeba zadat přihlašovací údaje, chcete-li filtrovat odběrů
* Přejmenování sdílené složky, kontejnery objektů blob a tabulek nezachovává metadata nebo dalších vlastností kontejneru, například kvóta pro sdílenou složku, úroveň veřejného přístupu nebo zásady přístupu
* Přejmenování objekty BLOB (samostatně nebo v kontejneru objektů blob přejmenovat) nezachovává snímky. Během přejmenovat se zachovají všechny ostatní vlastnosti a metadat pro objekty BLOB, soubory a entit
* Kopírování nebo přejmenování prostředky nefunguje v rámci SAS připojené účty

07/07/2016
### <a name="version-082"></a>Verze 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nová

* Účty úložiště jsou seskupené podle odběry; vývoj pro úložiště a prostředky, které jsou připojené prostřednictvím klíč nebo SAS se zobrazují v uzlu (místní a připojené)
* Odhlaste se z účtů v panelech "Nastavení účtu Azure"
* Konfigurace nastavení proxy serveru povolit a spravovat přihlášení
* Vytvoření a rozdělit zapůjčení objektů blob
* Kontejnery otevřete objektů blob, fronty, tabulky a soubory s jedním kliknutím

#### <a name="fixes"></a>Opravy

* Pevná: Vložit s knihovnami .NET nebo Java zprávy fronty nejsou dekódovat správně z formátu base64.
* Opravené: $metrics tabulky nejsou zobrazeny pro účty úložiště objektů Blob
* Opravené: uzel tabulky nefunguje pro místní úložiště (vývoj)

#### <a name="known-issues"></a>Známé problémy

* instalace systému macOS může vyžadovat zvýšenými oprávněními

06/15/2016
### <a name="version-080"></a>Verze 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nová

* Podpora sdílení souborů: zobrazení, odesílání, stahování, kopírování souborů a adresářů, identifikátory URI SAS (vytvořit a připojit)
* Vylepšené uživatelské prostředí pro připojování k úložišti s identifikátory URI SAS nebo účet klíče
* Export výsledků dotazu tabulky
* Změny pořadí sloupců tabulky a přizpůsobení
* Zobrazení $logs kontejnery objektů blob a tabulek $metrics pro účty úložiště pomocí povoleno metriky
* Vylepšené export a import chování, teď obsahuje typ hodnoty vlastnosti

#### <a name="fixes"></a>Opravy

* Opravené: nahrávání nebo stahování velké objekty BLOB může způsobit neúplné nahrávání nebo stahování
* Opravené: úpravy, přidáním nebo importováním entitu s hodnotou číselných řetězců ("1") bude převedena do double
* Pevná: Nelze rozbalte uzel v místním vývojovém prostředí

#### <a name="known-issues"></a>Známé problémy

* $metrics tabulky nejsou viditelné pro účty úložiště objektů Blob
* Zprávy fronty přidány prostřednictvím kódu programu, se nemusí zobrazit správně Pokud zprávy jsou zakódován pomocí kódování Base64

05/17/2016
### <a name="version-07201605090"></a>Verze 0.7.20160509.0

#### <a name="new"></a>Nová

* Lepší zpracování chyb pro aplikace, dojde k chybě

#### <a name="fixes"></a>Opravy

* Kde informační panel zpráv někdy nezobrazovat až při přihlašovací údaje nebyly potřeba opravené chyby

#### <a name="known-issues"></a>Známé problémy

* Tabulky: Přidávání, úpravy nebo import entita, která má vlastnost s ambiguously číselnou hodnotu, jako je například "1" nebo "1.0" a uživatel se pokusí odeslat jako `Edm.String`, hodnotu, vrátí se znovu prostřednictvím rozhraní API jako Edm.Double klienta

03/31/2016

### <a name="version-07201603250"></a>Verze 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nová

* Tabulka podporu: zobrazení, dotazování, export, jejich import a operace CRUD pro entity
* Podpora ve frontě: zobrazení, přidání, dequeueing zprávy
* Generování identifikátory URI SAS pro účty úložiště
* Připojování k účtům úložiště s identifikátory URI SAS
* Oznámení o aktualizacích pro budoucí aktualizace Průzkumníka úložiště
* Aktualizované vzhled a chování

#### <a name="fixes"></a>Opravy

* Vylepšení výkonu a spolehlivosti

### <a name="known-issues-amp-mitigations"></a>Známé problémy &amp; způsoby zmírnění rizik

* Stahování souborů velkého objektu blob nebude správně fungovat – doporučujeme použít AzCopy, když jsme tento problém vyřešit
* Přihlašovací údaje účtu nebude načíst ani v mezipaměti, pokud se na domovskou složku nelze nalézt nebo nelze zapisovat
* Pokud jsme jsou přidání, úpravy nebo import entita, která má vlastnost s ambiguously číselnou hodnotu, jako je například "1" nebo "1.0", a uživatel se pokusí odeslat jako `Edm.String`, hodnotu, vrátí se znovu prostřednictvím rozhraní API jako Edm.Double klienta
* Při importu souborů CSV pomocí víceřádkovým záznamy, může získat data chopped nebo kódována

02/03/2016

### <a name="version-07201601291"></a>Verze 0.7.20160129.1

#### <a name="fixes"></a>Opravy

* Lepší celkový výkon při odesílání, stahování a kopírování objektů BLOB

01/14/2016

### <a name="version-07201601050"></a>Verze 0.7.20160105.0

#### <a name="new"></a>Nová

* Podpora Linuxu (parity funkcí OSX)
* Přidat kontejnery objektů blob s podpisy sdíleného přístupu (SAS) klíčem.
* Přidat účty úložiště pro Azure Čína
* Přidat účty úložiště s vlastní koncové body
* Otevřít a zobrazit obsah textu a obrázků objektů BLOB
* Zobrazit a upravit vlastnosti objektů blob a metadat

#### <a name="fixes"></a>Opravy

* Pevná: nahrávání nebo stahování velkého počtu objektů BLOB (500 +) může v některých případech způsobit aplikaci bílé obrazovku.
* Opravené: při nastavení úrovně veřejný přístup kontejner objektů blob, nová hodnota se neaktualizuje dokud znovu nenastavíte zaměřuje na kontejneru. Navíc dialogu vždy použije jako výchozí "Žádné veřejný přístup" a ne skutečné aktuální hodnotu.
* Podpora lepší celkový klávesnice nebo usnadnění a uživatelského rozhraní
* Cesta ke stránce Historie text zalomen během procesu je dlouhá s bílými oblasti
* Dialogové okno SAS podporuje ověřování vstupu
* Místní úložiště bude nadále k dispozici i v případě, že vypršela platnost přihlašovací údaje uživatele
* Při odstranění kontejner objektů blob otevřenou Průzkumník objektů blob na pravé straně je uzavřený

#### <a name="known-issues"></a>Známé problémy

* Instalace Linux musí RSZ verze aktualizovat nebo upgradovat – kroky pro upgrade jsou níže:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Verze 0.7.20151116.0

#### <a name="new"></a>Nová

* systému macOS a verze systému Windows
* Přihlaste se k zobrazení účtů úložiště – pomocí účtu organizace, Microsoft Account, 2FA atd.
* Vývoj pro místní úložiště (pomocí emulátoru úložiště pouze pro systém Windows)
* Azure Resource Manager a klasický podporu prostředků
* Vytvářet a odstraňovat objekty BLOB, fronty nebo tabulky
* Vyhledejte konkrétní objekty BLOB, fronty nebo tabulky
* Prozkoumejte obsah kontejnery objektů blob
* Zobrazení a procházení adresářů
* Odesílání, stahování a odstraňování objektů BLOB a složky
* Zobrazit a upravit vlastnosti objektů blob a metadat
* Generovat klíče SAS
* Spravovat a vytvořit uložené zásady přístupu (SAP)
* Hledat objekty BLOB podle předpony
* Přetáhněte n vyřadit souborů k odeslání nebo stažení

#### <a name="known-issues"></a>Známé problémy

* Při nastavení úrovně veřejný přístup kontejner objektů blob, nová hodnota není aktualizován, dokud znovu nenastavíte zaměřuje na kontejneru
* Když otevřete dialogové okno nastavit úroveň veřejný přístup, vždy zobrazuje "Žádné veřejný přístup" jako výchozí a ne skutečné aktuální hodnota
* Nelze přejmenovat stažené objektů BLOB
* Položky protokolu aktivity se někdy "zablokuje" v v průběhu stavu, když dojde k chybě, a není zobrazit chyba
* Někdy dojde k chybě nebo změní zcela bílé při pokusu o odeslání nebo stažení velkého počtu objektů BLOB
* Někdy zrušení operace kopírování nefunguje
* Při vytvoření kontejneru (objektů blob nebo fronty nebo tabulky), pokud vstupní neplatný název a pokračovat ve vytváření jiné v rámci typu jiný kontejner nelze nastavit fokus na nový typ
* Nelze vytvořit novou složku nebo přejmenujte složku




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
