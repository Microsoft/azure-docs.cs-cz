---
title: Úrovně výkonu úložiště objektů blob bloku – Azure Storage
description: Popisuje rozdíl mezi úrovněmi výkonu premium a standardní pro úložiště objektů blob bloku Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270222"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Úrovně výkonu pro úložiště objektů blob bloku

Vzhledem k tomu, že podniky nasazují cloudové nativní aplikace citlivé na výkon, je důležité mít možnosti pro nákladově efektivní ukládání dat na různých úrovních výkonu.

Úložiště objektů blob bloků Azure nabízí dvě různé úrovně výkonu:

- **Premium:** optimalizováno pro vysoké transakční sazby a jednocifernou konzistentní latenci úložiště
- **Standard:** optimalizováno pro vysokou kapacitu a vysokou propustnost

Následující aspekty platí pro různé úrovně výkonu:

| Oblast |Standardní výkon  |Prémiový výkon  |
|---------|---------|---------|
|Dostupnost v oblastech     |   Všechny regiony      | Ve [vybraných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Podporované [typy účtů úložiště](../common/storage-account-overview.md#types-of-storage-accounts)     |     Obecné použití v2, BlobStorage, Všeobecné použití v1    |    Úložiště BlockBlobStorage     |
|Podporuje [objekty BLOB bloku s vysokou propustností.](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Ano, při velikosti větší než 4 MiB PutBlock nebo PutBlob     |    Ano, při velikosti větší než 256 KiB PutBlock nebo PutBlob    |
|Redundance     |     Viz [Typy účtů úložiště](../common/storage-account-overview.md#types-of-storage-accounts)   |  V současné době podporuje pouze místně redundantní úložiště (LRS) a zónově redudant ové úložiště (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1.</sup> Zónově redundantní úložiště (ZRS) je k dispozici ve vybraných oblastech pro účty úložiště objektů blob s prémiovými výkonnostními bloky.</div>

Pokud jde o náklady, špičkový výkon poskytuje optimalizované ceny pro aplikace s vysokými transakčními sazbami, které pomáhají [snížit celkové náklady na úložiště](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) pro tyto úlohy.

## <a name="premium-performance"></a>Prémiový výkon

Úložiště objektů blob premium performance block zpřístupňuje data prostřednictvím vysoce výkonného hardwaru. Data jsou uložena na ssd discích (SSD), které jsou optimalizovány pro nízkou latenci. SSD disky poskytují vyšší propustnost ve srovnání s tradičními pevnými disky.

Úložiště s výkonem Premium je ideální pro úlohy, které vyžadují rychlé a konzistentní doby odezvy. Je to nejlepší pro úlohy, které provádějí mnoho malých transakcí. Příklady úloh zahrnují:

- **Interaktivní úlohy**. Tyto úlohy vyžadují okamžité aktualizace a zpětnou vazbu od uživatelů, jako je e-commerce a mapovací aplikace. Například v aplikaci elektronického obchodu méně často zobrazené položky pravděpodobně nejsou ukládány do mezipaměti. Musí však být zákazníkovi okamžitě zobrazeny na vyžádání.

- **Analytika**. Ve scénáři IoT může být každou sekundu posunuto do cloudu spousta menších operací zápisu. Velké množství dat může být přijata, agregována pro účely analýzy a poté odstraněna téměř okamžitě. Díky vysokým možnostem ingestování úložiště objektů blob s prémiovými bloky je efektivní pro tento typ úlohy.

- **Umělá inteligence/strojové učení (AI/ML)**. Technologie AI/ML se zabývá spotřebou a zpracováním různých datových typů, jako jsou vizuály, řeč a text. Tento vysoce výkonný výpočetní typ úlohy se zabývá velkým množstvím dat, která vyžadují rychlou odezvu a efektivní časy ingestování pro analýzu dat.

- **Transformace dat**. Procesy, které vyžadují neustálé úpravy, úpravy a převod dat, vyžadují okamžité aktualizace. Pro přesné znázornění dat musí spotřebitelé těchto dat tyto změny okamžitě zobrazit.

## <a name="standard-performance"></a>Standardní výkon

Standardní výkon podporuje různé [úrovně přístupu](storage-blob-storage-tiers.md) pro ukládání dat nákladově nejefektivnějším způsobem. Je optimalizovaný pro vysokou kapacitu a vysokou propustnost u velkých datových sad.

- **Datové sady zálohování a zotavení po havárii**. Standardní úložiště výkonu nabízí nákladově efektivní úrovně, což z něj činí případ dokonalého použití pro krátkodobé i dlouhodobé datové sady zotavení po havárii, sekundární zálohy a archivaci dat dodržování předpisů.

- **Mediální obsah**. Obrázky a videa jsou často přístupné při prvním vytvoření a uložení, ale tento typ obsahu se používá méně často, jak stárne. Standardní úložiště výkonu nabízí vhodné úrovně pro potřeby mediálního obsahu. 

- **Hromadné zpracování dat**. Tyto druhy úloh jsou vhodné pro standardní úložiště, protože vyžadují nákladově efektivní úložiště s vysokou propustností namísto konzistentní nízké latence. Velké, nezpracované datové sady jsou připravené pro zpracování a nakonec migrovat do chladnějších vrstev.

## <a name="migrate-from-standard-to-premium"></a>Migrace ze standardního na prémii

Existující účet úložiště standardního výkonu nelze převést na účet úložiště objektů blob bloku s výkonem úrovně Premium. Chcete-li migrovat do účtu úložiště výkonu výkonu úrovně premium, musíte vytvořit účet BlockBlobStorage a migrovat data do nového účtu. Další informace naleznete [v tématu Vytvoření účtu BlockBlobStorage](storage-blob-create-account-block-blob.md).

Chcete-li kopírovat objekty BLOB mezi účty úložiště, můžete použít nejnovější verzi nástroje příkazového řádku [AzCopy.](../common/storage-use-azcopy-blobs.md) Další nástroje, jako je Azure Data Factory jsou také k dispozici pro přesun dat a transformace.

## <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů blob

Správa životního cyklu úložiště objektů blob nabízí bohaté zásady založené na pravidlech:

- **Premium**: Vyprší platnost dat na konci svého životního cyklu.
- **Standard**: Přechod dat na nejlepší úroveň přístupu a vyprší data na konci svého životního cyklu.

Další informace najdete [v tématu Správa životního cyklu úložiště objektů Blob Azure](storage-lifecycle-management-concepts.md).

Data uložená v účtu úložiště objektů blob s prémiovými bloky nelze přesunout mezi horkou, studenou a archivní úrovní. Můžete však kopírovat objekty BLOB z účtu úložiště objektů blob bloku do úrovně aktivního přístupu v *jiném* účtu. Chcete-li zkopírovat data do jiného účtu, použijte rozhraní [URL API put block from](/rest/api/storageservices/put-block-from-url) nebo [AzCopy v10](../common/storage-use-azcopy-v10.md). **Adresa URL obsazuje blok z** rozhraní URL synchronně kopíruje data na serveru. Volání bude dokončeno až po přesunutí všech dat z původního umístění serveru do cílového umístění.

## <a name="next-steps"></a>Další kroky

Vyhodnoťte horké, studené a archivační účty v účtech úložiště GPv2 a Blob.

- [Další informace o rehydrataci dat objektů blob z archivní vrstvy](storage-blob-rehydration.md)
- [Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)
- [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
- [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
