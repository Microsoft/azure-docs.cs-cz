---
title: Co dělat v případě výpadku Azure Storage | Microsoft Docs
description: Co dělat v případě výpadku Azure Storage
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: 3c313025917bba06675d3b2d844a6740fab89fbc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Co dělat v případě výpadku služby Azure Storage
Ve společnosti Microsoft můžeme fungovat pevného a ujistěte se, že jsou vždy k dispozici našich služeb. V některých případech vynutí nad rámec naše řízení dopad nám způsoby, které způsobit neplánované výpadky v jedné nebo více oblastech. Chcete-li zpracovat tyto výjimečných výskytů, poskytujeme vysoké úrovně pokynů pro služby Azure Storage.

## <a name="how-to-prepare"></a>Postup přípravy
Je důležité, aby si každý zákazník připravil vlastní plán zotavení po havárii. Úsilí o obnovení z úložiště výpadku obvykle zahrnuje operace pracovníky a automatické postupy Chcete-li znovu aktivovat aplikace ve stavu, funkční. Naleznete v dokumentaci k Azure k vytvoření vlastního plánu zotavení po havárii níže:

* [Kontrolní seznam k dostupnosti](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Návrh odolných aplikací pro Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Služba Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Účet replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Služba Azure Backup](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>K zjištění
Doporučeným způsobem, jak určit stav služby Azure se přihlásit k odběru [řídicím panelu stavu služeb Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Co dělat, pokud dojde k výpadku úložiště
Pokud jeden nebo více služeb úložiště nejsou dostupné na jeden nebo více oblastí, existují dvě možnosti, které je třeba zvážit. Pokud vyžadujete okamžitý přístup k datům, zvažte možnost 2.

### <a name="option-1-wait-for-recovery"></a>Možnost 1: Počkejte obnovení
V takovém případě není třeba žádné akce z vaší strany. Pracujeme usilovně obnovit dostupnost služby Azure. Stav služby můžete sledovat na [řídicím panelu stavu služeb Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Možnost 2: Kopírování dat z sekundární
Pokud jste zvolili [geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (doporučeno) pro účty úložiště, máte přístup pro čtení k datům ze sekundární oblasti. Můžete použít nástroje, jako [AzCopy](storage-use-azcopy.md), [prostředí Azure PowerShell](storage-powershell-guide-full.md)a [knihovna pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) ke zkopírování dat z oblasti sekundární do jiný účet úložiště v unimpacted oblasti, a pak bodu aplikace k tomuto účtu úložiště pro čtení i zápisu dostupnosti.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Co můžete očekávat, pokud dojde k selhání úložiště
Pokud jste zvolili [geograficky redundantní úložiště (GRS)](storage-redundancy-grs.md) nebo [geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (doporučeno), Azure Storage bude uchovávat data odolná v dvou oblastí (primární i sekundární). V obou oblastí Azure Storage neustále udržuje víc replik vaše data.

Při místní po havárii ovlivní primární oblasti, jsme se nejdřív pokusí použít k obnovení služby v této oblasti. Závisí na povaze po havárii a jeho dopadů, v některých výjimečných případech jsme nemusí být schopni obnovit primární oblast. V tomto bodě provedeme geo-převzetí služeb při selhání. Replikace dat mezi oblastmi je asynchronní proces, který zahrnuje zpoždění, takže je možné, že změny, které ještě nebyla replikována do sekundární oblasti mohou být ztraceny. Můžete zadat dotaz ["Čas poslední synchronizace" svého účtu úložiště](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) získat podrobné informace o stavu replikace.

Několik bodů týkající se možnosti geo-převzetí služeb při selhání úložiště:

* Úložiště geo převzetí aktivuje pouze tým Azure Storage – není vyžadována žádná akce zákazníka.
* Existující koncových bodů služby úložiště pro objekty BLOB, tabulky, fronty a soubory zůstane stejný po převzetí služeb při selhání; položku DNS dodané společností Microsoft bude potřeba aktualizovat přepnutí z primární oblasti sekundární oblast.  Microsoft vás provede tuto aktualizaci automaticky v rámci procesu geo-převzetí služeb při selhání.
* Před a během geo selhání nebudete mít přístup pro zápis na váš účet úložiště z důvodu dopad po havárii ale můžete pořád číst ze sekundární Pokud váš účet úložiště byl nakonfigurován jako RA-GRS.
* Po dokončení geo převzetí a rozšíří změny DNS, bude pokračovat oprávnění ke čtení a zápis k vašemu účtu úložiště; To ukazuje na co se používá jako sekundární koncový bod. 
* Všimněte si, že pokud máte GRS nebo RA-GRS nakonfigurované pro účet úložiště bude mít přístup pro zápis. 
* Můžete zadat dotaz ["Geograficky převzetí služeb při selhání čas poslední" svého účtu úložiště](https://msdn.microsoft.com/library/azure/ee460802.aspx) k získání dalších podrobností.
* Po převzetí služeb při selhání váš účet úložiště bude být plně funkční, ale stav "sníženou", protože je ve skutečnosti hostované v samostatné oblasti s žádné možné geografická replikace. Chcete-li toto riziko snížilo, jsme se obnovit původní primární oblasti a poté proveďte geo-navrácení služeb po obnovení obnovit původní stav. Pokud je původní primární oblasti Neopravitelná, jsme se přidělit jiné sekundární oblasti.
  Další informace o infrastrukturu geografická replikace Azure Storage, naleznete v článku na blogu týmu úložiště o [možnosti redundance a RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Osvědčené postupy pro ochranu dat
Existují některé doporučené přístupy k zálohování úložiště dat v pravidelných intervalech.

* Disky virtuálního počítače – používají [služby Azure Backup](https://azure.microsoft.com/services/backup/) zálohovat disky virtuálních počítačů používá virtuální počítače Azure.
* Objekty BLOB bloku – vytvořit [snímku](https://msdn.microsoft.com/library/azure/hh488361.aspx) každý objekt blob bloku nebo zkopírujte objekty BLOB na jiný účet úložiště v jiné oblasti pomocí [AzCopy](storage-use-azcopy.md), [prostředí Azure PowerShell](storage-powershell-guide-full.md), nebo [knihovna pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Pomocí tabulky – [AzCopy](storage-use-azcopy.md) Export tabulky dat na jiný účet úložiště v jiné oblasti.
* Použít soubory – [AzCopy](storage-use-azcopy.md) nebo [prostředí Azure PowerShell](storage-powershell-guide-full.md) kopírování souborů na jiný účet úložiště v jiné oblasti.

Informace o vytváření aplikací, které plně využít výhod funkce RA-GRS, najdete v článku [navrhování vysoce dostupné aplikace pomocí RA-GRS úložiště](../storage-designing-ha-apps-with-ragrs.md)

