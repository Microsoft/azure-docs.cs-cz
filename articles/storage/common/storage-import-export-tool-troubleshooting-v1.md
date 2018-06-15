---
title: Řešení potíží s nástroj Azure Import/Export | Microsoft Docs
description: Další informace o některé běžné problémy, proto při použití nástroje Azure Import/Export a postupy pro jejich zpracování.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873692"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Řešení potíží s nástrojem Azure pro import/export
Nástroj Microsoft Azure Import/Export vrací chybové zprávy, pokud běží na problémy. Toto téma uvádí některé běžné problémy, které mohou uživatelé do.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Relaci kopie selže, co mám udělat?  
 Při kopírování relace selže, existují dvě možnosti:  
  
 Pokud je chyba opakovatelná, například pokud sdílené síťové složce byla offline na krátkou dobu a nyní je zpět do režimu online, můžete obnovit kopii relace. Pokud chyba není opakovatelná, například pokud jste zadali nesprávný zdrojový adresář souboru v parametrech příkazového řádku, musíte k přerušení relace kopírování. V tématu [Příprava pevné disky pro úlohy importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md) Další informace o obnovení a přerušení kopírování relací.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nelze obnovit nebo k přerušení relace kopírování.  
 Pokud relace kopírování se má první relace kopie pro jednotku, pak by měl stavu chybová zpráva: "má první relace kopírování se nedá obnovit nebo přerušena." V takovém případě můžete odstranit původní soubor deníku a spusťte příkaz znovu.  
  
 Pokud relaci kopie není první z nich pro jednotku, může být vždy obnovení nebo přerušena.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>I ztráty deník souboru, je možné stále vytvořit úlohu?  
 Soubor deníku pro jednotku obsahuje kompletní informace o kopírování dat na tuto jednotku, a je potřeba přidat další soubory na disk a budou použity k vytvoření úlohy importu. Pokud soubor deníku dojde ke ztrátě, budete muset znovu provést všechny kopie relace pro jednotku.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje azure import/export](../storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
