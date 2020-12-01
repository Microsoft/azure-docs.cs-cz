---
title: Redundance dat v Azure Data Factory | Microsoft Docs
description: Přečtěte si o mechanismech redundance meta-data v Azure Data Factory
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 7f95adc264ed91e75eef668b43f674ddeb7d9e89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350020"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory redundanci dat**

Data Azure Data Factory zahrnují metadata (kanály, datové sady, propojené služby, modul runtime integrace a triggery) a data monitorování (spuštění kanálu, triggeru a aktivit). 

Ve všech oblastech (s výjimkou Brazílie – jih a jihovýchodní Asie) se Azure Data Factory data ukládají a replikují do [spárované oblasti](../best-practices-availability-paired-regions.md#azure-regional-pairs) , aby se mohla chránit před ztrátou metadat. V případě selhání regionálních datových Center může společnost Microsoft zahájit místní převzetí služeb při selhání vaší instance Azure Data Factory. Ve většině případů není nutná žádná akce na vaší straně. Po dokončení převzetí služeb při selhání spravovaného společností Microsoft budete mít přístup k vašemu Azure Data Factory v oblasti převzetí služeb při selhání. 

Z důvodu požadavků na umístění dat v oblasti Brazílie – jih a jihovýchodní Asie se Azure Data Factory data ukládají [pouze v místní oblasti](../storage/common/storage-redundancy.md#locally-redundant-storage). V jihovýchodní Asie se všechna data ukládají v Singapuru. V oblasti Brazílie – jih jsou všechna data uložená v Brazílii. Když dojde ke ztrátě oblasti z důvodu významné havárie, Microsoft nebude moct obnovit data Azure Data Factory.  

> [!NOTE]
> Převzetí služeb při selhání spravované Microsoftem se nevztahuje na místní prostředí Integration runtime (SHIR), protože tato infrastruktura je obvykle spravovaná zákazníkem. Pokud je SHIR nastavený na virtuálním počítači Azure, doporučuje se využít [Azure Site Recovery](../site-recovery/site-recovery-overview.md) ke zpracování [převzetí služeb virtuálního počítače Azure](../site-recovery/azure-to-azure-architecture.md) v jiné oblasti.



## <a name="using-source-control-in-azure-data-factory"></a>**Použití správy zdrojového kódu v Azure Data Factory**

Abyste měli jistotu, že budete moct sledovat a auditovat změny provedené v metadatech Azure Data Factory, měli byste zvážit nastavení správy zdrojového kódu pro Azure Data Factory. Také vám umožní přístup k souborům JSON metadat pro kanály, datové sady, propojené služby a Trigger. Azure Data Factory vám umožní pracovat s jiným úložištěm Git (Azure DevOps a GitHub). 

 Naučte se, jak nastavit [správu zdrojového kódu v Azure Data Factory](./source-control.md). 

> [!NOTE]
> V případě havárie (ztráta oblasti) je možné novou datovou továrnu zřídit ručně nebo automatizovaným způsobem. Po vytvoření nové datové továrny můžete své kanály, datové sady a soubory JSON propojených služeb obnovit z existujícího úložiště Git. 



## <a name="data-stores"></a>**Úložiště dat**

Azure Data Factory vám umožní přesouvat data mezi úložišti dat umístěnými místně a v cloudu. Pro zajištění kontinuity podnikových úložišť dat byste měli pro každé z těchto úložišť dat použít doporučení pro provozní kontinuitu. 

 

## <a name="see-also"></a>Viz také

- [Regionální páry Azure](../best-practices-availability-paired-regions.md)
- [Zasídlí dat v Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)