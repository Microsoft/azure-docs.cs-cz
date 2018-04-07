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
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
Geograficky redundantní úložiště (GRS) určená k poskytování alespoň % 99.99999999999999 (16 na 9) odolnost objektů během daného roku data replikace do sekundární oblasti, která je stovky miles od primární oblasti. Pokud má váš účet úložiště GRS povoleno, vaše data byla odolná i v případě výpadku dokončení místní nebo havárii, ve kterém není použitelná pro obnovení primární oblasti.

Pokud se přihlásíte pro GRS, máte na výběr dvě související možnosti:

* GRS replikuje data do jiného datového centra v sekundární oblasti, ale, že data jsou dostupná pro být jen pro čtení Pokud Microsoft zahájí převzetí služeb při selhání z primárního na sekundární oblast. 
* Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) je založena na GRS. RA-GRS replikuje data do jiného datového centra v sekundární oblasti a také poskytuje možnost číst z sekundární oblast. S RA-GRS může číst ze sekundární bez ohledu na to, jestli Microsoft zahájí převzetí služeb při selhání z primárního na sekundární. 

Účet úložiště GRS nebo RA-GRS povolené všechna data se replikují nejprve s místně redundantní úložiště (LRS). Aktualizace se nejprve zavazuje do primárního umístění a replikovat pomocí LRS. Aktualizace se pak replikují asynchronně sekundární oblast pomocí GRS. Data se zapisují do sekundárního umístění, se také replikují v tomto umístění pomocí LRS. 

Primární a sekundární oblasti spravovat repliky v rámci domén samostatné selhání a upgradu domén v rámci jednotky škálování úložiště. Jednotka škálování úložiště je jednotka základní replikace v rámci datového centra. Replikace na této úrovni zajišťuje LRS; Další informace najdete v tématu [místně redundantní úložiště (LRS): nízkonákladové redundanci dat pro úložiště Azure](../articles/storage/common/storage-redundancy-lrs.md).

Při výběru typu replikace používat zapamatujte si tyto body:

* Zónově redundantní úložiště (ZRS) poskytuje vysokou dostupnost synchronní replikace a může být v některých případech vhodnější než GRS nebo RA-GRS. Další informace o ZRS, najdete v části [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Protože asynchronní replikaci zahrnuje zpoždění, v případě havárie regionální je možné, že od primární oblasti nelze obnovit data budou ztracena změny, které ještě nebyla replikována do sekundární oblasti.
* S GRS replika není k dispozici Pokud Microsoft zahájí převzetí služeb při selhání pro sekundární oblast. Pokud Microsoft zahájit převzetí služeb při selhání pro sekundární oblast, budete mít ke čtení a zápis do dat po převzetí služeb po dokončení. Další informace najdete v tématu [pokyny pro zotavení po havárii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Pokud aplikace potřebuje ke čtení ze sekundární oblasti, povolte RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Geograficky redundantní úložiště s přístupem pro čtení

Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) maximalizuje dostupnost pro váš účet úložiště. RA-GRS poskytuje přístup jen pro čtení k datům v sekundárním umístění, kromě geografická replikace v rámci dvou oblastí.

Když povolíte přístup jen pro čtení k datům v sekundární oblasti, vaše data jsou k dispozici na sekundárním koncovým bodem, a také na primární koncový bod pro váš účet úložiště. Sekundární koncový bod je podobná primární koncový bod, ale připojí přípona `–secondary` k názvu účtu. Například, pokud je váš primární koncový bod služby objektů Blob `myaccount.blob.core.windows.net`, pak je sekundární koncový bod `myaccount-secondary.blob.core.windows.net`. Přístupové klíče účtu úložiště jsou stejné pro obě primární a sekundární koncové body.

Některé aspekty při používání RA-GRS mějte na paměti:

* Aplikace má ke správě kterému koncovému bodu je interakci s při použití RA-GRS.
* Vzhledem k tomu, že asynchronní replikaci zahrnuje zpoždění, změny, které ještě nebyla replikována do sekundární oblasti mohou být ztraceny, pokud data nelze obnovit z primární oblasti, například v případě místní havárie.
* Můžete zkontrolovat, čas poslední synchronizace účtu úložiště. Čas poslední synchronizace je hodnota, datum a čas GMT. Všechny primární zápisy před čas poslední synchronizace byla úspěšně zapsána do sekundárního umístění, což znamená, že jsou k dispozici čtení ze sekundárního umístění. Primární zapíše po čas poslední synchronizace může nebo nemusí být k dispozici pro čtení ještě. Můžete dotazovat pomocí této hodnoty [portál Azure](https://portal.azure.com/), [prostředí Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), nebo z jednoho z knihovny klienta Azure Storage.
* Pokud Microsoft zahájí převzetí služeb při selhání pro sekundární oblast, budete mít ke čtení a zápis do dat po převzetí služeb po dokončení. Další informace najdete v tématu [pokyny pro zotavení po havárii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Informace o tom, jak přepnout na sekundární oblast najdete v tématu [co dělat, když dojde k výpadku Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS, je určena pro účely vysokou dostupnost. Škálovatelnost pokyny najdete v článku [kontrolní seznam výkonu](../articles/storage/common/storage-performance-checklist.md).
* Doporučení týkající se návrhu pro zajištění vysoké dostupnosti s RA-GRS, najdete v části [navrhování vysoce dostupné aplikace pomocí úložiště RA-GRS](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co je plánovaný bod obnovení a RTO s GRS?
**Cíl bodu obnovení (RPO):** v GRS a RA-GRS, úložiště služby asynchronně geo replikují data z primární na sekundární umístění. V případě významnější havárie regionální v primární oblasti provede Microsoft převzetí služeb při selhání pro sekundární oblast. Pokud dojde převzetí služeb při selhání, nedávné změny, které dosud nebyly geograficky replikované mohou být ztraceny. Počet minut potenciální – ztráta dat se označuje jako plánovaný bod obnovení a znamená bod v čase, do kterých můžete obnovit data. Úložiště Azure obvykle má RPO méně než 15 minut, i když na jak dlouho se geografická replikace je aktuálně žádné SLA trvá.

**Cíli času obnovení (RTO):** RTO se rozumí míra doba potřebná k provedení převzetí a získat zpět do režimu online účet úložiště. Čas k provedení převzetí zahrnuje následující akce:

   * Čas Microsoft vyžaduje k určení, zda lze obnovit data v primární lokalitě nebo pokud převzetí služeb při selhání je nutné.
   * Čas k provedení převzetí služeb při selhání účtu úložiště tak, že změníte primární záznamy DNS tak, aby odkazovaly do sekundárního umístění.

   Společnost Microsoft má je zodpovědností vážně zachování vaše data. Pokud je pravděpodobné, že obnovení dat v primární oblasti, bude společnost Microsoft zpoždění převzetí služeb při selhání a zaměřit se na obnovení dat. Budoucí verze služby vám umožní spustit převzetí služeb při selhání na úrovni účtu, abyste mohli řídit RTO, sami.

## <a name="paired-regions"></a>Spárované oblastí 

Pokud vytvoříte účet úložiště, vyberte primární oblasti pro účet. Spárované sekundární oblast je určen na základě primární oblasti a nelze změnit. Aktuální informace o oblastech podporovaných v Azure najdete v tématu [obchodní kontinuitu a zotavení po havárii (BCDR): spárovat oblasti Azure](../articles/best-practices-availability-paired-regions.md).
