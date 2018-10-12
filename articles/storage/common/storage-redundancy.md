---
title: Replikace dat ve službě Azure Storage | Dokumentace Microsoftu
description: Data ve vašem účtu Microsoft Azure Storage se pro zajištění vysoké dostupnosti a odolnosti replikuje. Možnosti replikace zahrnují místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště jen pro čtení (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/08/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: c5cbeed7f689a7ae159d753963c35281b051f632
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114188"
---
# <a name="azure-storage-replication"></a>Účet replikace Azure Storage

Data na vašem účtu Microsoft Azure Storage se vždy replikují, aby byla zajištěna jejich stálost a vysoká dostupnost. Účet replikace Azure Storage zkopíruje data, takže je chráněný před plánovaných a neplánovaných událostí od krátkodobého selhání hardwaru, sítě nebo výpadků napájení, masivní přírodními katastrofami a tak dále. Můžete replikovat data ve stejném datovém centru, mezi oblastmi datových center v rámci stejné oblasti a dokonce i oblastmi.

Replikace zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Storage](https://azure.microsoft.com/support/legal/sla/storage/) i při selhání. Podívejte se do smlouvy SLA na informace o zárukách služby Azure Storage na stálost a dostupnost.

## <a name="choosing-a-replication-option"></a>Pokud vyberete možnost replikace

Při vytvoření účtu úložiště si můžete vybrat jednu z těchto možností replikace:

* [Místně redundantní úložiště (LRS)](storage-redundancy-lrs.md)
* [Zónově redundantní úložiště (ZRS)](storage-redundancy-zrs.md)
* [Geograficky redundantní úložiště (GRS)](storage-redundancy-grs.md)
* [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Následující tabulka poskytuje rychlý přehled o oboru odolnosti a dostupnosti, která každé strategie replikace bude poskytovat pro daný typ událost (nebo podobné dopad na chod firmy).

| Scénář                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Nedostupnost uzlu v rámci datového centra                                                                 | Ano                             | Ano                              | Ano                                  | Ano                                  |
| Nedostupné celého datového centra (zónový ani zónové)                                           | Ne                              | Ano                              | Ano                                  | Ano                                  |
| K výpadku celé oblasti                                                                                     | Ne                              | Ne                               | Ano                                  | Ano                                  |
| Přístup pro čtení k datům (v oblasti vzdálené, geograficky replikovaného) v případě nedostupnosti celé oblasti | Ne                              | Ne                               | Ne                                   | Ano                                  |
| Navrženo pro zajištění ___ odolnosti objektů v průběhu daného roku                                          | alespoň 99,999999999 % (11 9) | alespoň 99,9999999999 % (12 9) | minimálně 99,99999999999999 % (16. 9) | minimálně 99,99999999999999 % (16. 9) |
| Typy účtů úložiště podporuje                                                                   | Objektů Blob v účtech GPv1, GPv2                | Účty GPv2, GPv1 (prostřednictvím poskytovatele prostředí PowerShell, rozhraní příkazového řádku Azure nebo prostředku rozhraní API)                             | Objektů Blob v účtech GPv1, GPv2                     | Objektů Blob v účtech GPv1, GPv2                     |
| Smlouva SLA o dostupnosti pro požadavky na čtení | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,99 % (99,9 % pro vrstvu přístupu Cool) |
| Smlouva SLA o dostupnosti pro požadavky na zápis | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) |

Informace o cenách pro jednotlivé možnosti redundance, naleznete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Informace o službě Azure Storage záruky dostupnosti a odolnosti najdete v článku [SLA pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Premium Storage podporuje jen místně redundantní úložiště (LRS). Informace o službě Premium Storage najdete v tématu [Premium Storage: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Změna strategie replikace
Můžeme vám umožňují změnit strategie replikace účtu úložiště pomocí [webu Azure portal](https://portal.azure.com/), [prostředí Azure Powershell](storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), nebo jeden mnoho [ Klientské knihovny Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Změna typu replikace účtu úložiště nemá za následek časové prodlevy.

   > [!NOTE]
   > V současné době nelze použít na portálu nebo rozhraní API pro převod účtu na ZRS. Pokud chcete převést svůj účet replikaci ZRS, přečtěte si téma [zónově redundantní úložiště (ZRS)](storage-redundancy-zrs.md) podrobnosti.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Existují všechny náklady na měnící se strategie replikace Můj účet?
To závisí na vaší cestě pro převod. Z nejlevnější řazení nejdražší nabídky redundance máme LRS, ZRS, GRS a RA-GRS. Například, že přejdete *z* LRS k ničemu budou účtovat další poplatky, protože se chystáte sofistikovanější úroveň redundance. Přechod *k* GRS nebo RA-GRS, se budou účtovat poplatek za výstupní šířky pásma vzhledem k tomu, že vaše data (v primární oblasti) je právě replikován pro vaše vzdálené sekundární oblasti. Toto je jednorázový poplatek při počáteční instalaci. Po zkopírování dat neúčtují žádné další poplatky převodu. Vám naúčtujeme vám jenom pro replikaci žádné nové aktualizace nebo aktualizace na existující data. Podrobnosti o poplatky za šířku pásma, naleznete v tématu [stránku s cenami za Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pokud změníte z GRS na LRS, se neúčtují žádné další poplatky, ale replikovaná data se odstraní ze sekundárního umístění.

## <a name="see-also"></a>Další informace najdete v tématech

- [Místně redundantní úložiště (LRS): redundanci dat s nízkými náklady pro službu Azure Storage](storage-redundancy-lrs.md)
- [Zónově redundantní úložiště (ZRS): vysoce dostupné aplikace služby Azure Storage](storage-redundancy-zrs.md)
- [Geograficky redundantní úložiště (GRS): replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md)
- [Azure škálovatelnost a výkonnostní cíle Storage](storage-scalability-targets.md)
- [Navrhování aplikací s vysokou dostupností pomocí RA-GRS úložiště](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundance možnosti a oprávnění ke čtení geograficky redundantní úložiště ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Dokument SOSP - Azure Storage: Vysoce dostupné služby cloudového úložiště se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
