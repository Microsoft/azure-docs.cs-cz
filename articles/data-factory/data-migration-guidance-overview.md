---
title: Migrace dat ze služby Data Lake a Data Warehouse do Azure
description: Pomocí Azure Data Factory migrujte data ze svých datových Lake a datových skladů do Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367703"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pokud chcete migrovat data Lake nebo Podnikový datový sklad (podnikového) na Microsoft Azure, zvažte použití Azure Data Factory. Azure Data Factory je vhodným způsobem pro následující scénáře:

- Migrace úlohy s velkým objemem dat ze služby Amazon Simple Storage Service (Amazon S3) nebo místní Hadoop systém souborů DFS (Distributed File System) (HDFS) do Azure
- Migrace podnikového z Oracle Exadata, Netezza, Teradata nebo Amazon RedShift do Azure

Azure Data Factory může přesunout petabajty (PB) dat pro migraci Data Lake a desítky terabajtů (TB) dat pro migraci datového skladu.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Proč se dá Azure Data Factory použít k migraci dat

- Azure Data Factory může snadno škálovat množství výkonu zpracování, aby bylo možné přesouvat data bez serveru s vysokým výkonem, odolností a škálovatelností. A platíte jenom za to, co využijete. Všimněte si také následujícího: 
  - Azure Data Factory nemá žádná omezení na datový svazek ani na počet souborů.
  - Azure Data Factory může plně využít vaši síť a šířku pásma úložiště k dosažení nejvyššího objemu propustnosti přesunu dat ve vašem prostředí.
  - Azure Data Factory využívá metodu s průběžnými platbami, takže platíte jenom za čas, který skutečně využijete ke spuštění migrace dat do Azure.  
- Azure Data Factory může provádět jednorázové historické zatížení i plánovaná přírůstková zatížení.
- Azure Data Factory používá prostředí Azure Integration runtime (IR) k přesouvání dat mezi veřejně přístupnými koncovými body datového Lake a skladu. Může taky používat místní prostředí IR pro přesun dat pro koncové body Data Lake a Warehouse v Azure Virtual Network (VNet) nebo za bránou firewall.
- Azure Data Factory má zabezpečení na podnikové úrovni: pro zabezpečenou integraci služeb a služeb můžete použít Instalační služba systému Windows (MSI) nebo identitu služby, případně můžete použít Azure Key Vault pro správu přihlašovacích údajů.
- Azure Data Factory poskytuje prostředí pro vytváření obsahu bez kódu a bohatě integrovaný řídicí panel pro monitorování.  

## <a name="online-vs-offline-data-migration"></a>Online vs. migrace offline dat

Azure Data Factory je standardní nástroj pro migraci dat online pro přenos dat přes síť (Internet, ER nebo VPN). Vzhledem k tomu, že při migraci offline dat uživatelé fyzicky dodávají zařízení pro přenos dat ze své organizace do datového centra Azure.  

Existují tři klíčové důležité důvody, pokud si vyberete mezi online a offline migrací:  

- Velikost dat, která se mají migrovat
- Šířka pásma sítě
- Okno migrace

Předpokládejme například, že plánujete použít Azure Data Factory k dokončení migrace dat do dvou týdnů ( *okna migrace*). Všimněte si růžového nebo modrého vyjmutého řádku v následující tabulce. Nejnižší růžová buňka pro libovolný daný sloupec zobrazuje párování velikosti dat a šířky pásma sítě, jejichž okno migrace je nejblíže, ale méně než dva týdny. (Jakékoli párování velikosti a šířky pásma v modré buňce má okno online migrace delší než dva týdny.) 

![online vs. ](media/data-migration-guidance-overview/online-offline.png) Tato tabulka vám pomůže určit, jestli můžete požadované okno migrace splnit prostřednictvím online migrace (Azure Data Factory), a to na základě velikosti vašich dat a dostupné šířky pásma sítě. Pokud je okno online migrace delší než dva týdny, budete chtít použít offline migraci.

> [!NOTE]
> Při použití online migrace můžete pomocí jediného nástroje dosáhnout historických a koncových informačních kanálů od začátku do konce.  Prostřednictvím tohoto přístupu se vaše data dají během celého okna migrace synchronizovat mezi existujícím a novým úložištěm. To znamená, že můžete logiku ETL znovu sestavit v novém úložišti s aktualizovanými daty.


## <a name="next-steps"></a>Další kroky

- [Migrace dat z AWS S3 do Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrace dat z místního clusteru Hadoop do Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrace dat z místního serveru Netezza do Azure](data-migration-guidance-netezza-azure-sqldw.md)
