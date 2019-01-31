---
title: Řešení potíží s nástrojem Azure Import/Export | Dokumentace Microsoftu
description: Přečtěte si o některých běžných problémů zobrazit při použití nástroje Import/Export Azure a způsob jejich zpracování.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471908"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Řešení potíží s nástrojem Azure pro import/export
Nástroje Microsoft Azure Import/Export vrátí chybové zprávy, pokud běží na problémy. Toto téma uvádí některé běžné problémy, které uživatelé můžou spouštět na.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Relace kopírování selže, co mám dělat?  
 Při kopírování relace se nezdaří, existují dvě možnosti:  
  
 Pokud je chyba opakovatelná, například pokud síťové sdílené položce byla ve stavu offline na krátkou dobu a nyní je zpátky do online režimu, můžete obnovit kopii relace. Pokud chyba není opakovatelná, například pokud jste zadali nesprávný zdrojový adresář souboru v parametrech příkazového řádku, budete muset přerušení relace kopírování. Zobrazit [příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md) Další informace o obnovení a přerušení relace kopírování.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nejde pokračovat nebo přerušení relace kopírování.  
 Když je relace kopie prvního kopírování relace pro jednotku, by měl uvést chybová zpráva: "Prvního kopírování relace se nedá obnovit nebo přerušena." V takovém případě můžete odstranit původní soubor deníku a spusťte příkaz znovu.  
  
 Pokud relaci kopie není první z nich pro jednotku, můžete být vždy obnovit nebo přerušena.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Můžu ztráty soubor deníku, můžu přesto vytvářet úlohy?  
 Soubor deníku pro disk obsahuje kompletní informace o kopírování dat na této jednotce, a je potřeba přidat další soubory k jednotce se použijí k vytvoření úlohy importu. Pokud soubor deníku dojde ke ztrátě, budete muset znovu provést všechny kopie relace pro jednotku.  
  
## <a name="next-steps"></a>Další postup
 
* [Nastavení nástroje azure import/export](../storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
