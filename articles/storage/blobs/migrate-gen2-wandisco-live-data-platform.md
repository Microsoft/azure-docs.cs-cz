---
title: Platforma Data Lake Storage a WANdisco LiveData pro Azure (Preview)
description: Migrujte místní data Hadoop do Azure Data Lake Storage Gen2 s využitím platformy LiveData pro Azure pro WANdisco.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508925"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Splnění požadavků na náročné migrace s platformou WANdisco LiveData pro Azure (Preview)

Migrujte místní data Hadoop do Azure Data Lake Storage Gen2 s využitím [platformy LiveData pro Azure pro WANdisco](https://docs.wandisco.com/live-data-platform/docs/landing/). Tato platforma eliminuje nutnost výpadku aplikací, odstraňuje riziko ztráty dat a zajišťuje konzistenci dat i v případě, že operace pokračují v místním prostředí.  

> [!NOTE]
> Platforma WANdisco LiveData pro Azure je ve verzi Public Preview. Informace o regionální dostupnosti najdete v tématu [podporované oblasti](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

Platforma se skládá ze dvou služeb: [LiveData migrace pro Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) slouží k migraci aktivně používaných dat z místních prostředí do Azure Storage a [LiveData roviny pro Azure](https://www.wandisco.com/products/livedata-plane-for-azure) , která zajišťuje, že všechna upravená data nebo ingesta se replikují konzistentně. 

> [!div class="mx-imgBorder"]
> ![Obrázek přehledu živé datové platformy](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Obě služby můžete spravovat pomocí Azure Portal a rozhraní příkazového řádku Azure CLI a oba sledovat stejný model fakturace s průběžnými platbami jako všechny ostatní služby Azure. Platforma LiveData pro využití Azure se zobrazí na stejné měsíční faktuře Azure a bude poskytovat konzistentní a pohodlný způsob, jak sledovat a monitorovat vaše využití.

Na rozdíl od migrace dat do _režimu offline_ [zkopírováním statických informací do Azure Data box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)nebo pomocí nástrojů Hadoop, jako je [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), můžete během _online_ migrace s WANdisco LiveData pro Azure udržovat úplnou práci s vašimi podnikovými systémy. Udržujte prostředí pro velké objemy dat, a to i při přesunu dat do Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Klíčové funkce platformy WANdisco LiveData pro Azure

[Platforma WANdisco LiveData pro Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) používá jedinečný, široce Škálovatelný modul podporující sítě, který umožňuje dosáhnout konzistence dat a provádění replikace dat ve velkém měřítku, zatímco aplikace můžou dál upravovat data při replikaci. <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

Mezi klíčové funkce platformy patří následující:

- **Konzistence dat**: řešení problémů s migrací velkých objemů dat mezi prostředími a udržování těchto dat v rámci migrace propustnosti úložných systémů, i když dojde k nepřetržité změně. Využijte jedinečný a vysoce plošný WANdiscoou síť, která je schopná využít přímo v Azure, aby bylo možné zajistit konzistenci dat a migrovat data s zárukami konzistence v průběhu změn dat.

- **Údržba operací**: aplikace můžou během migrace dál vytvářet, upravovat, číst a odstraňovat data, takže nemusíte narušovat provozní operace ani nastavovat okno výpadků, abyste mohli migrovat velké objemy dat do Azure. Pokračujte v práci s aplikacemi, analytickou infrastrukturou, úlohami ingest a dalším zpracováním.

- **Ověřit výsledky**: ucelené ověření, že vaše data je možné efektivně použít po migraci do Azure, vyžaduje, abyste pro ně spustili úlohy produkčních aplikací. Pouze služba LiveData poskytuje tuto skutečnost bez toho, aby se zavedlo riziko nekonzistence dat, a to tak, že zachovává konzistenci dat bez ohledu na to, zda ke změně dojde na zdroji nebo cíli migrace Otestujte a ověřte chování aplikace bez rizika nebo změny v procesech a systémech.

- **Snížení složitosti**: eliminace nutnosti vytvářet a spravovat naplánované úlohy pro kopírování dat migrací dat prostřednictvím automatizace. Využijte hloubkovou integraci s Azure jako řídicí plochu pro správu a monitorování průběhu migrace, včetně replikace selektivních dat, metadat podregistru, zabezpečení dat a důvěrnosti.

- **Efektivita**: Udržujte vysokou propustnost a výkon a snadno Škálujte na velké objemy dat. Díky řízení spotřeby šířky pásma můžete zajistit splnění cílů migrace, aniž by to mělo vliv na jiné systémové operace.

## <a name="migrate-big-data-faster-without-risk"></a>Rychlejší migrace velkých objemů dat bez rizika

První služba platformy WANdisco LiveData pro Azure je LiveData – [migrace pro Azure](https://www.wandisco.com/products/livedata-migrator-for-azure); řešení pro migraci aktivně používaných dat z místních prostředí do služby Azure Storage. LiveData migrace pro Azure je zajištěná a spravovaná výhradně z Azure Portal nebo Azure CLI a funguje souběžně s vaším clusterem Hadoop bez jakékoli změny konfigurace, úprav aplikací nebo restartování služby za účelem okamžité migrace dat.

> [!div class="mx-imgBorder"]
> ![LiveData migrace pro architekturu Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Migrace velkých objemů dat může být složitá a náročná. Přesunutí petabajty informací, aniž by došlo k narušení obchodních operací, nebylo možné dosáhnout pomocí offline technologií pro kopírování dat. [LiveData Migration for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) nabízí jednoduché nasazení a může vytvořit službu LiveData s nepřetržitou migrací dat a replikací, zatímco aplikace čtou, zapisují a mění migrované data.

Provedení migrace je jednoduché jako u těchto tří kroků:

1. Zřiďte instanci LiveData migrace z Azure Portal do místního clusteru Hadoop. Nepotřebujete žádnou změnu clusteru ani výpadky a aplikace můžou dál fungovat.

   > [!div class="mx-imgBorder"]
   >![Vytvoření instance LiveData migrace](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Definujte cílový účet úložiště s povolenou Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   >![Vytvořit cíl migrace LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Zadejte umístění dat, která chcete migrovat, například: `/user/hive/warehouse` a spusťte migraci.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření migrace migračního LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Monitorujte svůj průběh migrace prostřednictvím standardních nástrojů Azure, včetně Azure CLI a Azure Portal a pokračujte v používání místního prostředí. Než začnete, zkontrolujte tyto [požadavky](https://docs.wandisco.com/live-data-platform/docs/prereq/).

## <a name="replicate-data-under-active-change"></a>Replikovat data v rámci aktivní změny

Rozsáhlé migrace místních datových laků do Azure vyžaduje testování a ověřování aplikací. To se dá dělat bez rizika zavedení změn dat, které vytvoří více zdrojů pravdy, které nepůjde snadno sjednotit, je zásadní pro vyloučení rizik a minimalizaci nákladů na přesun do Azure. [LiveData rovina pro Azure](https://www.wandisco.com/products/livedata-plane-for-azure) používá k překonání těchto otázek technologii WANdisco (koordinačního modulu).

> [!div class="mx-imgBorder"]
> ![Rovina LiveData pro architekturu Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Po počáteční migraci zachovejte vaše data konzistentní napříč místními clustery Hadoop a Azure Storage s rovinou LiveData pro Azure:

1. Zřídí LiveDataou rovinu pro místní Azure a v Azure počínaje od Azure Portal. Nevyžadují se žádné změny aplikací.

2. Nakonfigurujte pravidla replikace, která se vztahují na umístění dat, která chcete uchovat konzistentně, například: `/user/contoso/sales/region/WA` .

3. Spouštějte aplikace, které přistupují k datům v libovolném umístění a mění je jako systém souborů kompatibilní se systémem Hadoop, jak potřebujete.

LiveData rovina pro Azure udržuje vaše data konzistentní, aniž by musela významně zvýšit nároky na provoz clusteru nebo výkon aplikace. Upravovat nebo ingestovat data v případě, že se všechny změny replikují konzistentně

## <a name="next-steps"></a>Další kroky

- [Platforma LiveData pro Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) pro Azure se používá jako jakýkoli jiný prostředek Azure a teď je dostupná ve verzi Preview. 

- Seznamte se s [požadavky](https://docs.wandisco.com/live-data-platform/docs/prereq/), naplánujte migraci a rychle dokončete migraci ve velkém měřítku pomocí LiveData migrace pro Azure.

- Vyzkoušejte LiveData migrační, aniž byste museli mít místní cluster Hadoop s použitím [izolovaného prostoru (sandbox)](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Viz také

- [LiveData migrace pro Azure na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Rovina LiveData pro Azure v Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData migrace pro plány a ceny Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData rovina pro plány a ceny Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData platforma pro nejčastější dotazy k Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Známé problémy s platformou LiveData pro Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Úvod do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)