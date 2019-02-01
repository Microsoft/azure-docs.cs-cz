---
title: Geograficky redundantní úložiště (GRS) pro odolnost napříč zónami ve službě Azure Storage | Dokumentace Microsoftu
description: Geograficky redundantní úložiště (GRS) replikuje vaše data mezi dvěma oblastmi, které jsou stovky mil. GRS chrání před selháním hardwaru v datovém centru, stejně jako místní jiného problému ovlivňujícího.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: 2dc409743ce94ecb73e351b839a5a2fb09eadab2
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512097"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Geograficky redundantní úložiště (GRS): Replikace mezi zónami pro službu Azure Storage
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Geograficky redundantní úložiště jen pro čtení
Geograficky redundantní úložiště jen pro čtení (RA-GRS) maximalizuje dostupnost účtu úložiště. RA-GRS poskytuje přístup jen pro čtení k datům v sekundárním umístění, kromě geografické replikace ve dvou oblastech.

Když povolíte přístup jen pro čtení k datům v sekundární oblasti, vaše data jsou k dispozici na sekundární koncový bod, a také na primární koncový bod vašeho účtu úložiště. Sekundární koncový bod je podobný primárního koncového bodu, ale přidá přípona `–secondary` k názvu účtu. Například, pokud je váš primární koncový bod služby Blob service pro `myaccount.blob.core.windows.net`, pak je sekundární koncový bod `myaccount-secondary.blob.core.windows.net`. Přístupové klíče účtu úložiště jsou stejné pro obě primární a sekundární koncových bodů.

Některé aspekty, mějte na paměti při použití RA-GRS:

* Má vaše aplikace pro správu, který koncový bod je interakce s při použití RA-GRS.
* Vzhledem k tomu, že asynchronní replikace zahrnuje zpoždění, změny, které nebyly dosud replikují do sekundární oblasti mohou být ztraceny, pokud není možné obnovit data z primární oblasti.
* Čas poslední synchronizace účtu úložiště můžete zkontrolovat. Čas poslední synchronizace je hodnota data a času GMT. Všechny operace zápisu primární před čas poslední synchronizace byla úspěšně zapsána do sekundárního umístění, což znamená, že jsou k dispozici ke čtení ze sekundárního umístění. Primární zapisuje po čas poslední synchronizace může nebo nemusí být k dispozici pro operace čtení ještě. Dotazovat můžete podle této hodnoty [webu Azure portal](https://portal.azure.com/), [prostředí Azure PowerShell](storage-powershell-guide-full.md), nebo jednu z knihoven klienta služby Azure Storage.
* Pokud spustíte funkci účtu převzetí služeb při selhání (preview) účtu GRS nebo RA-GRS do sekundární oblasti, k zápisu do tohoto účtu se obnoví po dokončení převzetí služeb při selhání. Další informace najdete v tématu [po havárii pro obnovení a úložiště účtu převzetí služeb při selhání (preview)](storage-disaster-recovery-guidance.md).
* RA-GRS je určená pro účely vysokou dostupnost. Doprovodné materiály škálovatelnosti, najdete v tématu [kontrolní seznam výkonu](storage-performance-checklist.md).
* Návrhy k návrhu pro zajištění vysoké dostupnosti s RA-GRS, naleznete v tématu [navrhování aplikací s vysokou dostupností pomocí RA-GRS úložiště](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co je RPO a RTO s GRS?

**Cíl bodu obnovení (RPO):** GRS a RA-GRS služeb úložiště asynchronně geografickou replikaci dat z primárního do sekundárního umístění. V případě, že primární oblast stane nedostupnou, můžete provádět účtu převzetí služeb při selhání (preview) do sekundární oblasti. Při zahájení převzetí služeb při selhání může dojít ke ztrátě nedávné změny, které dosud nebyly geograficky replikovaný. Počet minut, ke které došlo ke ztrátě dat. se označuje jako cíle bodu obnovení. Cíle bodu obnovení Určuje bod v čase, ke kterému můžete data obnovit. Azure Storage obvykle má RPO kratší než 15 minut, ale aktuálně neexistuje žádná smlouva SLA na jak dlouho georeplikace trvá.

**Plánovaná doba obnovení (RTO):** RTO měří dobu potřebnou k provedení převzetí služeb při selhání a získat zpět do online režimu účtu úložiště. Čas k provedení převzetí služeb zahrnuje následující akce:

   * Druhém, dokud zákazník iniciuje převzetí služeb při selhání účet úložiště z primární do sekundární oblasti.
   * Doba, kterou Azure provádět převzetí služeb při selhání tak, že změníte primární záznamy DNS tak, aby odkazoval na sekundární lokalitě.

## <a name="paired-regions"></a>Spárované oblasti 
Při vytváření účtu úložiště vyberte primární oblast pro účet. Spárované oblasti sekundární závisí na primární oblasti a nelze změnit. Aktuální informace o oblasti, které podporuje Azure najdete v tématu [obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): Spárovaných oblastech Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Další informace najdete v tématech
- [Účet replikace Azure Storage](storage-redundancy.md)
- [Místně redundantní úložiště (LRS): Redundanci dat s nízkými náklady pro službu Azure Storage](storage-redundancy-lrs.md)
- [Zónově redundantní úložiště (ZRS): Vysoce dostupné aplikace služby Azure Storage](storage-redundancy-zrs.md)
