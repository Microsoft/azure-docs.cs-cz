---
title: Mapování struktury složek na Synchronizace souborů Azure topologii
description: Namapujte existující strukturu souborů a složek na sdílené složky Azure pro použití s Synchronizace souborů Azure. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547542"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Přepínač              | Význam |
|---------------------|---------|
| /MT                 | Umožňuje nástroji Robocopy spustit vícevláknové procesy. Výchozí hodnota je 8 a maximální hodnota je 128. Porovnává tuto hodnotu s počtem jader procesoru a počtem vláken na jádro. Zvažte, jestli je potřeba rezervovat jádra pro jiné úkoly, které může mít provozní server. |
| /NP                 | Průběh kopírování pro každý soubor a složku se nezobrazí. Zobrazení průběhu významně snižuje výkon kopírování. |
| /NFL                | Určuje, že se neprotokolují názvy souborů. Zlepšuje výkon kopírování. |
| /NDL                | Určuje, že se neprotokolují názvy adresářů. Zlepšuje výkon kopírování. |
| /B                  | Spustí příkaz Robocopy ve stejném režimu, který používá zálohovací aplikace. Umožňuje nástroji Robocopy přesunout soubory, ke kterým nemá aktuální uživatel oprávnění. |
| /MIR                | *Zdroj zrcadlení k cíli* umožňuje nástroji Robocopy kopírovat pouze rozdíly mezi zdrojem a cílem. Prázdné podadresáře budou zkopírovány. Položky (soubory nebo složky), které se změnily nebo neexistují na cíli, budou zkopírovány. Položky, které existují na cíli, ale ne na zdroji, budou vymazány (odstraněny) z cíle. Při použití tohoto přepínače je naprosto nutné, abyste přesně odpovídali struktuře zdrojové a cílové složky. "Matching" znamená, že zkopírujete ze správné úrovně zdroje a složky do odpovídající úrovně složky v cíli. Jenom pak může být kopie "zachytit výše" úspěšná. Pokud se neshodují zdroje a cíle, použití `/MIR` bude mít za následek velký rozsah odstranění a překopírování. |
| /IT                 | Zajistí, aby se v některých scénářích zrcadlení zachovala přesnost. </br>*Příklad* : Pokud mezi dvě Robocopy spouští soubor, dojde ke změně seznamu ACL a aktualizace atributu: instance je označená jako *Skrytá*. Bez/IT může být Změna seznamu řízení přístupu vynechána v nástroji Robocopy a nepředána do cílového umístění. |
|Kopií`[copyflags]`  | Přesnost kopírování souborů (výchozí Pokud není zadaná `/COPY:DAT` ), příznaky kopírování: `D` = data, `A` = atributy, `T` = Časová razítka, = `S` Security = NTFS ACLs, `O` = informace o vlastníkovi, `U` = A<u>u</u>diting informace. Informace o auditování nejde ukládat do sdílené složky Azure. |
| /DCOPY:`[copyflags]`| Věrnost pro kopii adresářů (výchozí Pokud není zadána `/DCOPY:DA` ), příznaky kopírování: `D` = data, `A` = atributy, `T` = Časová razítka. |
| /UNILOG:<file name> | Výstupuje stav do souboru protokolu jako UNICODE (přepíše existující protokol). |
| /LFSM               | **jenom pro cíle s vrstveném úložištěm** </br>Použití/LFSM žádá Robocopy, aby fungovaly v režimu nízkého volného místa. Tento přepínač je vhodný jenom pro cíle s vrstveném úložištěm, které můžou po dokončení nástroje Robocopy vycházet z místní kapacity. Tento přepínač se přidal konkrétně pro použití s povoleným cílem Synchronizace souborů Azureho cloudového vrstvení. Dá se použít nezávisle na Synchronizace souborů Azure. V tomto režimu bude program Robocopy pozastavit pokaždé, když kopírování souborů způsobí, že volné místo na cílovém svazku bude nižší než hodnota "Floor". Tuto hodnotu lze určit pomocí `/LFSM:n` formuláře příznaku. Parametr `n` je zadán v základní třídě 2: `nKB` , `nMB` nebo `nGB` . Pokud `/LFSM` je zadaný bez explicitní hodnoty Floor, nastaví se patra na 10 procent velikosti cílového svazku. Režim nedostatku volného místa je nekompatibilní s/MT,/EFSRAW,/B a/ZB.. |
| /Z                  | **pečlivé použití** </br>Kopíruje soubory v režimu restartování. použití se doporučuje pouze v nestabilním síťovém prostředí. Tato možnost významně snižuje výkon při kopírování z důvodu dodatečného protokolování. |
| /ZB                 | **pečlivé použití** </br>Používá režim restartování. Pokud je přístup odepřený, tato možnost používá režim zálohování. Tato možnost významně snižuje výkon při kopírování z důvodu kontrolního bodu. |
   