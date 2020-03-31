---
title: Migrace dat z datového jezera a datového skladu do Azure
description: Azure Data Factory slouží k migraci dat z datového jezera a datového skladu do Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930806"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Migrace dat z datového jezera nebo datového skladu do Azure pomocí Azure Data Factory

Pokud chcete migrovat datové jezero nebo podnikový datový sklad (EDW) do Microsoft Azure, zvažte použití Azure Data Factory. Azure Data Factory se dobře hodí pro následující scénáře:

- Migrace úloh velkých objemů dat ze služby Amazon Simple Storage Service (Amazon S3) nebo místního distribuovaného souborového systému Hadoop (HDFS) do Azure
- Migrace EDW z aplikací Oracle Exadata, Netezza, Teradata nebo Amazon Redshift do Azure

Azure Data Factory můžete přesunout petabajty (PB) dat pro migraci datového jezera a desítky terabajtů (TB) dat pro migraci datového skladu.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Proč se Azure Data Factory dá použít pro migraci dat

- Azure Data Factory můžete snadno vertikálně navýšit kapacitu výpočetního výkonu pro přesun dat bez serveru s vysokým výkonem, odolností a škálovatelností. A platíte jen za to, co používáte. Všimněte si také následující: 
  - Azure Data Factory nemá žádná omezení na objem dat nebo na počet souborů.
  - Azure Data Factory může plně využít šířku pásma sítě a úložiště k dosažení nejvyššího objemu propustnosti pohybu dat ve vašem prostředí.
  - Azure Data Factory používá metodu průběžných plateb, takže platíte jenom za dobu, kterou skutečně použijete ke spuštění migrace dat do Azure.  
- Azure Data Factory můžete provádět jednorázové historické zatížení a naplánované přírůstkové zatížení.
- Azure Data Factory používá runtime integrace Azure (IR) k přesunu dat mezi veřejně přístupnými datovými jezery a koncovými body skladu. Může také použít samoobslužné infračervené ovládání pro přesun dat pro koncové body datových jezer a skladů uvnitř virtuální sítě Azure (VNet) nebo za bránou firewall.
- Azure Data Factory má zabezpečení na podnikové úrovni: Můžete použít Instalační službu Windows (MSI) nebo Identitu služby pro integraci zabezpečené služby ke službě nebo použít Azure Key Vault pro správu přihlašovacích údajů.
- Azure Data Factory poskytuje prostředí pro vytváření bez kódu a bohatý integrovaný řídicí panel monitorování.  

## <a name="online-vs-offline-data-migration"></a>Migrace dat online vs. offline

Azure Data Factory je standardní online nástroj pro migraci dat pro přenos dat přes síť (internet, ER nebo VPN). Vzhledem k tomu, že při migraci dat offline uživatelé fyzicky doručují zařízení pro přenos dat ze své organizace do datového centra Azure.  

Při výběru mezi přechodem migrace online a offline existují tři klíčové aspekty:  

- Velikost migrovaných dat
- Šířka pásma sítě
- Okno migrace

Předpokládejme například, že plánujete použít Azure Data Factory k dokončení migrace dat do dvou týdnů *(okno migrace).* Všimněte si růžovo-modré řezané čáry v následující tabulce. Nejnižší růžová buňka pro daný sloupec zobrazuje párování šířky pásma dat/šířky pásma sítě, jehož okno migrace je nejblíže, ale méně než dva týdny. (Jakékoli párování velikosti a šířky pásma v modré buňce má okno migrace online více než dva týdny.) 

![online vs offline](media/data-migration-guidance-overview/online-offline.png) Tato tabulka vám pomůže určit, jestli můžete splnit zamýšlené okno migrace prostřednictvím migrace online (Azure Data Factory) na základě velikosti dat a dostupné šířky pásma sítě. Pokud je okno migrace online delší než dva týdny, budete chtít použít offline migraci.

> [!NOTE]
> Pomocí migrace online můžete dosáhnout načítání historických dat i přírůstkových kanálů od konce ke konci prostřednictvím jediného nástroje.  Prostřednictvím tohoto přístupu mohou být data synchronizována mezi existujícím úložištěm a novým úložištěm během celého okna migrace. To znamená, že můžete znovu sestavit logiku ETL v novém úložišti s aktualizovanými daty.


## <a name="next-steps"></a>Další kroky

- [Migrace dat z AWS S3 do Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrace dat z místního clusteru hadoop do Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrace dat z místního serveru Netezza do Azure](data-migration-guidance-netezza-azure-sqldw.md)
