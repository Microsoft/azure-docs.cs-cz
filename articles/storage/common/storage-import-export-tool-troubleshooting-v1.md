---
title: Řešení potíží s nástrojem Azure import/export | Microsoft Docs
description: Seznamte se s některými běžnými problémy, ke kterým dochází při použití nástroje Azure import/export a jak je zpracovat.
author: twooley
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: bc9d338579385001d33669ed06ff71e590571502
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514143"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Řešení potíží s nástrojem Azure pro import/export
Nástroj Microsoft Azure Import/Export vrátí chybové zprávy, pokud dojde k potížím. V tomto tématu jsou uvedeny některé běžné problémy, se kterými se uživatelé můžou spouštět.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Relace kopírování nefunguje, co mám dělat?  
 Pokud dojde k chybě relace kopírování, existují dvě možnosti:  

 Pokud je chyba opakovaným, například pokud byla sdílená složka po krátkou dobu offline a teď je zase online, můžete pokračovat v relaci kopírování. Pokud se chyba nedá opakovat, například pokud jste v parametrech příkazového řádku zadali nesprávný adresář zdrojového souboru, musíte přerušit relaci kopírování. Další informace o obnovení a přerušení relací kopírování najdete v tématu [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md) .  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nejde obnovit nebo přerušit relaci kopírování.  
 Pokud je relace kopírování první relací kopírování pro jednotku, chybová zpráva by měla obsahovat: "první relace kopírování nemůže být obnovena nebo přerušena." V takovém případě můžete odstranit starý soubor deníku a znovu spustit příkaz.  

 Pokud relace kopírování není první pro jednotku, může být vždy obnovena nebo přerušena.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ztratil (a) jsem soubor deníku, můžu i přesto vytvořit úlohu?  
 Soubor deníku pro jednotku obsahuje kompletní informace o kopírování dat na tuto jednotku a je nutné přidat do jednotky další soubory a bude použit k vytvoření úlohy importu. Pokud dojde ke ztrátě souboru deníku, budete muset znovu provést všechny relace kopírování pro jednotku.  

## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje Azure pro import/export](../storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
