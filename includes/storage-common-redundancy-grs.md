---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219847"
---
Geograficky redundantní úložiště (GRS) je navržené pro poskytování 99,99999999999999 % (16. 9) odolnosti objektů v průběhu daného roku díky replikuje vaše data do sekundární oblasti, která je stovky mil od primární oblasti. Pokud má váš účet úložiště GRS povolena, vaše data byla odolná i v případě kompletní oblastnímu výpadku nebo havárii, ve kterém se obnovit primární oblast.

Pokud se rozhodnete pro geograficky redundantní úložiště, máte dvě možnosti související lze vybírat:

* GRS replikuje vaše data do jiného datového centra v sekundární oblasti, ale, že data jsou k dispozici být jen pro čtení. Pokud Microsoft zahájí převzetí služeb při selhání z primární do sekundární oblasti. 
* Geograficky redundantní úložiště jen pro čtení (RA-GRS) je založená na GRS. RA-GRS replikuje vaše data do jiného datového centra v sekundární oblasti a také vám poskytuje možnost číst ze sekundární oblasti. V případě RA-GRS můžou načítat ze sekundární oblasti bez ohledu na to, jestli Microsoft zahájí převzetí služeb při selhání z primární do sekundární oblasti. 

Pro účet úložiště GRS nebo RA-GRS povolené všechna data se replikují nejprve s místně redundantním úložištěm (LRS). Aktualizace se nejprve zaměřuje na primární umístění a replikovat pomocí LRS. Aktualizace se pak asynchronně replikuje do sekundární oblasti pomocí GRS. Data se zapisují do sekundárního umístění, se také replikuje do tohoto umístění použití úložiště LRS. 

Primární a sekundární oblasti spravovat repliky v samostatných doménách selhání a upgradovacími doménami v jednotce škálování úložiště. Jednotka škálování úložiště je jednotka basic replikace v rámci datového centra. Replikace na této úrovni je poskytován LRS; Další informace najdete v tématu [místně redundantní úložiště (LRS): redundanci dat s nízkými náklady pro službu Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Mějte tyto body při rozhodování o možnosti replikace, kterou chcete použít:

* Zónově redundantní úložiště (ZRS) poskytuje vysokou dostupnost díky synchronní replikaci a může být lepší volbou pro některé scénáře než GRS nebo RA-GRS. Další informace o ZÓNOVĚ najdete v tématu [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Asynchronní replikace zahrnuje zpoždění od okamžiku, data se zapisují do primární oblasti do při replikaci do sekundární oblasti. Regionální havárie mohou být změny, které nebyly dosud replikují do sekundární oblasti ztraceny, pokud tato data nejde obnovit z primární oblasti.
* S GRS není k dispozici pro čtení nebo zápis repliky, pokud Microsoft nezahájí převzetí služeb při selhání do sekundární oblasti. V případě selhání budete přečetl(a) a přístup pro zápis k těmto datům po převzetí služeb byla dokončena. Další informace najdete v tématu [pokyny pro zotavení po havárii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Pokud vaše aplikace potřebuje ke čtení ze sekundární oblasti, povolte RA-GRS.