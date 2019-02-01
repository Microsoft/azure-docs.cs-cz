---
title: Redundanci dat ve službě Azure Storage | Dokumentace Microsoftu
description: Data ve vašem účtu Microsoft Azure Storage se pro zajištění vysoké dostupnosti a odolnosti replikuje. Možnosti redundance zahrnují místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště jen pro čtení (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 0ba36042d02d0b4101f1e80c63af232717bcf4ca
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506783"
---
# <a name="azure-storage-redundancy"></a>Azure redundance úložiště

Data na vašem účtu Microsoft Azure Storage se vždy replikují, aby byla zajištěna jejich stálost a vysoká dostupnost. Úložiště Azure zkopíruje data, takže je chráněný před plánovaných a neplánovaných událostí, včetně krátkodobého selhání hardwaru, sítě nebo výpadky napájení a masivní přírodními pohromami. Můžete replikovat data ve stejném datovém centru, napříč oblastmi datovými centry v rámci stejné oblasti nebo napříč geograficky oddělenými regiony.

Replikace zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Storage](https://azure.microsoft.com/support/legal/sla/storage/) i při selhání. Podívejte se do smlouvy SLA na informace o zárukách služby Azure Storage na stálost a dostupnost.

## <a name="choosing-a-redundancy-option"></a>Pokud vyberete možnost redundance

Když vytvoříte účet úložiště, můžete vybrat jednu z následujících možností redundance:

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
| Navrženo pro zajištění \_ \_ odolnosti objektů v průběhu daného roku                                          | alespoň 99,999999999 % (11 9) | alespoň 99,9999999999 % (12 9) | minimálně 99,99999999999999 % (16. 9) | minimálně 99,99999999999999 % (16. 9) |
| Typy účtů úložiště podporuje                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Smlouva SLA o dostupnosti pro požadavky na čtení | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,99 % (99,9 % pro vrstvu přístupu Cool) |
| Smlouva SLA o dostupnosti pro požadavky na zápis | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) | Minimálně 99,9 % (99 % pro vrstvu přístupu cool) |

Informace o cenách pro jednotlivé možnosti redundance, naleznete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Informace o službě Azure Storage záruky dostupnosti a odolnosti najdete v článku [SLA pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Premium Storage podporuje jen místně redundantní úložiště (LRS). Informace o službě Premium Storage najdete v tématu [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Změna strategie replikace
Můžeme vám umožňují změnit strategie replikace účtu úložiště pomocí [webu Azure portal](https://portal.azure.com/), [prostředí Azure Powershell](storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), nebo jeden mnoho [ Klientské knihovny Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Změna typu replikace účtu úložiště nemá za následek časové prodlevy.

   > [!NOTE]
   > V současné době nelze použít na portálu nebo rozhraní API pro převod účtu na ZRS. Pokud chcete převést svůj účet replikaci ZRS, přečtěte si téma [zónově redundantní úložiště (ZRS)](storage-redundancy-zrs.md) podrobnosti.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Existují všechny náklady na měnící se strategie replikace Můj účet?
To závisí na vaší cestě pro převod. Z nejlevnější řazení nejdražší nabídky redundance máme LRS, ZRS, GRS a RA-GRS. Například, že přejdete *z* LRS k ničemu budou účtovat další poplatky, protože se chystáte sofistikovanější úroveň redundance. Přechod *k* GRS nebo RA-GRS, se budou účtovat poplatek za výstupní šířky pásma vzhledem k tomu, že vaše data (v primární oblasti) je právě replikován pro vaše vzdálené sekundární oblasti. Toto je jednorázový poplatek při počáteční instalaci. Po zkopírování dat neúčtují žádné další poplatky převodu. Vám naúčtujeme vám jenom pro replikaci žádné nové aktualizace nebo aktualizace na existující data. Podrobnosti o poplatky za šířku pásma, naleznete v tématu [stránku s cenami za Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pokud změníte z GRS na LRS, se neúčtují žádné další poplatky, ale replikovaná data se odstraní ze sekundárního umístění.

## <a name="see-also"></a>Další informace najdete v tématech

- [Místně redundantní úložiště (LRS): Redundanci dat s nízkými náklady pro službu Azure Storage](storage-redundancy-lrs.md)
- [Zónově redundantní úložiště (ZRS): Vysoce dostupné aplikace služby Azure Storage](storage-redundancy-zrs.md)
- [Geograficky redundantní úložiště (GRS): Replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md)
- [Azure škálovatelnost a výkonnostní cíle Storage](storage-scalability-targets.md)
- [Navrhování aplikací s vysokou dostupností pomocí RA-GRS úložiště](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundance možnosti a oprávnění ke čtení geograficky redundantní úložiště ](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Dokument SOSP - Azure Storage: Služby s vysokou dostupností cloudového úložiště se silnou konzistenci](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
