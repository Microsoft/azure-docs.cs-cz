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
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399953"
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

## <a name="read-access-geo-redundant-storage"></a>Geograficky redundantní úložiště jen pro čtení

Geograficky redundantní úložiště jen pro čtení (RA-GRS) maximalizuje dostupnost účtu úložiště. RA-GRS poskytuje přístup jen pro čtení k datům v sekundárním umístění, kromě geografické replikace ve dvou oblastech.

Když povolíte přístup jen pro čtení k datům v sekundární oblasti, vaše data jsou k dispozici na sekundární koncový bod, a také na primární koncový bod vašeho účtu úložiště. Sekundární koncový bod je podobný primárního koncového bodu, ale přidá přípona `–secondary` k názvu účtu. Například, pokud je váš primární koncový bod služby Blob service pro `myaccount.blob.core.windows.net`, pak je sekundární koncový bod `myaccount-secondary.blob.core.windows.net`. Přístupové klíče účtu úložiště jsou stejné pro obě primární a sekundární koncových bodů.

Některé aspekty, mějte na paměti při použití RA-GRS:

* Má vaše aplikace pro správu, který koncový bod je interakce s při použití RA-GRS.
* Vzhledem k tomu, že asynchronní replikace zahrnuje zpoždění, pokud data nejde obnovit z primární oblasti, například v případě regionálního může dojít ke ztrátě změny, které nebyly dosud replikují do sekundární oblasti.
* Čas poslední synchronizace účtu úložiště můžete zkontrolovat. Čas poslední synchronizace je hodnota data a času GMT. Všechny operace zápisu primární před čas poslední synchronizace byla úspěšně zapsána do sekundárního umístění, což znamená, že jsou k dispozici ke čtení ze sekundárního umístění. Primární zapisuje po čas poslední synchronizace může nebo nemusí být k dispozici pro operace čtení ještě. Dotazovat můžete podle této hodnoty [webu Azure portal](https://portal.azure.com/), [prostředí Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), nebo jednu z knihoven klienta služby Azure Storage.
* Pokud Microsoft iniciuje převzetí služeb při selhání do sekundární oblasti, budete mít ke čtení a zápis k těmto datům po převzetí služeb se dokončí. Další informace najdete v tématu [pokyny pro zotavení po havárii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Informace o tom, jak přepnout do sekundární oblasti, naleznete v tématu [co dělat, když dojde k výpadku služby Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS je určená pro účely vysokou dostupnost. Doprovodné materiály škálovatelnosti, najdete v tématu [kontrolní seznam výkonu](../articles/storage/common/storage-performance-checklist.md).
* Návrhy k návrhu pro zajištění vysoké dostupnosti s RA-GRS, naleznete v tématu [navrhování aplikací s vysokou dostupností pomocí RA-GRS úložiště](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co je RPO a RTO s GRS?
**Cíl bodu obnovení (RPO):** GRS a RA-GRS, úložiště služby asynchronně geografickou replikaci dat z primárního do sekundárního umístění. V případě větší havárie regionální v primární oblasti Microsoft provede převzetí služeb při selhání do sekundární oblasti. Pokud dojde k selhání, může dojít ke ztrátě nedávné změny, které dosud nebyly geograficky replikovaný. Počet minut potenciální ztráta dat se označuje jako cíle bodu obnovení a informuje bod v čase, ke kterému můžete data obnovit. Azure Storage obvykle má RPO kratší než 15 minut, ale aktuálně neexistuje žádná smlouva SLA na jak dlouho georeplikace trvá.

**Plánovaná doba obnovení (RTO):** RTO měří dobu potřebnou k provedení převzetí služeb při selhání a získat zpět do online režimu účtu úložiště. Čas k provedení převzetí služeb zahrnuje následující akce:

   * Čas, aby Microsoftu určit, zda data můžete obnovit v primární lokalitě nebo pokud je nutné převzetí služeb při selhání.
   * Čas k provedení převzetí služeb při selhání z účtu úložiště tak, že změníte primární záznamy DNS tak, aby odkazoval na sekundární lokalitě.

   Společnost Microsoft má na starost zachování vašich dat vážně. Pokud je pravděpodobné, že obnovení dat v primární oblasti, Microsoft zpoždění převzetí služeb při selhání a zaměřit se na obnovení dat. Budoucí verze služby vám umožní aktivovat převzetí služeb při selhání na úrovni účtu, takže můžete řídit RTO sami.

## <a name="paired-regions"></a>Spárované oblasti 

Při vytváření účtu úložiště vyberte primární oblast pro účet. Spárované oblasti sekundární závisí na primární oblasti a nelze změnit. Aktuální informace o oblasti, které podporuje Azure najdete v tématu [obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): spárované oblasti Azure](../articles/best-practices-availability-paired-regions.md).
