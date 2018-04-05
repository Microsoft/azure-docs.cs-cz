---
title: Replikace dat v Azure Storage | Microsoft Docs
description: Data ve vašem účtu úložiště Microsoft Azure se replikují pro odolnost a vysokou dostupnost. Možnosti replikace zahrnují místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: bdb9bfaa85f526af0c5e42294a75664fa7137849
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="azure-storage-replication"></a>Účet replikace Azure Storage

Data na vašem účtu Microsoft Azure Storage se vždy replikují, aby byla zajištěna jejich stálost a vysoká dostupnost. Účet replikace Azure Storage zkopíruje data, takže je chráněný před plánovaných a neplánovaných události od výpadku hardwaru, sítě nebo výpadky napájení, masivní přírodní katastrofy a tak dále. Je možné replikovat data ve stejném datovém centru, mezi oblastmi datových centrech v rámci stejné oblasti a dokonce i v jiných oblastech.

Replikace zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Storage](https://azure.microsoft.com/support/legal/sla/storage/) i při selhání. Podívejte se do smlouvy SLA na informace o zárukách služby Azure Storage na stálost a dostupnost.

## <a name="choosing-a-replication-option"></a>Výběrem možnosti replikace

Při vytvoření účtu úložiště si můžete vybrat jednu z těchto možností replikace:

* [Místně redundantní úložiště (LRS)](storage-redundancy-lrs.md)
* [Zónově redundantní úložiště (ZRS)](storage-redundancy-zrs.md)
* [Geograficky redundantní úložiště (GRS)](storage-redundancy-grs.md)
* [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Následující tabulka poskytuje rychlý přehled o oboru odolnosti a dostupnosti, každý strategie replikace získáte pro daný typ události (nebo událostí dopadu na podobné).

| Scénář | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Nedostupnosti uzlu v rámci datového centra |Ano |Ano |Ano |Ano
| Nedostupný celého datového centra (oblastmi nebo jiných oblastmi) |Ne |Ano |Ano |Ano |
| Výpadku celou oblast |Ne |Ne |Ano |Ano |
| Přístup pro čtení k datům (v oblasti vzdáleného, geograficky replikované) v případě nedostupnosti celou oblast |Ne |Ne |Ne |Ano |
| Určená k poskytnutí ___ odolnost objektů během daného roku |alespoň % 99.999999999 (11 společnosti 9)|alespoň % 99.9999999999 (12 na 9)|alespoň % 99.99999999999999 (16 na 9)|alespoň % 99.99999999999999 (16 na 9)|
| K dispozici v ___ typy účtů úložiště |GPv1, GPv2, objektů Blob |GPv2 |GPv1, GPv2, objektů Blob |GPv1, GPv2, objektů Blob

V tématu [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/) o cenách informace o možnostech různých redundance.

> [!NOTE]
> Premium Storage podporuje pouze místně redundantní úložiště (LRS). Informace o Premium Storage najdete v tématu [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Změna strategie replikace
Jsme vám umožňují změnit strategie replikace účtu úložiště pomocí [portál Azure](https://portal.azure.com/), [prostředí Azure Powershell](storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), nebo jednoho z dalších [ Knihovny klienta Azure](https://docs.microsoft.com/en-us/azure/index?view=azure-dotnet#pivot=sdkstools). Změna typu replikace účtu úložiště nevede výpadek.

   > [!NOTE]
   > V současné době nelze použít portál nebo rozhraní API převést svůj účet na ZRS. Ale plánujeme podporu migrace na ZRS z LRS, GRS a RA-GRS po ZRS je všeobecně dostupná. V tématu [Zónově redundantní úložiště (ZRS)](storage-redundancy-zrs.md) podrobnosti.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Existují jakékoli náklady na měnící se strategie replikace Můj účet?
To závisí na cestu k převodu. Z nejlevnější řazení s nabídkou nejnákladnější redundance máme LRS, ZRS, GRS a RA-GRS. Například budete *z* LRS k ničemu bude způsobí nárůst nákladů, protože se chystáte sofistikovanější úrovní redundance. Budete *k* GRS nebo RA-GRS je zpoplatněná poplatek za odchozí šířky pásma, protože data (ve vaší primární oblasti) je právě replikován pro vaši vzdálené sekundární oblast. To je jednorázový poplatek za při počáteční instalaci. Po zkopírování dat nejsou žádné další poplatky převod. Jenom vám bude účtována pro všechny nové replikaci nebo aktualizace stávající data. Podrobnosti o šířku pásma poplatky, najdete v části [Azure Storage – ceny stránky](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pokud změníte na LRS GRS, je bez dalších nákladů, ale replikovaná data se odstraní ze sekundárního umístění.

## <a name="see-also"></a>Další informace najdete v tématech

- [Místně redundantní úložiště (LRS): nízkonákladové redundanci dat pro úložiště Azure](storage-redundancy-lrs.md)
- [Zónově redundantní úložiště (ZRS): aplikace s vysokou dostupností Azure Storage](storage-redundancy-zrs.md)
- [Geograficky redundantní úložiště (GRS): mezi místní replikace pro Azure Storage](storage-redundancy-grs.md)
- [Azure Storage škálovatelnosti a cílech výkonnosti](storage-scalability-targets.md)
- [Návrh aplikace s vysokou dostupností pomocí RA-GRS úložiště](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundance možnosti a přístup pro čtení geograficky redundantní úložiště ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [STUDIE Sosp - Azure Storage: Vysoce dostupný cloudové úložiště služby se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
