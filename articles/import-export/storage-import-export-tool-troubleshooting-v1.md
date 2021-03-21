---
title: Řešení potíží s importem a exportem v Azure import/export | Microsoft Docs
description: Naučte se řešit běžné problémy při použití importu a exportu Azure.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706299"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Řešení potíží v Azure import/export
Tento článek popisuje, jak řešit běžné problémy při importu a exportu dat v Azure import/export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Relace kopírování se nezdařila. Co mám dělat?  

Pokud se relace kopírování nezdařila, máte dvě možnosti:  
* Pokud se chyba může opakovat – například pokud byla sdílená složka po krátkou dobu offline a teď je znovu online – můžete pokračovat v relaci kopírování.
* Pokud se chyba nedá opakovat, například pokud jste v parametrech příkazového řádku zadali nesprávný adresář zdrojového souboru, musíte přerušit relaci kopírování.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nejde obnovit nebo přerušit relaci kopírování.

Pokud je relace kopírování první relací kopírování pro jednotku, chybová zpráva by měla obsahovat: "první relace kopírování nemůže být obnovena nebo přerušena." V takovém případě můžete odstranit starý soubor deníku a znovu spustit příkaz.  

Pokud relace kopírování není první pro jednotku, může být relace vždy obnovena nebo přerušena.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ztratil (a) jsem soubor deníku. Můžu I přesto vytvořit úlohu?

Soubor deníku pro jednotku obsahuje úplné informace o relaci z kopie dat. Když do jednotky přidáte soubory, použije se k vytvoření úlohy importu soubor deníku. Pokud soubor deníku ztratíte, budete muset znovu provést všechny relace kopírování pro jednotku.

## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje Azure pro import/export](storage-import-export-tool-setup-v1.md)
* [Příprava pevných disků pro úlohu importu](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Kontrola stavu úlohy pomocí kopírování souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)
* [Opravit úlohu importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](storage-import-export-tool-repairing-an-export-job-v1.md)