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
ms.openlocfilehash: 922f4d5dd8c71bc9365523b1d539d1b0754fff15
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580935"
---
Geograficky redundantní úložiště (GRS) je navržené pro poskytování 99,99999999999999 % (16. 9) odolnosti objektů v průběhu daného roku díky replikuje vaše data do sekundární oblasti, která je stovky mil od primární oblasti. Pokud má váš účet úložiště GRS povolena, vaše data byla odolná i v případě kompletní oblastnímu výpadku nebo havárii, ve kterém primární oblasti se nedá vrátit zpátky.

Pokud se rozhodnete pro geograficky redundantní úložiště, máte dvě možnosti související lze vybírat:

* GRS replikuje vaše data do jiného datového centra v sekundární oblasti, ale, že data jsou k dispozici být jen pro čtení. Pokud Microsoft zahájí převzetí služeb při selhání z primární do sekundární oblasti. 
* Geograficky redundantní úložiště jen pro čtení (RA-GRS) je založená na GRS. RA-GRS replikuje vaše data do jiného datového centra v sekundární oblasti a také vám poskytuje možnost číst ze sekundární oblasti. V případě RA-GRS můžou načítat ze sekundárního bez ohledu na to, jestli Microsoft zahájí převzetí služeb při selhání z primárního na sekundární. 

Pro účet úložiště GRS nebo RA-GRS povolené všechna data se replikují nejprve s místně redundantním úložištěm (LRS). Aktualizace se nejprve zaměřuje na primární umístění a replikovat pomocí LRS. Aktualizace se pak asynchronně replikuje do sekundární oblasti pomocí GRS. Data se zapisují do sekundárního umístění, se také replikuje do tohoto umístění použití úložiště LRS. 

Primární a sekundární oblasti spravovat repliky v samostatných doménách selhání a upgradovacími doménami v jednotce škálování úložiště. Jednotka škálování úložiště je jednotka basic replikace v rámci datového centra. Replikace na této úrovni je poskytován LRS; Další informace najdete v tématu [místně redundantní úložiště (LRS): redundanci dat s nízkými náklady pro službu Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Mějte tyto body při rozhodování o možnosti replikace, kterou chcete použít:

* Zónově redundantní úložiště (ZRS) poskytuje vysokou dostupnost díky synchronní replikaci a může být lepší volbou pro některé scénáře než GRS nebo RA-GRS. Další informace o ZÓNOVĚ najdete v tématu [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Vzhledem k tomu, že asynchronní replikace zahrnuje zpoždění, v případě regionálního je možné, že změny, které nebyly dosud replikují do sekundární oblasti budou ztraceny, pokud není možné obnovit data z primární oblasti.
* S GRS replika není k dispozici Pokud Microsoft nezahájí převzetí služeb při selhání do sekundární oblasti. Pokud společnosti Microsoft spustit převzetí služeb při selhání do sekundární oblasti, budete mít ke čtení a zápis k těmto datům po převzetí služeb se dokončí. Další informace najdete v tématu [pokyny pro zotavení po havárii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Pokud vaše aplikace potřebuje ke čtení ze sekundární oblasti, povolte RA-GRS.