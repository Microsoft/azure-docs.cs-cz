---
title: Použití Azure Data Factory k migraci dat ze služby Data Lake a datového skladu do Azure | Microsoft Docs
description: Pomocí Azure Data Factory migrujte data ze svých datových Lake a datových skladů do Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 780b9ae6e4664af86fa655c9136193bed58526d9
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708495"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure 

Azure Data Factory může být nástrojem k migraci dat, když chcete migrovat datový sklad Data Lake nebo Enterprise Data Warehouse (podnikového) do Azure. Migrace Data Lake a datového skladu se týká následujících scénářů: 

- Migrace úlohy s velkým objemem dat z AWS S3, Prem systému souborů Hadoop do Azure. 
- PODNIKOVÉHO migraci z Oracle Exadata, Netezza, Teradata, AWS RedShift do Azure. 

Azure Data Factory může přesunout úroveň dat služby PBs pro migraci Data Lake a desítky TB dat pro migraci datového skladu. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Proč se dá Azure Data Factory použít k migraci dat 

- Azure Data Factory může snadno škálovat množství přístupnosti pro pohyb dat bez serveru s vysokým výkonem, odolností a škálovatelností a platit jenom za to, co využijete.  
  - Azure Data Factory nemá žádné omezení na objem dat a počet souborů.
  - Azure Data Factory může 100% využití šířky pásma sítě a úložiště k dosažení nejvyšší propustnosti přesunu dat ve vašem prostředí.   
  - Azure Data Factory řídí strategii průběžných plateb, takže budete muset platit jenom za čas, kdy používáte Azure Data Factory k migraci dat do Azure.  
- Azure Data Factory má schopnost provést jednorázové historické zatížení i plánované přírůstkové zatížení. 
- Azure Data Factory používá Azure IR k přesouvání dat mezi veřejně přístupnými koncovými body Data Lake/Warehouse nebo pro přesun dat pro koncové body Data Lake/Warehouse v rámci virtuální sítě nebo za bránou firewall případně používá místní prostředí IR. 
- Azure Data Factory má zabezpečení na podnikové úrovni: buď použijte MSI nebo identitu služby pro zabezpečenou integraci služeb, nebo můžete využít Azure Key Vault pro správu přihlašovacích údajů. 
- Azure Data Factory poskytuje prostředí pro vytváření obsahu bez kódu a bohatě integrovaný řídicí panel monitorování.  

## <a name="online-vs-offline-data-migration"></a>Online vs. migrace offline dat

Azure Data Factory je typický Nástroj pro migraci online dat pro přenos dat přes síť (Internet, ER nebo VPN), kde offline migrace dat umožňuje lidem fyzicky dodávat zařízení pro přenos dat z vaší organizace do datového centra Azure.  

Při výběru režimu online a offline migrace je potřeba mít tři důležité důležité skutečnosti:  

- Velikost dat, která se mají migrovat 
- Šířka pásma sítě. 
- Okno migrace   

Pokud chcete dokončit migraci dat během dvou týdnů (migrační okno), můžete na obrázku níže zobrazit vyjmutý řádek, který ukazuje, jestli je vhodné používat online Azure Data Factory migraci, a to s různou velikostí dat a šířkou pásma sítě.   

![online vs. offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> Výhodou přístupu k online migraci je, že můžete dosáhnout historických dat a přírůstkových informačních kanálů, které končí jedním nástrojem.  Díky tomu mohou být data synchronizována mezi existujícím a novým úložištěm během celého okna migrace, abyste mohli znovu sestavit logiku ETL v novém úložišti s aktualizovanými daty. 


## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)