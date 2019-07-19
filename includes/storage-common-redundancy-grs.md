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
ms.openlocfilehash: 3aa5310589101fa66fd70cc8d5449fbef80f02fa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286308"
---
Geograficky redundantní úložiště (GRS) je navrženo tak, aby poskytovalo alespoň 99.99999999999999% (16 9) odolnosti objektů v průběhu daného roku tím, že replikuje data do sekundární oblasti, která je od primární oblasti od sebe stovky kilometrů. Pokud je váš účet úložiště GRS povolený, jsou vaše data odolná i v případě kompletního výpadku nebo havárie, ve kterém se primární oblast nedá obnovit.

Pokud se rozhodnete pro GRS, máte k dispozici dvě související možnosti:

* GRS replikuje vaše data do jiného datového centra v sekundární oblasti, ale data jsou k dispozici pro čtení pouze v případě, že společnost Microsoft iniciuje převzetí služeb při selhání z primární do sekundární oblasti. 
* Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) je založené na GRS. RA-GRS replikuje vaše data do jiného datového centra v sekundární oblasti a poskytuje také možnost číst ze sekundární oblasti. Pomocí RA-GRS můžete číst ze sekundární oblasti bez ohledu na to, jestli Microsoft iniciuje převzetí služeb při selhání z primární do sekundární oblasti. 

Pro účet úložiště s povoleným GRS nebo RA-GRS se všechna data nejdřív replikují s místně redundantním úložištěm (LRS). Aktualizace se nejdřív potvrdí do primárního umístění a replikuje se pomocí LRS. Aktualizace se pak asynchronně replikuje do sekundární oblasti pomocí GRS. Když jsou data zapsána do sekundárního umístění, je také replikována v tomto umístění pomocí LRS. 

Primární i sekundární oblasti spravují repliky v různých doménách selhání a upgradovací domény v rámci jednotky škálování úložiště. Jednotka škálování úložiště je základní jednotkou replikace v datacentru. Replikaci na této úrovni poskytuje LRS; Další informace najdete v tématu [místně redundantní úložiště (LRS): Redundance dat pro Azure Storage](../articles/storage/common/storage-redundancy-lrs.md)s nízkými náklady

Při rozhodování o tom, kterou možnost replikace použít, pamatujte na tyto body:

* Zóna – redundantní úložiště (ZRS) poskytuje vysokou dostupnost díky synchronní replikaci a může být lepší volbou pro některé scénáře než GRS nebo RA-GRS. Další informace o ZRS najdete v tématu [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Asynchronní replikace zahrnuje zpoždění od okamžiku, kdy jsou data zapsána do primární oblasti, při replikaci do sekundární oblasti. V případě regionálních havárií může dojít ke ztrátě změn, které ještě nebyly replikovány do sekundární oblasti, pokud tato data nebude možné obnovit z primární oblasti.
* V GRS není tato replika k dispozici pro přístup pro čtení nebo zápis, pokud společnost Microsoft neinicializuje převzetí služeb při selhání sekundární oblastí. V případě převzetí služeb při selhání budete mít k datům přístup pro čtení a zápis po dokončení převzetí služeb při selhání. Další informace najdete v tématu [pokyny pro zotavení po havárii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Pokud vaše aplikace potřebuje číst ze sekundární oblasti, povolte RA-GRS.
