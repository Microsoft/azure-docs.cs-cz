---
title: Co dělat v případě výpadku služby Azure Storage | Dokumentace Microsoftu
description: Co dělat v případě výpadku služby Azure Storage
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 07/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: bca4b13ea2a003ea428351bcff44944630387e1b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528006"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Co dělat v případě výpadku služby Azure Storage
V Microsoftu usilovně pracujeme na Ujistěte se, že naše služby byly vždycky dostupné. V některých případech vynutí nad rámec naší dopad ovládací prvek nám takovým způsobem, který způsobit neplánované výpadky v jedné nebo několika oblastech. Při zpracování těchto výjimečných výskytů, nabízíme následující obecné pokyny pro služby Azure Storage.

## <a name="how-to-prepare"></a>Postup přípravy
Je důležité, aby si každý zákazník připravil vlastní plán zotavení po havárii. Úsilí nezbytné k zotavení z výpadku úložiště obvykle zahrnuje operace pracovníky a automatizované postupy aby bylo možné znovu aktivovat vaše aplikace ve funkčním stavu. Najdete v dokumentaci k Azure a sestavit vlastní plán zotavení po havárii:

* [Kontrolní seznam k dostupnosti](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Návrh odolných aplikací pro Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Služba Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Účet replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Služba Azure Backup](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Jak detekovat
Doporučeným způsobem, jak určit stav služby Azure se přihlásit k odběru [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Co dělat, když dojde k výpadku úložiště
Pokud jeden nebo více služeb úložiště není dočasně k dispozici v jedné nebo několika oblastech, existují dvě možnosti, které byste měli zvážit. Pokud vyžadujete okamžitý přístup k vašim datům, zvažte prosím možnost 2.

### <a name="option-1-wait-for-recovery"></a>Možnost 1: Vyčkat, než obnovení
V takovém případě nemusíte vaší nic dělat. Pilně pracujeme na dostupnosti služby Azure. Můžete monitorovat stav služby na [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Možnost 2: Kopírování dat ze sekundární
Pokud jste zvolili [geograficky redundantní úložiště jen pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (doporučeno) pro vaše účty úložiště, máte přístup pro čtení k datům ze sekundární oblasti. Můžete pomocí nástrojů, jako [AzCopy](storage-use-azcopy.md), [prostředí Azure PowerShell](storage-powershell-guide-full.md)a [knihovna pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) ke kopírování dat ze sekundární oblasti do jiného účtu úložiště do unimpacted oblasti a pak bod vaší aplikace do tohoto úložiště účtu pro obě čtení a zápisu dostupnosti.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Co očekávat, pokud dojde k selhání úložiště
Pokud jste zvolili [geograficky redundantní úložiště (GRS)](storage-redundancy-grs.md) nebo [geograficky redundantní úložiště jen pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (doporučeno), Azure Storage se zachovat data trvalý ve dvou oblastech (primární i sekundární). V obou oblastech služby Azure Storage uchovává neustále víc replik vašich dat.

Když regionálního ovlivní primární oblast, jsme se nejdřív pokusí použít k obnovení služby v dané oblasti. Závislé na povaze po havárii a jeho dopady, v některých výjimečných případech jsme nemusí být schopni obnovit primární oblast. V tomto okamžiku provedeme geo-převzetí služeb při selhání. Replikace dat mezi oblastmi je asynchronní proces, který může zahrnovat zpoždění, takže je možné, že změny, které nebyly dosud replikují do sekundární oblasti mohou být ztraceny. Můžete zadat dotaz ["Čas poslední synchronizace" vašeho účtu úložiště](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) získat podrobné informace o stavu replikace.

Několik bodů týkající se možnosti úložiště geo-převzetí služeb při selhání:

* Úložiště geograficky při selhání se aktivuje pouze týmem služby Azure Storage – není zapotřebí žádná akce zákazníka.
* Existující koncových bodů služby storage pro objekty BLOB, tabulky, fronty a soubory se nezmění po převzetí služeb při selhání; záznam DNS zadaný Microsoft bude potřeba aktualizovat přepnutí z primární oblasti do sekundární oblasti.  Microsoft bude provádět tuto aktualizaci automaticky jako součást procesu geo-převzetí služeb při selhání.
* Před nasazením a během geo-převzetí služeb při selhání nebude mít přístup pro zápis do účtu úložiště kvůli dopadu po havárii, ale můžete nadále číst ze sekundárního Pokud váš účet úložiště byl nakonfigurován jako RA-GRS.
* Když při selhání geo bylo dokončeno a změny DNS rozšíří, bude obnovena pro čtení a zápis do účtu úložiště; To ukazuje na co používá sekundární koncový bod. 
* Všimněte si, že pokud máte GRS nebo RA-GRS nakonfigurovaná pro účet úložiště bude mít přístup pro zápis. 
* Můžete zadat dotaz ["Geografické převzetí služeb při selhání čas poslední" vašeho účtu úložiště](https://msdn.microsoft.com/library/azure/ee460802.aspx) zobrazíte další podrobnosti.
* Po převzetí služeb při selhání váš účet úložiště bude plně funkční, ale ve stavu "omezeného", protože je ve skutečnosti hostované v samostatné oblasti s možným žádné geografické replikace. Ke zmírnění tohoto rizika, budeme obnovit původní primární oblasti a pak proveďte geo-failback k obnovení původního stavu. Pokud původní primární oblast Neopravitelná, jsme se přidělí jiné sekundární oblasti.
  Podrobné informace o infrastruktuře geografickou replikaci Azure Storage najdete v článku na blogu týmu úložiště o [možnostech redundance a RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Osvědčené postupy pro ochranu vašich dat.
Existují některé doporučené postupy pro zálohování úložiště dat v pravidelných intervalech.

* Disky virtuálního počítače – používají [služby Azure Backup](https://azure.microsoft.com/services/backup/) zálohování disků virtuálních počítačů používají virtuální počítače Azure.
* Objekty BLOB bloku – zapnout [obnovitelné odstranění](../blobs/storage-blob-soft-delete.md) k ochraně proti odstranění na úrovni objektů a přepíše nebo do jiného účtu úložiště v jiné oblasti pomocí zkopírovat objekty BLOB [AzCopy](storage-use-azcopy.md), [Azure Prostředí PowerShell](storage-powershell-guide-full.md), nebo [knihovna pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Tabulky – používají [AzCopy](storage-use-azcopy.md) export tabulkových dat do jiného účtu úložiště v jiné oblasti.
* Použít soubory – [AzCopy](storage-use-azcopy.md) nebo [prostředí Azure PowerShell](storage-powershell-guide-full.md) kopírování souborů do jiného účtu úložiště v jiné oblasti.

Informace o vytváření aplikací, které využívají všech výhod funkce RA-GRS najdete [navrhování aplikací s vysokou dostupností pomocí RA-GRS úložiště](../storage-designing-ha-apps-with-ragrs.md)
