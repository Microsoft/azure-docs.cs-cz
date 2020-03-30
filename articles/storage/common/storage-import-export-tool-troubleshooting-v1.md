---
title: Poradce při potížích s nástrojem pro import a export Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o některých běžných problémech, které se vyskytují při používání nástroje Pro import a export Azure a jak je zpracovat.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978406"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Řešení potíží s nástrojem Azure pro import/export
Nástroj import/export Microsoft Azure vrátí chybové zprávy, pokud se naskytne problémům. Toto téma uvádí některé běžné problémy, které uživatelé mohou narazit.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Relace kopírování se nezdaří, co mám dělat?  
 Pokud se relace kopírování nezdaří, existují dvě možnosti:  

 Pokud je chyba opakovatelná, například pokud byla sdílená síťová sdílená síťová sdílená síťová sdílená síťová na krátkou dobu offline a nyní je opět online, můžete pokračovat v relaci kopírování. Pokud není chyba opakovatelná, například pokud jste v parametrech příkazového řádku zadali nesprávný adresář zdrojového souboru, je třeba přerušit relaci kopírování. Další informace o obnovení a přerušení relací kopírování naleznete v [tématu Příprava pevných disků pro úlohu importu.](../storage-import-export-tool-preparing-hard-drives-import-v1.md)  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nelze obnovit nebo přerušit relaci kopírování  
 Pokud je relace kopírování první kopií relace pro jednotku, pak by měla být chybová zpráva: "První relace kopírování nelze obnovit nebo přerušit." V takovém případě můžete odstranit starý soubor deníku a znovu spustit příkaz.  

 Pokud relace kopírování není první pro jednotku, může být vždy obnovena nebo přerušena.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ztratil(a) jsem soubor deníku, mohu stále vytvořit práci?  
 Soubor deníku pro jednotku obsahuje úplné informace o kopírování dat na tuto jednotku a je nutné přidat další soubory na jednotku a bude použit k vytvoření úlohy importu. Pokud dojde ke ztrátě souboru deníku, budete muset znovu provést všechny relace kopírování jednotky.  

## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje pro import a export azurové](../storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
